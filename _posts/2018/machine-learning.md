 Machine Learning

# data

## spreadsheet

Think of data as a spreadsheet as a table.

## Matrix

rows: observations, our datadata. columns - features.  Get used to it.

### sparse matrix

matrix who's most rows are zeros

# learning

## types

### statistical

Output = f(input) # => f(inputVariable) or f(inputVector), or f(independent variables) or Y = F(X) // X1,X2,..

### programming

OutputAttributes = Program(InputAttributes) or Program(InputFeatures) or Model = Algorithm(Data)

### parametric

No matter how much data you throw on it, it will still need these parameters like a line `Y = ax + b` (logistic regression, linear discriminant analysis, perceptron)

### non parametric

No matter how much data you throw on it, it will still need these parameters like a line Y = ax + b (logistic regression, linear discriminant analysis, perceptron)

### supervised

You have a teacher he knows the answer, classification, regression

### unsupervised

No teacher, clustering, association

### Semi supervised

Some can be with a teacher

### Classification vs Regression

classification(input) => spam/notspam (categorical)<br />regression(input) => bitcoin price (continous outcome)

## Errors

### error Y=f(x) + e

Y = f(X) + e # => You learn a function!

### Bias Variance

Bias Error (model assumptions), Variance Error, Irreducable Error. Increasing bias error reduce variance, increase variance will decrease bias

### Overfitting

Resampling to estimate model accuracy, Hold back validation dataset, Cross validation.

# map reduce

```bash
grep something | wc -l # => grep is map wc -l is the reduce!
```Based on simple [key, value] pair
Moving computation is cheaper than moving data, our data is big ain't it?

## map

List(input) => List(output) # => like grep

## reduce

List(input) => Output(value) # => like wc -l

# spark

## libraries

### graphx

has a library for computing graph computations (in addition to mlib).

## operations

### transformations

### actions

## data structures

### RDD

#### blind data

### DF

dfs.replication: 1, dfs.namenode.name.dir: /var/data/hadoop/...

#### scheme

Think of it as distributed database table.

#### read json element

