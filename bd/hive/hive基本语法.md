* 建表语句  
```
CREATE [EXTERNAL] TABLE [IF NOT EXISTS] table_name 
     [(col_name data_type [COMMENT col_comment], ...)] 
     [COMMENT table_comment] 
     [PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)] 
     [CLUSTERED BY (col_name, col_name, ...) 
     [SORTED BY (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS] 
     [ROW FORMAT row_format] 
     [STORED AS file_format] 
     [LOCATION hdfs_path]
```    
> * EXTERNAL 关键字可以让用户创建一个外部表，在建表的同时指定一个指向实际数据的路径（LOCATION）
> * LIKE 允许用户复制现有的表结构，但是不复制数据
> * COMMENT可以为表与字段增加描述
> * STORED AS  
    SEQUENCEFILE  
    | TEXTFILE  
    | RCFILE  
    | INPUTFORMAT input_format_classname OUTPUTFORMAT output_format_classname  
    如果文件数据是纯文本，可以使用 STORED AS TEXTFILE。如果数据需要压缩，使用 STORED AS SEQUENCE 。
* 删除表  
```
DROP TABLE table_name
```
* 修改表
```
查看表结构 DESC table_name;
 
新增表字段 ALTER TABLE table_name ADD COLUMNS (columns_1 STRING,columns_2 STRING); 
 
修改表名称 ALTER TABLE table_name RENAME TO table_name1;
```
* 导入外部文件数据  
```
加载数据到表中
  LOAD DATA LOCAL INPATH '/home/hadoop/data/data.txt' INTO TABLE table_name;
 
加载hdfs中的文件：  
  LOAD DATA INPATH '/user/hive/data.txt' INTO TABLE table_name;
 
复制表数据:  
  INSERT OVERWRITE TABLE table_name1 select * from table_name2;
```
* 分区操作
```
分区表创建  
  CREATE TABLE table_name(
  ...
  )
  PARTITION BY (opt_mon STRING,opt_day STRING);
  
分区表添加分区
  alter table table_name add partition (opt_mon='201907',opt_day='20190706'); 
 
分区中添加数据
  insert overwrite table table_name1 
  partition(opt_mon='201907',opt_day='20190706') 
  select * from table_name2 where opt_mon='201907' and opt_day='20190706';
  
多分区插入数据
  from table_name1
  insert overwrite table table_name2 partition(opt_mon='201907',opt_day='20190706') select * from table_name1 where opt_mon='201907',opt_day='20190706'
  insert overwrite table table_name2 partition(opt_mon='201907',opt_day='20190707') select * from table_name1 where opt_mon='201907',opt_day='20190707'
  insert overwrite table table_name2 partition(opt_mon='201907',opt_day='20190708') select * from table_name1 where opt_mon='201907',opt_day='20190708';  
  
动态分区
前提：set hive.exec.dynamic.partition=true;  
     set hive.exec.dynamic.partition.mode=nonstrict;   
需要设置以上两个参数
insert overwrite table table_name1 partition(opt_mon='201907',opt_day)
select *,opt_day from table_name2 where opt_mon='201907'; 
opt_mon 作为静态分区列 opt_day 作为动态分区列    
```
**动态分区不允许主分区采用动态列而副分区采用静态列，这样将导致所有的主分区都要创建副分区静态列所定义的分区**


* ...



















