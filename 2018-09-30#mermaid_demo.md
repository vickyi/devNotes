# mermaid

## flow chart

> 用`<br/>`时，预览是正常的，下载 svg 会报错

### graph TD

```mermaid
graph TD
A[业务表] -->|1. 目标表名: aiplus_业务_表名<br>2. 同步后保留原始表的注释信息<br>3. 数据量>100万级的并且增长快的大表, <br> 建议按照周期创建分区表, 便于定期更新<br>4. 数据量<100万级的按照实际情况建普通表, <br>可以采用周期全量跟新<br>5. 大表数据压缩格式 ORC<br>6.建表需要给表加注释, 注明表owner和主要信息<br>7. ods/dw 层的表在业务表原有字段基础上,<br> 建议多增加一个update_time字段, <br>格式为current_timestamp ,<br>标记数据的更新时间| C(EMR Hive 下的ODS 库)
C --> D{建模: 维表OR事实表}
D -->|维表| E[前缀dim_]
D -->|事实表| F[前缀 fact_]
E --> |报表|G(前缀 rpt_)
F --> |报表|G
```

### graph TD 2

```mermaid
graph TD
A[业务表] -->B(EMR Hive 下的Temp 库<br>1. 目标表名: aiplus_业务_表名_周期<br>2. 同步后保留原始表的注释信息)

B --> C(EMR Hive 下的ODS 库<br>1. 目标表名: aiplus_业务_表名<br>2. 同步后保留原始表的注释信息<br>3. 数据量>100万级的并且增长快的大表, 建议按照周期创建分区表, 便于定期更新<br>4. 数据量<100万级的按照实际情况建普通表, 可以采用周期全量跟新<br>5. 大表数据压缩格式 ORC<br>6.建表需要给表加注释, 注明表owner和主要信息<br>7. ods/dw 层的表在业务表原有字段基础上, 建议多增加一个update_time字段, <br>格式为current_timestamp , 标记数据的更新时间</span>)

C --> D{建模: 维表OR事实表}
D -->|维表| E[前缀dim_]
D -->|事实表| F[前缀 fact_]
E --> |报表|G(前缀 rpt_)
F --> |报表|G
```

### graph LR

```mermaid
graph LR
A[业务表] -->|1. 目标表名: aiplus_业务_表名_周期<br>2. 同步后保留原始表的注释信息| B(EMR Hive 下的Temp 库)
B -->|1. 目标表名: aiplus_业务_表名<br>2. 同步后保留原始表的注释信息<br>3. 数据量>100万级的并且增长快的大表,  建议按照周期创建分区表, 便于定期更新<br>4. 数据量<100万级的按照实际情况建普通表, 可以采用周期全量跟新<br>5. 数据压缩格式 ORC<br>6.建表需要给表加注释, 注明表owner和表的主要信息<br>7. ods/dw 层的表相比业务表原有的字段, 建议多增加一个current_timestamp格式的update_time字段,  标记数据的更新时间 | C(EMR Hive 下的ODS 库)
C --> D{建模: 维表OR事实表}
D -->|维表| E[前缀dim_]
D -->|事实表| F[前缀 fact_]
E --> |报表|G(前缀 rpt_)
F --> |报表|G
```

## sequenceDiagram

```mermaid
sequenceDiagram
业务库 ->> EMR Hive Temp: 周期同步
Note left of EMR Hive Temp: 1. 目标表名: aiplus_业务_表名_周期<br>2. 同步保留原始表的注释信息
EMR Hive Temp ->> EMR Hive DW: 周期同步
Note right of EMR Hive DW: 1. 目标表名: aiplus_业务_表名<br>2. 同步后保留原始表的注释信息<br>3. 数据量>100万级的并且增长快的大表,  建议按照周期创建分区表, 便于定期更新<br>4. 数据量<100万级的按照实际情况建普通表, 可以采用周期全量跟新<br>5. 数据压缩格式 ORC<br>6.建表需要给表加注释, 注明表owner和表的主要信息<br>7. ods/dw 层的表相比业务表原有的字段, 建议多增加一个current_timestamp格式的update_time字段,  标记数据的更新时间
```

### 参考

[mermaid-live-editor](https://mermaidjs.github.io/mermaid-live-editor)