```scala
object SparkDFOnlineJson extends App {
 
  override def main(args: Array[String]): Unit = {
 
    val jsonString = scala.io.Source.fromURL("https://min-api.cryptocompare.com/data/histoday?fsym=BTC&tsym=ETH&limit=30&aggregate=1&e=CCCAGG").mkString
 
    val spark = org.apache.spark.sql.SparkSession.builder().appName("someapp").master("local[*]").getOrCreate()
 
    import spark.implicits._
    import org.apache.spark.sql.functions._
    val df = spark.read.json(Seq(jsonString).toDS())
 
    df.show()
 
    df.take(10).foreach(println)
    df.printSchema()
 
    df.select($"Data.close".as("close_price")).show(2) // <-- HERE reading Data.close from the json!
 
    val jsonExplodedDF = df.select($"Aggregated", $"ConversionType", explode($"Data").as("prices")) // <-- HERE reading Data.close from the json!
    jsonExplodedDF.printSchema()
    jsonExplodedDF.select($"Aggregated", $"ConversionType", $"prices".getItem("close")).show(10) // Then getItem instead of explode to objects!!
  }
}
 
// +----------+--------------+--------------------+-----------------+--------+----------+----------+----+
// |Aggregated|ConversionType|                Data|FirstValueInArray|Response|  TimeFrom|    TimeTo|Type|
// +----------+--------------+--------------------+-----------------+--------+----------+----------+----+
// |     false|     [,invert]|[[23.91,25.06,21....|             true| Success|1513209600|1515801600| 100|
// +----------+--------------+--------------------+-----------------+--------+----------+----------+----+
 
// [false,[,invert],WrappedArray([23.91,25.06,21.87,23.39,1513209600,62691.53,1452942.54], [25.87,29.03,23.88,23.91,1513296000,50825.4,1342967.63], [28.11,28.62,24.53,25.87,1513382400,38155.01,1013078.48], [26.72,28.11,25.93,28.11,1513468800,36242.76,979762.25], [24.08,26.86,23.29,26.72,1513555200,46712.69,1186390.62], [21.63,24.41,21.29,24.08,1513641600,65125.17,1449434.45], [20.67,22.29,20.42,21.63,1513728000,64539.45,1372742.27], [19.79,20.94,19.4,20.67,1513814400,61802.62,1244602.57], [20.93,21.98,19.47,19.79,1513900800,80230.91,1656134.49], [20.78,20.97,20.42,20.93,1513987200,42893.35,887428.82], [20.53,20.97,20.36,20.77,1514073600,41294.18,855012.67], [19.18,20.53,18.67,20.53,1514160000,48165.25,929653.57], [20.91,21.55,18.75,19.18,1514246400,46999.33,956924.92], [20.88,21.57,20.45,20.91,1514332800,36759.37,769083.49], [20.04,20.95,19.7,20.88,1514419200,40883.16,828193.82], [19.58,20.25,19.32,20.04,1514505600,43487.34,857520.42], [18.14,19.77,18.09,19.58,1514592000,66161.84,1246949.13], [18.68,19.07,18.05,18.14,1514678400,48718.02,902419.05], [17.76,18.7,17.54,18.67,1514764800,50703.72,910875.63], [17.16,18.94,15.25,17.76,1514851200,96092.61,1574640.02], [16.01,17.68,15.62,17.16,1514937600,75289.68,1266911.61], [16.06,16.59,14.43,16.03,1515024000,80755.25,1258516.2], [17.59,18.29,14.54,16.07,1515110400,104693.19,1682729.53], [17.03,17.91,16.25,17.59,1515196800,58014.94,975679.49], [14.49,17.06,14.47,17.03,1515283200,64620.79,994739.35], [13.2,14.5,12.73,14.49,1515369600,102880.99,1380565.72], [11.18,13.21,10.93,13.2,1515456000,95751.66,1168583.78], [11.95,12.06,10.16,11.18,1515542400,143351.13,1546032.52], [11.66,11.96,10.93,11.95,1515628800,97380.62,1100658.4], [10.96,11.8,10.89,11.66,1515715200,63382.56,710582.11], [10.27,11.12,10.24,10.96,1515801600,58214.24,625184.97]),true,Success,1513209600,1515801600,100]
// root
//  |-- Aggregated: boolean (nullable = true)
//  |-- ConversionType: struct (nullable = true)
//  |    |-- conversionSymbol: string (nullable = true)
//  |    |-- type: string (nullable = true)
//  |-- Data: array (nullable = true)
//  |    |-- element: struct (containsNull = true)
//  |    |    |-- close: double (nullable = true)
//  |    |    |-- high: double (nullable = true)
//  |    |    |-- low: double (nullable = true)
//  |    |    |-- open: double (nullable = true)
//  |    |    |-- time: long (nullable = true)
//  |    |    |-- volumefrom: double (nullable = true)
//  |    |    |-- volumeto: double (nullable = true)
//  |-- FirstValueInArray: boolean (nullable = true)
//  |-- Response: string (nullable = true)
//  |-- TimeFrom: long (nullable = true)
//  |-- TimeTo: long (nullable = true)
//  |-- Type: long (nullable = true)
 
// +--------------------+
// |         close_price|
// +--------------------+
// |[23.91, 25.87, 28...|
// +--------------------+
 
// root
//  |-- Aggregated: boolean (nullable = true)
//  |-- ConversionType: struct (nullable = true)
//  |    |-- conversionSymbol: string (nullable = true)
//  |    |-- type: string (nullable = true)
//  |-- prices: struct (nullable = true)
//  |    |-- close: double (nullable = true)
//  |    |-- high: double (nullable = true)
//  |    |-- low: double (nullable = true)
//  |    |-- open: double (nullable = true)
//  |    |-- time: long (nullable = true)
//  |    |-- volumefrom: double (nullable = true)
//  |    |-- volumeto: double (nullable = true)
 
// +----------+--------------+------------+
// |Aggregated|ConversionType|prices.close|
// +----------+--------------+------------+
// |     false|     [,invert]|       23.91|
// |     false|     [,invert]|       25.87|
// |     false|     [,invert]|       28.11|
// |     false|     [,invert]|       26.72|
// |     false|     [,invert]|       24.08|
// |     false|     [,invert]|       21.63|
// |     false|     [,invert]|       20.67|
// |     false|     [,invert]|       19.79|
// |     false|     [,invert]|       20.93|
// |     false|     [,invert]|       20.78|
// +----------+--------------+------------+
// only showing top 10 rows
 
// jsonString: String = {"Response":"Success","Type":100,"Aggregated":false,"Data":[{"time":1513209600,"high":25.06,"low":21.87,"open":23.39,"volumefrom":62691.53,"volumeto":1452942.54,"close":23.91},{"time":1513296000,"high":29.03,"low":23.88,"open":23.91,"volumefrom":50825.4,"volumeto":1342967.63,"close":25.87},{"time":1513382400,"high":28.62,"low":24.53,"open":25.87,"volumefrom":38155.01,"volumeto":1013078.48,"close":28.11},{"time":1513468800,"high":28.11,"low":25.93,"open":28.11,"volumefrom":36242.76,"volumeto":979762.25,"close":26.72},{"time":1513555200,"high":26.86,"low":23.29,"open":26.72,"volumefrom":46712.69,"volumeto":1186390.62,"close":24.08},{"time":1513641600,"high":24.41,"low":21.29,"open":24.08,"volumefrom":65125.17,"volumeto":1449434.45,"close":21.63},{"time":1513728000,"high":22.29,"low":20.42,"open":21.63,"volumefrom":64539.45,"volumeto":1372742.27,"close":20.67},{"time":1513814400,"high":20.94,"low":19.4,"open":20.67,"volumefrom":61802.62,"volumeto":1244602.57,"close":19.79},{"time":1513900800,"high":21.98,"low":19.47,"open":19.79,"volumefrom":80230.91,"volumeto":1656134.49,"close":20.93},{"time":1513987200,"high":20.97,"low":20.42,"open":20.93,"volumefrom":42893.35,"volumeto":887428.82,"close":20.78},{"time":1514073600,"high":20.97,"low":20.36,"open":20.77,"volumefrom":41294.18,"volumeto":855012.67,"close":20.53},{"time":1514160000,"high":20.53,"low":18.67,"open":20.53,"volumefrom":48165.25,"volumeto":929653.57,"close":19.18},{"time":1514246400,"high":21.55,"low":18.75,"open":19.18,"volumefrom":46999.33,"volumeto":956924.92,"close":20.91},{"time":1514332800,"high":21.57,"low":20.45,"open":20.91,"volumefrom":36759.37,"volumeto":769083.49,"close":20.88},{"time":1514419200,"high":20.95,"low":19.7,"open":20.88,"volumefrom":40883.16,"volumeto":828193.82,"close":20.04},{"time":1514505600,"high":20.25,"low":19.32,"open":20.04,"volumefrom":43487.34,"volumeto":857520.42,"close":19.58},{"time":1514592000,"high":19.77,"low":18.09,"open":19.58,"volumefrom":66161.84,"volumeto":1246949.13,"close":18.14},{"time":1514678400,"high":19.07,"low":18.05,"open":18.14,"volumefrom":48718.02,"volumeto":902419.05,"close":18.68},{"time":1514764800,"high":18.7,"low":17.54,"open":18.67,"volumefrom":50703.72,"volumeto":910875.63,"close":17.76},{"time":1514851200,"high":18.94,"low":15.25,"open":17.76,"volumefrom":96092.61,"volumeto":1574640.02,"close":17.16},{"time":1514937600,"high":17.68,"low":15.62,"open":17.16,"volumefrom":75289.68,"volumeto":1266911.61,"close":16.01},{"time":1515024000,"high":16.59,"low":14.43,"open":16.03,"volumefrom":80755.25,"volumeto":1258516.2,"close":16.06},{"time":1515110400,"high":18.29,"low":14.54,"open":16.07,"volumefrom":104693.19,"volumeto":1682729.53,"close":17.59},{"time":1515196800,"high":17.91,"low":16.25,"open":17.59,"volumefrom":58014.94,"volumeto":975679.49,"close":17.03},{"time":1515283200,"high":17.06,"low":14.47,"open":17.03,"volumefrom":64620.79,"volumeto":994739.35,"close":14.49},{"time":1515369600,"high":14.5,"low":12.73,"open":14.49,"volumefrom":102880.99,"volumeto":1380565.72,"close":13.2},{"time":1515456000,"high":13.21,"low":10.93,"open":13.2,"volumefrom":95751.66,"volumeto":1168583.78,"close":11.18},{"time":1515542400,"high":12.06,"low":10.16,"open":11.18,"volumefrom":143351.13,"volumeto":1546032.52,"close":11.95},{"time":1515628800,"high":11.96,"low":10.93,"open":11.95,"volumefrom":97380.62,"volumeto":1100658.4,"close":11.66},{"time":1515715200,"high":11.8,"low":10.89,"open":11.66,"volumefrom":63382.56,"volumeto":710582.11,"close":10.96},{"time":1515801600,"high":11.12,"low":10.24,"open":10.96,"volumefrom":58214.24,"volumeto":625184.97,"close":10.27}],"TimeTo":1515801600,"TimeFrom":1513209600,"FirstValueInArray":true,"ConversionType":{"type":"invert","conversionSymbol":""}}
// spark: org.apache.spark.sql.SparkSession = org.apache.spark.sql.SparkSession@3fb8bf89
// import spark.implicits._
// import org.apache.spark.sql.functions._
// df: org.apache.spark.sql.DataFrame = [Aggregated: boolean, ConversionType: struct<conversionSymbol: string, type: string> ... 6 more fields]
// jsonExplodedDF: org.apache.spark.sql.DataFrame = [Aggregated: boolean, ConversionType: struct<conversionSymbol: string, type: string> ... 1 more field]

```

