p132 grouping

### Chapter 5 基本结构化操作

#### 5.1 概念

数据框（DataFrame）: 一系列记录（表中的行），数据类型为**行**（Row）与多个列(Columns)，数据集（Dataset）中的单个记录。DataFrame是元素均为Row的DataSet。

模式（Schemas）：来自数据库系统的概念，定义数据每一列的名字和类型。可以自己定义也可以直接导入数据，由数据决定。

分区（Partitioning）：数据框的分区定义了数据框的布局以及数据集在集群上的物理分布。

分区模式（Partitioning Schema）：定义了数据分布的方式。

结构类型（StructType）：由几个域（fields）组成，结构域（StructFields）包含了数据列名、数据类型和是否存在空值信息（name,type,nullable）。

转换（Spark transformation）：修改数据框的计划，由于lazy机制，需要先设置数据的转换计划再一起执行。有narrow transforamtion和wide transformation等多种方式。

e.g. :memo:增删行列、行列转换、根据列值进行行排序

表达式（Expressions）：列（Columns）也是表达式；表达式是对数据框上一个或多个记录进行的一系列变换。spark对一系列变换进行逻辑分词，优化逻辑计划（logical plan）之后再生成物理计划进行执行。

#### 5.2 函数

##### 载入

df = spark.read.format("-csv/json-").load("-dir-")  读入数据

可使用.option("header","true")

##### 模式

df.printSchema() 打印df的数据类型（模式）

myManualSchema = StructType([StructField("xxx",StringType(),True),...])手动设置模式

df.columns 获取列名

myDf = spark.createDataFrame([myRow], myManualSchema) 利用手动生成的行与模式生成数据框

##### 表达式

expr("") 输入表达式交给spark生成计算逻辑

##### 行

df.first() 获取第一行

myRow = Row() 初始化一行数据

myRow[i] 行内第i个数据

case::video_camera:多行数据生成

```python
newRows = [Row(), Row()...] 生成多行数据

parallelizedRows = spark.sparkContext.parallelize(newRows) 多行数据排列

newDF = spark.createDataFrame(parallelizedRows, schema)
```

df.union(newDF).where()... 数据框df与数据框newDF合并，使用where筛选

df.filter(col("count") < 2) 或者 df.where("count < 2").show()过滤行记录，多个筛选条件可以分别加入，spark会在最后统一执行，因此多个AND关系的纳排条件存在时可以连续使用.where。

df.select().distinct().count() 计数不重复的记录

df.randomSplit([portion1, portion2...], seed) 随机分开样本，根据比例（portion）划分，保存列表。

case::video_camera: 随机抽样

```python
seed = 5 # 随机种子
withReplacement = False # 是否替换
fraction = 0.5 # 抽样比例
df.sample(withReplacement, fraction, seed).count() #抽样函数
```

df.take(N) 取出N行

##### 列

df.withColumn("列名",lit(1)) 插入一列，.lit()函数可以加入一列数字1。

e.g. :memo:

```python
df.withColumn("withinCountry", expr("ORIGIN_COUNTRY_NAME == DEST_COUNTRY_NAME")).show(2)
```

df.withColumnRenamed("列名1","列名2").columns 修改列名

df.drop("").columns 删除列

df.withColumn("count2", col("count").cast("long")) 使用cast进行类型转换

##### 表

df.createOrReplaceTempView("tableName") 将dataframe转换为与sparkSession同寿命的表，用于查询

df.select("").show() SQL表查询语句，select中可以写“列名”或者expr("列名")或者col("列名")或者column("列名")都是等价的。

SELECT... FROM... LIMIT... sql查询语句

e.g. :memo:

```python
df.select(expr("DEST_COUNTRY_NAME AS destination")).show(2)
```

等价于

```sql
SELECT DEST_COUNTRY_NAME AS destination FROM dfTable LIMIT 2
```

等价于在pyspark中使用.alias("别名")

等价于

```python
df.selectExpr("DEST_COUNTRY_NAME as newColumnName", "DEST_COUNTRY_NAME").show(2)
```

df.selectExpr("表达式1","表达式2"...) 表达式可以为聚合函数，

也可以用来给列改名字。当列名有空格以及其他特殊符号时，使用``引用。

e.g.:memo:

```python
df.selectExpr("avg(count)", "count(distinct(DEST_COUNTRY_NAME))").show(2)
```

df.orderBy() 可以填入expr或者col().desc()

df.sortWithinPartitions("") 在分区内排序

df.limit() 显示特定有限数量的记录

##### 分区

df.rdd.getNumPartitions() 返回分区数量

df.repartition() 重新分配分区，可以选定分区数量、选定列名从而根据列进行重分区

df.collect() 将数据收集到驱动，将RDD数据类型转化为数组存放，一次collect一次shuffle，数据在本地运行

