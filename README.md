# Big Data Practical Commands — Complete Extract (Markdown Version)

## PRACTICAL 1 — HDFS COMMANDS
```
hdfs dfs -mkdir /user/cloudera/data
hdfs dfs -touchz /user/cloudera/data/file1.txt
echo "this is my content" | hdfs dfs -appendToFile - /user/cloudera/data/file1.txt
hdfs dfs -put localfile.txt /user/cloudera/
hdfs dfs -copyFromLocal localfile.txt /user/cloudera/
hdfs dfs -get /user/cloudera/file1.txt ./
hdfs dfs -copyToLocal /user/cloudera/file1.txt ./
hdfs dfs -moveFromLocal localfile.txt /user/cloudera/
hdfs dfs -cp /user/cloudera/file1.txt /user/hadoop/
hdfs dfs -mv /user/cloudera/file1.txt /user/hadoop/
hdfs dfs -rm /user/cloudera/file1.txt
hdfs dfs -rm -r /user/cloudera/data/
hdfs dfs -du /user/cloudera/
hdfs dfs -dus /user/cloudera/
hdfs dfs -stat /user/cloudera/file1.txt
```

### Execution Commands
```
hdfs dfs -mkdir /user/cloudera/Kapil
hdfs dfs -touchz /user/cloudera/Kapil/file1.txt
echo "Kapil Lad C24050" | hdfs dfs -appendToFile - /user/cloudera/Kapil/file1.txt
hdfs dfs -copyFromLocal file2 /user/cloudera/Kapil
hdfs dfs -copyToLocal /user/cloudera/Kapil/file1.txt ./
hdfs dfs -moveFromLocal file3 /user/cloudera/Kapil
hdfs dfs -cp /user/cloudera/Kapil/file1.txt /user/cloudera/joey
hdfs dfs -mkdir /user/cloudera/union_input
hdfs dfs -put file1.txt /user/cloudera/union_input/
hdfs dfs -put file2.txt /user/cloudera/union_input/
```

## PRACTICAL 2 — MAPREDUCE COMMANDS
```
hdfs dfs -ls
cat myInputFile.txt
hdfs dfs -put prac.jar /user/cloudera
hdfs dfs -put myInputFile.txt /user/cloudera
hadoop jar prac.jar WordCount /user/cloudera/myInputFile.txt /user/cloudera/myOutput
hdfs dfs -ls /user/cloudera/myOutput
hdfs dfs -cat /user/cloudera/myOutput/part-r-00000
```

WordCount
```java
import java.io.IOException;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

public class WordMapper extends Mapper<LongWritable, Text, Text, IntWritable> {
    private final static IntWritable one = new IntWritable(1);
    private Text wordText = new Text();

    @Override
    public void map(LongWritable key, Text value, Context context) 
            throws IOException, InterruptedException {
        String line = value.toString();
        for (String word : line.split("\\W+")) {
            if (word.length() > 0) {
                wordText.set(word);
                context.write(wordText, one);
            }
        }
    }
}
```

SumReducer
```java
import java.io.IOException;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

public class SumReducer extends Reducer<Text, IntWritable, Text, IntWritable> {
    private IntWritable result = new IntWritable();

    @Override
    public void reduce(Text key, Iterable<IntWritable> values, Context context) 
            throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable v : values) {
            sum += v.get();
        }
        result.set(sum);
        context.write(key, result);
    }
}
```

WordCountDriver
```java
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class WordCountDriver {
    public static void main(String[] args) throws Exception {
        if (args.length != 2) {
            System.err.println("Usage: WordCountDriver <input path> <output path>");
            System.exit(-1);
        }
        Configuration conf = new Configuration();
        Job job = Job.getInstance(conf, "Word Count");
        job.setJarByClass(WordCountDriver.class);
        job.setMapperClass(WordMapper.class);
        job.setReducerClass(SumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```
UnionIntersection
```
hdfs dfs -mkdir /user/cloudera/union_input
hdfs dfs -put file1.txt /user/cloudera/union_input/
hdfs dfs -put file2.txt /user/cloudera/union_input/
```
Union
```java
import java.io.IOException;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class Union {

    public static class UnionMapper extends Mapper<Object, Text, Text, Text> {
        private static final Text empty = new Text("");
        private Text line = new Text();

        @Override
        public void map(Object key, Text value, Context context)
                throws IOException, InterruptedException {
            line.set(value);
            context.write(line, empty);
        }
    }

    public static class UnionReducer extends Reducer<Text, Text, Text, Text> {
        @Override
        public void reduce(Text key, Iterable<Text> values, Context context)
                throws IOException, InterruptedException {
            // Write each unique key once
            context.write(key, null);
        }
    }

    public static void main(String[] args) throws Exception {
        if (args.length < 2) {
            System.err.println("Usage: Union <input path> <output path>");
            System.exit(-1);
        }
        Configuration conf = new Configuration();
        Job job = Job.getInstance(conf, "Union Operation");
        job.setJarByClass(Union.class);
        job.setMapperClass(UnionMapper.class);
        job.setReducerClass(UnionReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(Text.class);
        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```