## fast

1. Memory
1. Result of mappers goes to shared memory accross the cluster and not to disk
1. In reality hadoop mapreduced optimized with Tez which means it keeps values in mem like spark
1. In reality If spark runs out of memory intermediate results goes to disk.

## run

```bash./bin/pyspark --master local[1] # start spark shell.
./bin/pyspark-submit myprog.py 1 2 just args
./bin/sparkR --master local # => (r spark shell)
```

## hdfs

```scala
val textFile = sc.textFile("hdfs://localhost:9000/user/hdfs/somefile.txt")
txtFile.count
```

# Algorithms

## Gradiant Descent

almost every machine learning algorithm uses optimisation at it's core, optimising the target function.  Local minimum.  start with 0 `coefficient = 0.0`.  `cost = evaluate(f(coefficient))`.  Update coefficient downhill with derivative.  `coefficient = coefficient - (alpha * delta)`.  alpha learning parameter.

### Stochastic Gradiant Descent

Have large amounts of data, update to coefficients is for each training instance, not in batch, as we have random data we move quickly.

# hadoop

## install

in general for hadoop, hive, ping installations you download the tar.gz, set environment variables for its home, and add folders in hdfs if needed.

### from source

https://www.safaribooksonline.com/library/view/hadoop-and-spark/9780134770871/HASF_01_02_02_01.html
extract hadoop tar.gz, make sure JAVA_HOME in path, HADOOP_HOME configured, add yarn, hdfs, mapred users, make directories: /var/data/hadoop/hadfs/[nn,snn], log directory,