df.toLocalIterator() 逐个分区地将数据收集到驱动



#### 5.3 pyspark库

from pyspark.sql.types import StructField, StructType, StringType, LongType 手动设置模式

from pyspark.sql import Row 从pyspark载入Row

set spark.sql.caseSensitive true 设置sql是否区分大小写，默认不区分

from pyspark.sql.functions import desc, asc 载入排序工具

### Chapter 6 使用不同类型的数据

#### 6.1 概念

布尔型（Boolean）：Spark会将布尔型数据的执行逻辑在最后一起执行，因此可以将and逻辑操作串联在一起。

结构体（Struct）：数据框中的数据框。

#### 6.2 函数

##### 数据类型

lit() 将数据转化为spark类型

e.g.:memo:filter定义

```python
priceFilter = col("xxx") > 600
# instr 返回N以后字符串s2中第一次出现s1的索引
descripFilter = instr(df.Description, "POSTAGE") >= 1
# filter定义后可以在后面直接用逻辑关系连接
```

##### 字符处理

instr('s1', 's2', N) 返回N以后字符串s2中第一次出现s1的索引

.isin() 存在某字符，返回布尔值

col("xx").eqNullSafe() 对一整列查看是否全为空，对空值安全的equal运算

initcap() 将所有空格分开的首字符变为大写

upper() 将字符变为大写

lower() 将字符变为小写

ltrim() rtrim() 左右去掉空格

lpad(string, num, " ") rpad() 在string左/右添加num个" "

##### 正则表达式

regexp_replace(col(""), regex_string, " ") 在col列中匹配regex_string，替换为“ ”

translate(column, string1, string2) string1与string2是对应的，translate将列中的string1内对应字符转为string2中对应。类似于字典翻译

regexp_extract(column, extract_string, 1) 返回column中匹配项，1表示返回第一个匹配，0表示返回所有匹配

locate(string1, column, pos) 确定column中是否存在string1，不存在时返回0

##### 时间类型

current_date() 返回当前日期

current_timestamp() 返回当前

date_sub(col(""), 1) 当前日期减去1天

date_add(col(""), 1) 当前日期加上1天

datediff(date1, date2) date1与date2时间差

to_date(lit("2021-01-01"), "yyyy-dd-MM") 将字符串转化为日期格式，符合SimpleDateFormat标准，也可以修改第二个参数

to_timestamp() 字符串转化为时间戳

months_between() 月份差

##### 数值类型

pow(num, exp) 指数运算，num为底数，exp为次数

e.g.:memo:数值运算示例

```python
from pyspark.sql.functions import expr, pow fabricatedQuantity = pow(col("Quantity") * col("UnitPrice"), 2) + 5 df.select(expr("CustomerId"), fabricatedQuantity.alias("realQuantity")).show(2)

#等价于

df.selectExpr( "CustomerId", "(POWER((Quantity * UnitPrice), 2.0) + 5) as realQuantity").show(2)
```

round(num, bits) num四舍五入近似的值，bits保留位数

bround(num, bits) 向下近似

corr(a,b) a,b值的皮尔森系数

df.describe()

:notebook:df.stat 方法内后置许多工具

> df.stat.approxQuantile(column, [portion], prob) 计算分位数 列名，分位比例，容错率
>
> df.stat.crosstab(column1, column2) 两列之间交叉表
>
> df.stat.freqItems([column1, column2]) 两列之间频数统计

monotonically_increasing_id() 给每一行加入递增的独一id

##### 空值处理

coalesce() 返回第一个空值

ifnull() 如果第一个为空值则选择第二个数值

nullif() 如果两个值相等返回空值，不相等返回第二个元素

nvl() 默认返回第一个值，第一个值为空时返回第二个值

nvl2() 如果第一个不是空值，返回第二个值；否则返回最后一个确定的值

df.na.drop() "any"参数：去掉所有含有空值的行，“all”参数：去掉值全部为空的行，加入第二个参数（列名）来删除特定列。

df.na.fill() 使用传入的参数填充所有空值，可以使用字典填充

e.g.:memo:空值填充

```python
fill_cols_vals = {"StockCode": 5, "Description" : "No Value"} df.na.fill(fill_cols_vals)
```

df.na.replace([""], ["UNKNOWN"],"Description") 在Description列利用“UNKNOWN”替换“”

asc_nulls_first, desc_nulls_first, asc_nulls_last, desc_nulls_last按不同规则为dataframe排列

##### 复杂类型

###### 结构体（Struct）

complexDF = df.select(struct("Description", "InvoiceNo").alias("complex")) 结构体声明创建

complexDF.select("complex.*") 

###### 数组（Array）

split(column, delimiter) 按照分隔符划分字符串

size() 返回数组的长度

array_contains(column, string) 数组中是否包含string

