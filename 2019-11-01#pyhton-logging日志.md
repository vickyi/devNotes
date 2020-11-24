# 2019-11-01#pyhton-logging日志

```python
#_*_coding:utf-8_*_

import time
import sys, getopt
from datetime import datetime, timedelta
# from logger import logger
import pymysql
import configparser
import logging
from logging.handlers import RotatingFileHandler,TimedRotatingFileHandler

logger = logging.getLogger('logger')
logger.setLevel(logging.DEBUG)

DATE_FORMAT = "%Y-%M-%d_%H:%M:%S_%a,"
LOG_FORMAT = "%(asctime)s [%(levelname)s]: %(filename)s-[line:%(lineno)d] %(message)s"
formatter = logging.Formatter(LOG_FORMAT, datefmt=DATE_FORMAT)

# logging.basicConfig(level=logging.DEBUG,
#                 format=LOG_FORMAT,
#                 datefmt=DATE_FORMAT,
#                 filename='myapp.log',
#                 filemode='a')

#定义一个StreamHandler，将INFO级别或更高的日志信息打印到标准错误，并将其添加到当前的日志处理对象#
console = logging.StreamHandler()
console.setLevel(logging.DEBUG)
console.setFormatter(formatter)

# #定义一个 TimedRotatingFileHandler，
Rthandler = TimedRotatingFileHandler('log_gateways.log', when='D', interval=1, backupCount=5)
# RotatingFileHandler，按照文件大小切割日志文件，最多备份5个日志文件，每个日志文件最大10M
# Rthandler = RotatingFileHandler('log_gateways.log', maxBytes=10*1024*1024, backupCount=5)
Rthandler.setLevel(logging.DEBUG)
Rthandler.setFormatter(formatter)

logger.addHandler(console)
logger.addHandler(Rthandler)

'''
从网关 zabbix 监控数据库读取数据，然后写到 rpt 库
'''

def query_gateway_call_info(gateways, start_datetime, end_datetime):

    try:
        host = "255.255.255.0"
        user = "name"
        pwd = "123"
        db = "test"
        conn = get_mysql_conn(host, user, pwd, db)
        columns = []
        trunk_codes = []

        with conn.cursor() as cursor:
            for trunk_code, ip in gateways.items():
                trunk_codes.append(trunk_code)
                logger.info(
                    ">>>>>>>>开始获取网关数据: trunk_code: {}, ip: {}, 网关数据时间查询条件: {}~{}".format(
                        trunk_code,
                        ip,
                        start_datetime,
                        end_datetime))

                rows = query_gateway_log(trunk_code, ip, cursor, start_datetime, end_datetime)

                if len(rows) == 0:
                    logger.info(">>>>>>>>获取网关数据为--空: trunk_code: {}, ip: {}".format(trunk_code, ip))
                    now = datetime.now()
                    x_minutes_ago = now + timedelta(minutes=-10)
                    start_datetime = x_minutes_ago.strftime("%Y-%m-%d %H:%M:00")
                    logger.info(">>>>>>>>开始获取网关数据: trunk_code: {}, ip: {}, 网关数据时间查询条件: {}~{}".format(
                        trunk_code,
                        ip,
                        start_datetime,
                        end_datetime))
                    rows = query_gateway_log(trunk_code, ip, cursor, start_datetime, end_datetime)

                if rows:
                    for row in rows:
                        print(row)
                        columns.append(row)
                else:
                    logger.info(">>>>>>>>重试后，获取网关数据依旧--空: trunk_code: {}, ip: {}, 网关数据时间查询条件: {}~{}".format(
                        trunk_code, ip, start_datetime, end_datetime))

        # 关闭游标
        cursor.close()
        # 关闭连接
        conn.close()

        if len(columns) > 0:
            insert_trunk_monitor(columns, trunk_codes, start_datetime, end_datetime)
        else:
            logger.info(">>>>>>>>全部为空: 获取网关数据全都为空: 网关数据时间查询条件: {}~{}".format(start_datetime, end_datetime))
    except Exception as e:
        logger.error(">>>>>>>>网关数据查询异常，sql参数: trunk_code: {}, ip: {}。异常信息: {}".format(trunk_code, ip, str(e)))


def clean_trunk_codes_data(cursor, trunk_codes, start_datetime, end_datetime):
    del_sql = "delete from rpt.rpt_trunk_monitor where trunk_code in {} and data_time >= '{}' and data_time < '{}'"
    sql = del_sql.format(tuple(trunk_codes), start_datetime, end_datetime)
    print(sql)
    cursor.execute(sql)


def insert_trunk_monitor(columns, trunk_codes, start_datetime, end_datetime):
    # 存放秒级打点数据
    # trunk_line_type 1:vos,2:网关
    sql = "insert into rpt.rpt_trunk_monitor (`trunk_code`,`data_cnt`,`data_date`,`data_time`) values (%s, %s, %s, %s)"

    try:
        host = "test.com"
        user = "name"
        pwd = "123"
        db = "rpt"
        conn = get_mysql_conn(host, user, pwd, db)
        cursor = conn.cursor()
        clean_trunk_codes_data(cursor, trunk_codes, start_datetime, end_datetime)
        conn.commit()
        cursor.executemany(sql, columns)
        conn.commit()
        # 关闭游标
        cursor.close()
        # 关闭连接
        conn.close()
    except Exception as e:
        logger.error(str(e))


def query_gateway_log(trunk_code, ip, cursor, start_datetime, end_datetime):

    query_gateway_call = """
    select
        trunk_code,
        sum(data_cnt) as data_cnt,
        data_date,
        data_time
    from (
        select
            '{}' as trunk_code,
            a.itemid,
            cc.hostid,
            substr(from_unixtime(a.clock),1,10) as data_date,
            concat(substr(from_unixtime(a.clock),1,16),":00") as data_time,
            max(a.value) as data_cnt
        from zabbix.history_uint a
        join (
            select bb.itemid,a.hostid,a.ip
            from zabbix.interface a
            join (
                select itemid, hostid
                from zabbix.items
                where name = '正在被使用的pstn通道'
                group by itemid, hostid
                ) bb on bb.hostid = a.hostid
                where ip in ({})
            group by itemid,a.hostid,a.ip
            ) cc on a.itemid = cc.itemid
        where from_unixtime(a.clock, '%Y-%m-%d %H:%i:00') >= '{}'
        and from_unixtime(a.clock, '%Y-%m-%d %H:%i:00') < '{}'
        group by
            a.itemid,
            cc.hostid,
            substr(from_unixtime(a.clock),1,10),
            concat(substr(from_unixtime(a.clock),1,16),":00")
    )tt
    group by
        trunk_code,
        data_date,
        data_time
    """

    sql = query_gateway_call.format(trunk_code, ip, start_datetime, end_datetime)
    cursor.execute(sql)
    rows = cursor.fetchall()
    return rows


def get_mysql_conn(host, user, pwd, db):
    connection = pymysql.connect(host=host,
                                port=3306,
                                user=user,
                                password=pwd,
                                db=db,
                                charset='utf8')
    return connection


def get_gateways():
    config = configparser.ConfigParser()
    config.read('gateways.conf')

    gateways = {}

    for key in config['monitor.config.gateways']:
        gateways[key] = config['monitor.config.gateways'][key]

    return gateways


def usage():
    print("Usage: ")
    print("python gateway_data_flush.py --start_datetime '2019-08-06 00:00:00' --end_datetime '2019-08-07 00:00:00'")
    print("\n\n")


def main(argv):
    try:
        opts, args = getopt.getopt(argv, "m:p:f:t:h", ["help", "method=", "project=", "flow=", "datetime=", "start_datetime=", "end_datetime="])
    except getopt.GetoptError:
        print("请输入正确的参数，--help 查看命令使用")
        sys.exit(2)

    now = datetime.now()
    five_minutes_ago = now + timedelta(minutes=-5)
    start_datetime = five_minutes_ago.strftime("%Y-%m-%d %H:%M:00")
    end_datetime = now.strftime("%Y-%m-%d %H:%M:00")

    for opt, arg in opts:
        if opt == '-h':
            usage()
            sys.exit()
        elif opt in ("-m", "--method"):
            method = arg
        elif opt in ("-p", "--project"):
            project = arg
        elif opt in ("-f", "--flow"):
            flow = arg
        elif opt in ("--start_datetime"):
            start_datetime = arg
        elif opt in ("--end_datetime"):
            end_datetime = arg
        elif opt in ("-h", "--help"):
            usage()
        else:
            logger.error("unhandled option")
            assert False, "unhandled option"

    if len(start_datetime) == 19 and len(end_datetime) == 19:
        gateways = get_gateways()
        # query_gateway_call_info(gateways, start_datetime, end_datetime)
    else:
        usage()


if __name__ == '__main__':
    # 打开数据库连接
    t0 = time.time()
    main(sys.argv[1:])
    msg = '>>>>>>>>gateway_data handle in {:.2f} seconds.'
    logger.info(msg.format(time.time() - t0))

```