### core-site.xml

fs.default.name: hdfs://localhsot:9000 #=> set the hdfs port.

### hdfs-site.xml

hdfs parameters, dfs.replication: 1, dfs. directory...

### format hdfs

```bash
su - hdfs
cd /opt/hadoop-2.8.1/bin
./hdfs namenode -format
```

### start

```bash
cd /opt/hadoop-2.8.1/sbin
./hadoop-daemon.sh start namenode
./hadoop-daemon.sh start secondarynamenode
./hadoop-daemon.sh start datanode
jps # => java processes status the above are all java processes.
```

### create hdfs folders

hdfs dfs -mkdir -p /mr-history/tmp /mr-history/done chown to yarn:hadoop

### start yarn

```bash
su - yarn
./yarn-daemon.sh start resourcemanager
./yarn-daemon.sh start nodemanager
./mr-jobhistory-daemon.sh start historyserver
jps
```

## urls

### hdfs fs

#### http://localhost:50070

hdfs file system

### yarn

#### http://localhost:8088

as a local file system

### jobtracker

#### http://headnode:50030

## run test

### yarn jar somejob.jar args

run a test mr jar with yarn

## hdfs

### roles

#### namenode

like a **traffic cop**, telling us where to find or write data, also handles failures of data nodes, if data node does not report back with status its timeout and namenode will remove it, we see one namespace across the whole data.  Client contacts namenode and then datanode returned from namenode for the actual data.