Intersect
```java
import java.io.IOException;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class Intersection {

    public static class IntersectionMapper extends Mapper<Object, Text, Text, IntWritable> {
        private final static IntWritable one = new IntWritable(1);
        private Text lineText = new Text();

        @Override
        public void map(Object key, Text value, Context context)
                throws IOException, InterruptedException {
            lineText.set(value);
            context.write(lineText, one);
        }
    }

    public static class IntersectionReducer extends Reducer<Text, IntWritable, Text, Text> {
        @Override
        public void reduce(Text key, Iterable<IntWritable> values, Context context)
                throws IOException, InterruptedException {
            int count = 0;
            for (IntWritable v : values) {
                count += v.get();
            }
            // If an item appears in at least two input files, it's in the intersection
            if (count >= 2) {
                context.write(key, null);
            }
        }
    }

    public static void main(String[] args) throws Exception {
        if (args.length < 2) {
            System.err.println("Usage: Intersection <input path> <output path>");
            System.exit(-1);
        }
        Configuration conf = new Configuration();
        // Optionally run locally for testing:
        // conf.set("fs.defaultFS", "file:///");
        // conf.set("mapreduce.framework.name", "local");
        Job job = Job.getInstance(conf, "Intersection Operation");
        job.setJarByClass(Intersection.class);
        job.setMapperClass(IntersectionMapper.class);
        job.setReducerClass(IntersectionReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```
Matrix
```
hdfs dfs -ls
hdfs dfs -mkdir /user/cloudera/matrixInput
hdfs dfs -put matrix1.txt /user/cloudera/matrixInput/
hdfs dfs -put matrix2.txt /user/cloudera/matrixInput/
hadoop jar MatrixMultiplication.jar MatrixMultiplicationDriver matrixInput matrixOutput
hdfs dfs -ls /user/cloudera/matrixOutput
hdfs dfs -cat /user/cloudera/matrixOutput/part-r-00000
```
MatrixMultiplicationMapper
```java
import java.io.IOException;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

public class MatrixMultiplicationMapper extends Mapper<LongWritable, Text, Text, Text> {
    @Override
    public void map(LongWritable key, Text value, Context context)
            throws IOException, InterruptedException {
        Configuration conf = context.getConfiguration();
        int m = Integer.parseInt(conf.get("m", "1000"));
        int p = Integer.parseInt(conf.get("p", "1000"));

        // Input line format expected: Tag,i,j,value
        // Tag is "M" or "N"
        String line = value.toString().trim();
        if (line.length() == 0) return;

        String[] parts = line.split(",");
        if (parts.length < 4) return;

        String tag = parts[0].trim();   // "M" or "N"
        String iStr = parts[1].trim();
        String jStr = parts[2].trim();
        String valStr = parts[3].trim();

        Text outputKey = new Text();
        Text outputValue = new Text();

        if (tag.equals("M")) {
            // M: (i, j, M_ij)
            // emit (i,k) as key for all k in [0, p)
            for (int k = 0; k < p; k++) {
                outputKey.set(iStr + "," + k);
                outputValue.set("M," + jStr + "," + valStr);
                context.write(outputKey, outputValue);
            }
        } else if (tag.equals("N")) {
            // N: (j, k, N_jk) or PDF uses (N, j, k, value)
            // emit (i,k) for all i in [0, m)
            for (int i = 0; i < m; i++) {
                outputKey.set(i + "," + jStr); // here jStr is k in some conventions; keep PDF style
                outputValue.set("N," + iStr + "," + valStr);
                context.write(outputKey, outputValue);
            }
        }
    }
}
```
MatrixMultiplicationReducer
```java
import java.io.IOException;
import java.util.HashMap;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

public class MatrixMultiplicationReducer extends Reducer<Text, Text, Text, Text> {

    @Override
    public void reduce(Text key, Iterable<Text> values, Context context)
            throws IOException, InterruptedException {
        // key is "i,k"
        // values are strings like "M,j,value" or "N,j,value" depending on mapper output
        HashMap<Integer, Double> mMap = new HashMap<>();
        HashMap<Integer, Double> nMap = new HashMap<>();

        for (Text val : values) {
            String s = val.toString();
            String[] parts = s.split(",");
            if (parts.length < 3) continue;
            String tag = parts[0];
            int index = Integer.parseInt(parts[1]);
            double v = Double.parseDouble(parts[2]);

            if (tag.equals("M")) {
                // M,j,value
                mMap.put(index, v);
            } else if (tag.equals("N")) {
                // N,j,value
                nMap.put(index, v);
            }
        }

        // compute dot product over j
        double sum = 0.0;
        // iterate over keys in mMap (or nMap) and multiply if present in both
        for (Integer j : mMap.keySet()) {
            double mv = mMap.get(j);
            double nv = nMap.containsKey(j) ? nMap.get(j) : 0.0;
            sum += mv * nv;
        }

        // output only if non-zero (optional)
        if (sum != 0.0) {
            context.write(key, new Text(Double.toString(sum)));
        }
    }
}
```
MatrixMultiplicationDriver
```java
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.TextInputFormat;
import org.apache.hadoop.mapreduce.lib.output.TextOutputFormat;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class MatrixMultiplicationDriver {
    public static void main(String[] args) throws Exception {
        if (args.length != 2) {
            System.err.println("Usage: MatrixMultiply <in_dir> <out_dir>");
            System.exit(2);
        }
        Configuration conf = new Configuration();
        // set matrix dimensions (adjust appropriately)
        conf.set("m", "1000"); // number of rows in M
        conf.set("n", "100");  // common dimension
        conf.set("p", "1000"); // number of columns in N

        Job job = Job.getInstance(conf, "MatrixMultiply");
        job.setJarByClass(MatrixMultiplicationDriver.class);
        job.setMapperClass(MatrixMultiplicationMapper.class);
        job.setReducerClass(MatrixMultiplicationReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(Text.class);
        job.setInputFormatClass(TextInputFormat.class);
        job.setOutputFormatClass(TextOutputFormat.class);

        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```


