# scala JDBC

```scala
package com.abcplus

import java.sql.{Connection, DriverManager, Timestamp}
import java.util.Calendar


/**
  * Created by gz on 2017/6/28.
  */
object MySqlConn {

  // for test env
  val mysqlConfTest = collection.mutable.Map(
    "driver" -> "com.mysql.jdbc.Driver",
    "url" -> "jdbc:mysql://hostname:3306/rpt",
    "username" -> "test",
    "password" -> "test"
  )

  // for prod env
  val mysqlConfProd = collection.mutable.Map(
    "driver" -> "com.mysql.jdbc.Driver",
    "url" -> "jdbc:mysql://hostname:3306/rpt?autoReconnect=true",
    "username" -> "xxxx",
    "password" -> "xxxxx"
  )

  /**
    * 创建mysql连接
    * @return
    */
  def getMysqlConn(): Connection = {
    Class.forName(mysqlConfTest("driver"))
    DriverManager.getConnection(mysqlConfTest("url"), mysqlConfTest("username"), mysqlConfTest("password"))
  }

  /**
    * 查询所有漏斗
    * @return
    */
  def getFunnels(dateStr: String): collection.mutable.Map[Int, (String, Int, Int ,String ,String)] = {
    // connect to the database named "mysql" on the localhost
    val conn = getMysqlConn

    val funnels = collection.mutable.Map[Int, (String, Int, Int ,String ,String)]()

    try {
      // create the statement, and run the select query
      val statement = conn.createStatement()
      val sql = s"""select id,funnel_name,status,done,start_time,end_time
                  |from rpt_funnel_manage
                  |where status = 1
                  |and (done in (0,3) or (done = 2 and '$dateStr' <= end_time and '$dateStr' >= start_time))
                  |order by id""".stripMargin
      println(sql)
      val resultSet = statement.executeQuery(sql)
      while (resultSet.next()) {
        val fid = resultSet.getInt("id")
        val funnelName = resultSet.getString("funnel_name")
        val status = resultSet.getInt("status")
        val done = resultSet.getInt("done")
        val startTime = resultSet.getString("start_time")
        val endTime = resultSet.getString("end_time")

        funnels += (fid -> (funnelName, status, done, startTime, endTime))
      }
    } catch {
      case e:Throwable => {
        println("FunnelGO failed! Coursed by getFunnels error")
        e.printStackTrace()
        System.exit(1)
      }
    } finally {
      conn.close
    }
    funnels
  }

  def getFunnelLevels(funnelId: Int): collection.mutable.Map[Int, List[Int]] = {
    // connect to the database named "mysql" on the localhost
    val conn = getMysqlConn

    val funnel = collection.mutable.Map[Int, List[Int]]()

    try {
      // create the statement, and run the select query
      val statement = conn.createStatement()
      val sql = s"select * from rpt_funnel_manage_level where fid = $funnelId order by level desc"
      val resultSet = statement.executeQuery(sql)
      while (resultSet.next()) {
        val fid = resultSet.getInt("fid")
        val level = resultSet.getInt("level")
        val page_id = resultSet.getInt("page_id")
        val pageIds = List(page_id)

        // 实际调用的是contains(key)
        if(funnel.isDefinedAt(level)){
          val pages = funnel(level)
          // List 多种姿势
          funnel += (level -> pages.++(pageIds))
        } else {
          funnel += (level -> pageIds)
        }
        println("id, funnel_name, page_id = " + fid + ", " + level+ ", " + page_id)
      }
    } catch {
      case e:Throwable => e.printStackTrace()
    } finally {
      conn.close
    }
    funnel
  }

  /**
    * 更新运行状态至rpt_funnel_manage表的done字段
    * 0 未执行 1 执行中 2 执行完成 3 sql执行失败 4 dump执行失败
    * @param funnelId
    * @param runningStatus
    */
  def updateRunningStatus(funnelId: Int, runningStatus: Int): Unit = {
    // create database connection
    val conn = getMysqlConn

    try {
      val ps = conn.prepareStatement("UPDATE rpt_funnel_manage SET done = ? WHERE id = ?")

      // set the preparedstatement parameters
      ps.setInt(1, runningStatus)
      ps.setInt(2, funnelId)

      // call executeUpdate to execute our sql update statement
      val res = ps.executeUpdate()
      ps.close()

      if(res > 0) println(s"UPDATE rpt_funnel_manage SET done = $runningStatus WHERE id = $funnelId success!")
      else println(s"UPDATE rpt_funnel_manage SET done = $runningStatus WHERE id = $funnelId failed!")
    } catch {
      case e:Throwable => e.printStackTrace()
    } finally {
      conn.close
    }
  }

  /**
    * 将sql写入mysql表，同时将sql的运行状态done字段更新为1：运行中
    * @param funnelId
    * @param sqlString
    */
  def updateSql(funnelId: Int, sqlString: String): Unit = {
    // create database connection
    val conn = getMysqlConn

    try {
      val ps = conn.prepareStatement("UPDATE rpt_funnel_manage SET sqlstring = ?,done = 1 WHERE id = ?")

      // set the preparedstatement parameters
      ps.setString(1, sqlString)
      ps.setInt(2, funnelId)

      // call executeUpdate to execute our sql update statement
      val res = ps.executeUpdate()
      ps.close()

      if(res > 0) println(s"UPDATE rpt_funnel_manage SET sqlstring WHERE id = $funnelId success!")
      else println(s"UPDATE rpt_funnel_manage SET sqlstring WHERE id = $funnelId failed!")
    } catch {
      case e:Throwable => e.printStackTrace()
    } finally {
      conn.close
    }
  }

  private def deleteById(conn: Connection, funnelId: Int): Int = {
    var status = 0
    try {
      val sql = s"delete from rpt_funnel_sql where funnelId = $funnelId"
      System.out.println("sql=" + sql)
      val st = conn.createStatement()
      val result = st.executeUpdate(sql)

      //处理结果
      if (result > 0) println(s"delete from rpt_funnel_sql where funnelId = $funnelId success!")
      else println(s"delete from rpt_funnel_sql where funnelId = $funnelId failed!")
      status = result
    } catch {
      case e:Throwable => e.printStackTrace()
    } finally {
      conn.close
    }
    status
  }

  /**
    * 根据 funnelId 删除记录
    * @param funnelId
    */
  def deleteById(funnelId: Int): Int = {
    val connection = getMysqlConn
    val st = connection.createStatement
    var status = 0
    // do database insert
    try {
      val sql = s"delete from rpt_funnel_sql where funnelId = $funnelId"
      System.out.println("sql=" + sql)
      val result = st.executeUpdate(sql)

      //处理结果
      if (result > 0) println("操作成功") else println("操作失败")
      status = result
    } catch {
      case e:Throwable => e.printStackTrace()
    } finally {
      connection.close
    }
    status
  }

  /**
    * 日期
    * @return
    */
  private def getCurrentTimeStamp(): Timestamp = {
    val timeInMillis = Calendar.getInstance.getTimeInMillis
    new Timestamp(timeInMillis)
  }

  def main(args: Array[String]): Unit = {
    val funnelId = args(0).toInt
    MySqlConn.updateRunningStatus(funnelId,0)
  }
}
```