##### inmemory

stores HDFS metadata in memory at startup reads it fro file `fsimage`. Writes added to logfile on startup it merges the log with fsimage.

##### secondary namenode

###### bad title

####### checkpoint node

better named checkpoint node because it's merging the fsimage to the edits log while the namenode is running so startup will be fast.

##### backup node

same work as checkpoint node but is synchronized to namenode using real time stream from the namenode.  Still no redundancy with this.

#### datanode

#### hdfs-client

##### calls namenode then datanode

you do operations on hdfsClient it's doing all the work of communicating with namenodes and then sending the operations to the correct data nodes.

### commands

#### hdfs dfsadmin -report

#### dfs -put file.txt

hdfs dfs -put war-and-peace.txt

#### dfs -cp file1.txt file2.txt

copy a file inside hdfs

#### mount hdfs /mnt/hdfs

as a local file system!

### programming

#### java

```java
import org.apache.hadoop.fs.FileSystem // just same api as java file system.

Configuration conf = new Configuration();

conf.addResource(new Path("/etc/hadoop/conf/core-site.xml");
conf.addResource(new Path("/etc/hadoop/conf/hdfs-site.xml");

FileSystem fileSystem = FileSystem.get(conf);

fileSystem.exists("/users/tomer/test.txt");

// Create new file and write data to it.
FSDataOutputStream out = fileSystem.create(path);
InputStream in = new BufferedInputStream(new FileInputStream(
  new File(source)));
int numBytes = 0;
while ((numBytes = in.read(b)) > 0) {
  out.write(b, 0, numBytes);
}
```

##### compile

```bash
echo "Main-Class: org/myorg.HDFSClient" > manifest.txt
javac -classpath /usr/lib/hadoop/hadoop-core.jar -d HDSFClient -classes HDFSClient.java # => Note we needed to include hadoop core jar.
jar -cvfe HDFSClient.jar org/myorg.HDFSClient -C HDFSClient-classes/ .
hadoop jar ./HDFSClient.jar add sometextfile.txt /user/tomer # => run with program arguments.
```

##### classpath

export CLASSPATH=$(hadoop classpath)

### HA

#### namenode

##### standby namenode

acting like checkpoint node so it has the fsimage file, it will take over in case of failure.

##### federation

Break namespace across all namespace
namenode1: /research/marketing
namenode2: /data/project

##### snapshots

read onliy point-in-time copies of the file system.  can be of subtree.  it's not data no data copied only block list and file size.  Think of snapshot of a file directory.  can do this on daily basis does not slow things down.

### misc

#### nfsv3

NFS gateway allows you to access hdfs as if it's a local file system, its still not random access but it's convinient.

#### host:5700

web gui for nfs is at http://host:5700

## debug

### /var/log/hadoop

these are the logs on the headnode you can also ssh to worker nodes and similarly look at /var/log/hadoop/mapred you will see there the task tracker logs.

### kill

```bash
hadoop job -list
hadoop job -kill job_2016982347928_0042
```

## map reduce

map => banana,1
             banana, 1
             banana, 1
reduce => banana, 3

### grep | wc -l

`grep "Samuel" somebook.txt | wc -l`
grep => map
wc -l => reduce

### helloworld

mapper: string tokenizer, emit (word, 1), reduce, sum+= values, in addition you write the "driver", going to run the mapper and reducer, you say which class is mapper conf.setMapperClass(MapClass.class); you also do conf.setCombinerClass and setReducerClass.
`hadoop jar wordcount.jar org.myorg.WordCount /user/myuser/inputdir /user/myuser/outputdir`

### shuffle

```markdown
shuffle is the only step where we have communication transfer of data between nodes.
![shuffle](https://www.todaysoftmag.com/images/articles/tsm33/large/a11.png)
```

### reduce

```markdown
can run on multiple hosts, depending on shuffle, shuffle puts same keys on same hosts, so reduce can work on grouping of same keys and he will know he has all the same keys on the same hosts.
```

### combiner