## PRACTICAL 3 — MONGODB COMMANDS
```
use College
db.student.insertOne({id:50, name:"Kapil Lad", course:"MCA"})
db.student.insertMany([{...}, {...}, ...])
db.employees.insertOne({...})
db.employees.insertMany([...])
db.employees.find().pretty()
db.employees.find({name:"Kapil Lad"})
db.employees.find({salary:{$gt:60000}})
db.employees.find().sort({salary:1})
db.employees.deleteOne({emp_id:102})
db.employees.deleteMany({dept_name:"Sales"})
db.employees.deleteMany({})
db.employees.createIndex({name:1})
db.employees.createIndex({name:1, dept_name:1})
db.employees.getIndexes()
```

## PRACTICAL 4 — HIVE COMMANDS
```
create database if not exists userdb;
create schema userdb;
show databases;
create table if not exists employee(eid int, name string, salary string, destination string)
row format delimited fields terminated by ',';
create table student(id int, name string, age int, institute string)
partitioned by (course string)
row format delimited fields terminated by ',';
load data inpath '/mumbai/student.csv' into table student partition(course="java");
load data inpath '/mumbai/student1.csv' into table student partition(course="hadoop");
select * from student;
select * from student where course="java";
select * from student where course="hadoop";
select * from empl;
select name, salary+50 from empl;
select * from empl where salary >= 6850;
select name, sqrt(salary) from empl;
select min(salary) from empl;
select id, upper(name) from empl;
select * from empl order by salary desc;
select name from empl where id = 1;
select course, sum(age) from student group by course having sum(age)>=23;
create table employeel(empid int, empname string , state string)
row format delimited fields terminated by ',';
load data local inpath '/home/cloudera/Desktop/joins1.csv' into table employeel;
create table employee_department(depid int, department string)
row format delimited fields terminated by ',';
load data local inpath '/home/cloudera/Desktop/joins2.csv' into table employee_department;
SET hive.auto.convert.join=false;
```

## PRACTICAL 5 — PIG
(No commands in the PDF)

## PRACTICAL 6 — SPARK COMMANDS
```
spark-shell
val data = sc.parallelize(List(...))
val rdd = sc.textFile("path")
rdd.map(...)
rdd.filter(...)
rdd.reduceByKey(...)
rdd.groupByKey()
rdd.mapValues(...)
rdd.sortByKey()
rdd.join(otherRDD)
rdd.cogroup(otherRDD)
rdd.aggregateByKey(...)
rdd.foldByKey(...)
rdd.collect()
rdd.saveAsTextFile("hdfs/path")
```