explode(column) 针对由数组组成的一列，将数组中每一个元素分配出来展开，分别与其他列组合如下面的Description

e.g.:memo:explode函数使用示例

```python
df.withColumn("splitted", split(col("Description"), " ")).withColumn("exploded", explode(col("splitted"))).select("Description", "InvoiceNo", "exploded").show(20)
+--------------------+---------+--------+
|         Description|InvoiceNo|exploded|
+--------------------+---------+--------+
|WHITE HANGING HEA...|   536365|   WHITE|
|WHITE HANGING HEA...|   536365| HANGING|
|WHITE HANGING HEA...|   536365|   HEART|
|WHITE HANGING HEA...|   536365| T-LIGHT|
|WHITE HANGING HEA...|   536365|  HOLDER|
| WHITE METAL LANTERN|   536365|   WHITE|
| WHITE METAL LANTERN|   536365|   METAL|
| WHITE METAL LANTERN|   536365| LANTERN|
|CREAM CUPID HEART...|   536365|   CREAM|
|CREAM CUPID HEART...|   536365|   CUPID|
|CREAM CUPID HEART...|   536365|  HEARTS|
|CREAM CUPID HEART...|   536365|    COAT|
|CREAM CUPID HEART...|   536365|  HANGER|
|KNITTED UNION FLA...|   536365| KNITTED|
|KNITTED UNION FLA...|   536365|   UNION|
|KNITTED UNION FLA...|   536365|    FLAG|
|KNITTED UNION FLA...|   536365|     HOT|
|KNITTED UNION FLA...|   536365|   WATER|
|KNITTED UNION FLA...|   536365|  BOTTLE|
|RED WOOLLY HOTTIE...|   536365|     RED|
+--------------------+---------+--------+
only showing top 20 rows
```

###### 映射（Maps）

create_map(column1, column2) 创建列之间的键值对

##### JSON类型

get_json_object() 查询一个json对象

json_tuple() 如果嵌套只有一层可以使用

to_json() 可以将结构体类型转换为json字符串

from_json() 将json字符串分词转换为结构体

##### 自定义函数

自定义的函数（user-difined function）需要注册，才能在所有工作机器上对列、sql使用；Spark会序列化这一函数到驱动上并且通过网路传输到所有的执行处理器。Java或Scala可以直接使用JVM，Python在序列化函数后还需要启动Python进程在每个机器中每一行输入数据，并由python一行行返回结果到JVM和Spark。

JVM和Python会争夺工作内存资源，真正消耗资源比较多的地方在于将数据序列化进入Python进程的过程，计算量也很大。因此有worker崩溃的风险。所以自定义函数最好使用Java或者Scala书写。

udf()函数将自定义函数注册到spark，之后可以在dataframe中使用；

spark.udf.register("udfName", udf, DataType())

为了规范和方便，最好规定返回值的类型，python与静态类型语言java,scala交互最好需要有的习惯。输入错误的DataType()不会报错而会返回null。

注册后的函数在不同语言之间可以跨语言使用。

令函数在Hive中暂时（TEMPORARY）使用需要声明一个依赖：

```sql
CREATE TEMPORARY FUNCTION myFunc AS 'com.organization.hive.udf.FunctionName'
```

#### 6.3 pyspark库

from pyspark.sql.functions import lit, round, bround 导入上下近似函数

from pyspark.sql.functions import count, mean, stddev_pop, min, max 描述性统计参数

from pyspark.sql.functions import translate, regexp_replace 正则表达式工具

from pyspark.sql.functions import current_date, current_timestamp, datediff, months_between, to_date 时间工具

from pyspark.sql.functions import split, explode 数组工具

### Chapter 7 聚合

#### 7.1 概念

分组（Grouping）：简单的分组就是使用聚合函数总结数据框。

- group by：选定特定的一个或者多个列进行分组
- window：对一个或多个数值列使用一个或多个聚合函数
- grouping set：在不同水平上聚合，SQL中的primative，在DataFrame中为rollup和cubes。
- rollup：垂直总结多个数值列的聚合
- cube：在所有组合的行间总结

#### 7.2 函数

##### 聚合函数

count() 输出记录数

countDistinct() 输出不重复的记录数

approx_count_distinct() 处理大数据的精准度可以降低以提高速度

first(), last() 数据框中第一个、最后一个记录

min(), max() 最大值、最小值

sum() 求和

sumDistinct() 对不重复记录求和

avg() 返回平均值

var_pop(), stddev_pop 总体的方差与标准差

var_samp(), stddev_samp() 样本的方差与标准差

skewness() 偏度

kurtosis() 峰度

corr() 皮尔森系数

covar_pop(), covar_samp() 总体的协方差和样本的协方差

collect_set(), collect_list() 将某列聚合成一个集或者一个列表

agg() 聚合两个元素

##### 分组函数