instead of mapper saying i found earth,1 and earth,1 compiner will have the mapper report earth,2 from a certain node, optimizing the mapper so the reducer has less work.

### streaming

```markdown
**Streaming interface for hadoop jobs**
you can write a mapper.py that expects stdin and just run it and amazingly you can also run it on hadoop.  in the java map reduce interface we got line by line, here we get the stdin we can do anything we want. [https://www.safaribooksonline.com/library/view/hadoop-and-spark/9780134770871/HASF_01_05_01.html?autoStart=True](https://www.safaribooksonline.com/library/view/hadoop-and-spark/9780134770871/HASF_01_05_01.html?autoStart=True)
```
Then you run it with:
```bash
/usr/lib/hadoop/contrib/streaming/hadoop-streaming-1.1.2.21.jar -file ./mapper.py -mapeper ./mapper.py -file ./reducer.py -reducer ./reducer.py ...
```

### pipes

```markdown
**Pipes interface to mapreduce**
it's a clean interface to do map reduce.
```

## YARN

does not care that its' map reduce its running could be any job.  the previous job manager and task manager ran only map reduce.  jobTracker manages jobs and taskTracker is on local nodes.

# hive

## install

https://www.safaribooksonline.com/library/view/hadoop-and-spark/9780134770871/HASF_01_02_02_02.html

### derby

hive uses apache derby simple database for metastore, so you need to install it.

# AWS

## considerations

### develop

### deploy

### iteration time

### lower scale

### processing time

## key technologies

### S3

```markdown
bucket name:
1. no underscores has to be a valid hostname for hadoop usage in url
```

#### ACL

### redshift

relational database

### data pipelines

ETL for data for example from S3 into redshift to view results can apply complex series of transformations.  It uses EC2 for the compute power to do the moving of data.

### kinesis

like kafka

### ec2

## resources

https://www.safaribooksonline.com/library/view/learn-how-to/9781491985632/video312545.html

## process

```md
1. use data-pipelines to ingest data (copy from one place maybe from s3 to s3)
2. run machine learning algorithm on ec2 or emr.
```

## ec2

create keypair public/private key in order to be able to connect

## EMR

```markdown
We it's all going through s3 bucket we create there folders for the jar to run for logs for the results and for the input data.


Resources:
1. [https://www.youtube.com/watch?v=cAZur5maWZE&index=3&list=PLB5E99B925DBE79FF](https://www.youtube.com/watch?v=cAZur5maWZE&index=3&list=PLB5E99B925DBE79FF)
```
elastic map reduce

### s3

```markdown
EMR uses S3 for input and output data you need to create buckets to put your jar files and input and output.

1. bucketname/folder for specifying jar to aws console
1. s3n://bucket/path # => for hadoop args
1. s3://bucket/path # for aws cmd line tools.
```

### JobFlow

```markdown
Then create a job flow so that you can create the flow you tell it where your jar file is the jar run arguments.
if you choose keepAlive <- no this means the EMR cluster is stopped once the job fiishes.
```

### Hive

```bash
mybucket/scripts/myhive.hql # => I put there my hive script.
mybucket/data/mydata.csv # => I put there my data

```

### cli

#### create spark cluster

```bash
aws emr create-cluster --name "Spark cluster" --release-label emr-5.13.0 --applications Name=Spark \
--ec2-attributes KeyName=tomer-key-pair --instance-type m4.small --instance-count 2 --use-default-roles
```

##### 

#### list emr clusters

```bash
aws emr list-clusters
```

#### terminate clusters

```bash
aws emr terminate-clusters --cluster-ids="j-W25BXM9TCOGX"
```

## awscli

### install

```bash
pip3 install awscli --upgrade --user
```
```markdown
then add /Users/tomer.bendavid/.local/bin to PATH on bash_profile
```

### configure

```markdown
1. `aws configure`
1. take security credentials from [here](https://console.aws.amazon.com/iam/home?region=us-east-1#/security_credential)
1. for default reigon i entered `us-east-1`
```

# python

## urllib2

### getfile

```python
import urllib.request
url = "http://www.cs.tufts.edu/comp/116/access.log"
accesslog =  urllib.request.urlopen(url).read().decode('utf-8')
print("accesslog: " + accesslog)
```

