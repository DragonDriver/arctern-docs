# 文件的导入与导出

Spark支持多种数据格式文件导入，以下是针对csv、parquet、json、orc等数据格式文件的导入导出例子。详细信息请查看[Spark官方文档](https://spark.apache.org/docs/latest/sql-data-sources-load-save-functions.html)

```python
#CSV文件导入，导出为PARQUET格式
>>> from pyspark.sql import SparkSession
>>> from arctern_pyspark import register_funcs
>>> spark_session = SparkSession.builder.appName("Python Arrow-in-Spark example").getOrCreate()
>>> spark_session.conf.set("spark.sql.execution.arrow.pyspark.enabled", "true")
>>> register_funcs(spark_session)
>>> df = spark_session.read.format('csv').options(header='true',sep='|').load("/path/to/geos.csv")
>>> df.show(100,0)
+---------------------------------------------+
|geos                                         |
+---------------------------------------------+
|POINT (30 10)                                |
|POLYGON ((30 10, 40 40, 20 40, 10 20, 30 10))|
|POLYGON ((1 2, 3 4, 5 6, 1 2))               |
|POLYGON ((1 1, 3 1, 3 3, 1 3, 1 1))          |
+---------------------------------------------+
>>> df.createOrReplaceTempView("simple")
>>> spark_session.sql("select ST_IsSimple(ST_GeomFromText(geos)) from simple").show(100,0)
+----------------------------------+                                            
|ST_IsSimple(ST_GeomFromText(geos))|
+----------------------------------+
|true                              |
|true                              |
|false                             |
|true                              |
+----------------------------------+
>>> df.select("geos").write.save("/path/to/geos.parquet", format="parquet")


#PARQUET文件导入，导出为JSON格式
>>> df = spark_session.read.format('parquet').options(header='true',sep='|').load("/path/to/geos.parquet")
>>> df.show(100,0)
+---------------------------------------------+
|geos                                         |
+---------------------------------------------+
|POINT (30 10)                                |
|POLYGON ((30 10, 40 40, 20 40, 10 20, 30 10))|
|POLYGON ((1 2, 3 4, 5 6, 1 2))               |
|POLYGON ((1 1, 3 1, 3 3, 1 3, 1 1))          |
+---------------------------------------------+
>>> df.createOrReplaceTempView("simple")
>>> spark_session.sql("select ST_IsSimple(ST_GeomFromText(geos)) from simple").show(100,0)
+----------------------------------+                                            
|ST_IsSimple(ST_GeomFromText(geos))|
+----------------------------------+
|true                              |
|true                              |
|false                             |
|true                              |
+----------------------------------+
>>> df.select("geos").write.save("/path/to/geos.json", format="json")


# JSON文件导入，导出为ORC格式
>>> df = spark_session.read.format('json').options(header='true',sep='|').load("/path/to/geos.json")
>>> df.show(100,0)
+---------------------------------------------+
|geos                                         |
+---------------------------------------------+
|POINT (30 10)                                |
|POLYGON ((30 10, 40 40, 20 40, 10 20, 30 10))|
|POLYGON ((1 2, 3 4, 5 6, 1 2))               |
|POLYGON ((1 1, 3 1, 3 3, 1 3, 1 1))          |
+---------------------------------------------+
>>> df.createOrReplaceTempView("simple")
>>> spark_session.sql("select ST_IsSimple(ST_GeomFromText(geos)) from simple").show(100,0)
+----------------------------------+                                            
|ST_IsSimple(ST_GeomFromText(geos))|
+----------------------------------+
|true                              |
|true                              |
|false                             |
|true                              |
+----------------------------------+
>>> df.write.save("/path/to/geos.orc", format="orc")


# ORC文件导入
>>> df = spark_session.read.format('orc').options(header='true',sep='|').load("/path/to/geos.orc")
>>> df.show(100,0)
+---------------------------------------------+
|geos                                         |
+---------------------------------------------+
|POINT (30 10)                                |
|POLYGON ((30 10, 40 40, 20 40, 10 20, 30 10))|
|POLYGON ((1 2, 3 4, 5 6, 1 2))               |
|POLYGON ((1 1, 3 1, 3 3, 1 3, 1 1))          |
+---------------------------------------------+
>>> df.createOrReplaceTempView("simple")
>>> spark_session.sql("select ST_IsSimple(ST_GeomFromText(geos)) from simple").show(100,0)
+----------------------------------+                                            
|ST_IsSimple(ST_GeomFromText(geos))|
+----------------------------------+
|true                              |
|true                              |
|false                             |
|true                              |
+----------------------------------+
```