## matplotlib

## pandas

```python
from pandas import read_csv
```

### data

#### 
pandas.read_csv

```python
url = "https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data"
names = ['sepal-length', 'sepal-width', 'petal-length', 'petal-width', 'class']
dataset = pandas.read_csv(url, names=names) #name is the above name for columns.
```

#### 
dataset.shape

#### 
dataset.head(20)

#### 
dataset.describe()

#### print(dataset.groupby('class').size())

#### pandas.set_option('expand_frame_repr', False)

Don't break table output when printing like with `.head()` to new lines, all in one line, wide table.

### plot

#### dataset.plot(kind='box', subplots=True, layout=(2,2), sharex=False, sharey=False)

#### dataset.hist()

#### scatter_matrix(dataset)

plt.show()

### build model

#### validation dataset

seperate out validation dataset.
80% for data, 20% for validation.

```python
# Split-out validation dataset
array = dataset.values
X = array[:,0:4]
Y = array[:,4]
validation_size = 0.20
seed = 7
X_train, X_validation, Y_train, Y_validation = model_selection.train_test_split(X, Y, test_size=validation_size, random_state=seed)
```

#### cross validation

10 fold cross validation for accuracy.
```python
# Test options and evaluation metric
seed = 7
scoring = 'accuracy'
```

#### build choose models

```markdown
evaluate 6 models:

1. Logistic Regression (LR)
1. Linear Discriminant Analysis (LDA)
1. K-Nearest Neighbors (KNN).
1. Classification and Regression Trees (CART).
1. Gaussian Naive Bayes (NB).
1. Support Vector Machines (SVM).

This is a good mixture of simple linear (LR and LDA), nonlinear (KNN, CART, NB and SVM) algorithms
```

```python
# Spot Check Algorithms
models = []
models.append(('LR', LogisticRegression()))
models.append(('LDA', LinearDiscriminantAnalysis()))
models.append(('KNN', KNeighborsClassifier()))
models.append(('CART', DecisionTreeClassifier()))
models.append(('NB', GaussianNB()))
models.append(('SVM', SVC()))
# evaluate each model in turn
results = []
names = []
for name, model in models:
    kfold = model_selection.KFold(n_splits=10, random_state=seed)
    cv_results = model_selection.cross_val_score(model, X_train, Y_train, cv=kfold, scoring=scoring)
    results.append(cv_results)
    names.append(name)
    msg = "%s: %f (%f)" % (name, cv_results.mean(), cv_results.std())
    print(msg)
```

results:

```bash
LR: 0.966667 (0.040825)
LDA: 0.975000 (0.038188)
KNN: 0.983333 (0.033333)
CART: 0.975000 (0.038188)
NB: 0.975000 (0.053359)
SVM: 0.981667 (0.025000)
```

plot models comparison:

```python
# Compare Algorithms
fig = plt.figure()
fig.suptitle('Algorithm Comparison')
ax = fig.add_subplot(111)
plt.boxplot(results)
ax.set_xticklabels(names)
plt.show()
```

#### make predictions

```python
# Make predictions on validation dataset
knn = KNeighborsClassifier()
knn.fit(X_train, Y_train)
predictions = knn.predict(X_validation)
print(accuracy_score(Y_validation, predictions))
print(confusion_matrix(Y_validation, predictions))
print(classification_report(Y_validation, predictions))
```

#### errors f1 score

```markdown
We can see that the accuracy is 0.9 or 90%. The confusion matrix provides an indication of the three errors made. Finally, the classification report provides a breakdown of each class by precision, recall, f1-score and support showing excellent results (granted the validation dataset was small).
```

```bash
0.9

[[ 7  0  0]
 [ 0 11  1]
 [ 0  2  9]]

             precision    recall  f1-score   support

Iris-setosa       1.00      1.00      1.00         7
Iris-versicolor   0.85      0.92      0.88        12
Iris-virginica    0.90      0.82      0.86        11

avg / total       0.90      0.90      0.90        30
```

### resources

#### https://machinelearningmastery.com/machine-learning-in-python-step-by-step/

# Amazon

## open data

```markdown
1. [Amazon reviews data set](https://www.kaggle.com/bittlingmayer/amazonreviews)
```
