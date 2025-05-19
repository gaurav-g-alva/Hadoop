## 1. Install java
## 2. Setup HADOOP
### Format namenode 
```
hdfs namenode -format
```
### Start all Hadoop Services
```
start-all.sh
```
### Check wether the Services are running properly
```
jps
```
### Set the Mapred Class path to the dfs
```
mapred classpath
```
### copy all the lines and paste it into
```
export CLASSPATH = " PASTE HERE "
```
### Edit the java file
```
gedit  WordCount.java
```
### Copy the following code
```
import java.io.IOException; 
import org.apache.hadoop.conf.Configuration; 
import org.apache.hadoop.fs.Path; 
import org.apache.hadoop.io.IntWritable; 
import org.apache.hadoop.io.LongWritable; 
import org.apache.hadoop.io.Text; 
import org.apache.hadoop.mapreduce.Job; 
import org.apache.hadoop.mapreduce.Mapper; 
import org.apache.hadoop.mapreduce.Reducer; 
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat; 
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat; 
public class WordCount { 
public static class WordCountMapper extends Mapper<LongWritable, Text, Text, IntWritable> { 
private final static IntWritable one = new IntWritable(1); 
private Text word = new Text(); 
public void map(LongWritable key, Text value, Context context) throws IOException, 
InterruptedException { 
String[] words = value.toString().split("\\s+"); 
for (String w : words) { 
w = w.replaceAll("[^a-zA-Z]", "").toLowerCase(); 
if (!w.isEmpty()) { 
word.set(w); 
context.write(word, one); 
} 
} 
} 
} 
public static class WordCountReducer extends Reducer<Text, IntWritable, Text, IntWritable> { 
public void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, 
InterruptedException { 
int sum = 0; 
for (IntWritable val : values) { 
sum += val.get(); 
} 
context.write(key, new IntWritable(sum)); 
} 
    } 
 
    public static void main(String[] args) throws Exception { 
        if (args.length != 2) { 
            System.err.println("Usage: WordCount <input path> <output path>"); 
            System.exit(-1); 
        } 
 
        Configuration conf = new Configuration(); 
        Job job = Job.getInstance(conf, "Word Count"); 
 
        job.setJarByClass(WordCount.class); 
        job.setMapperClass(WordCountMapper.class); 
        job.setReducerClass(WordCountReducer.class); 
 
        job.setOutputKeyClass(Text.class); 
        job.setOutputValueClass(IntWritable.class); 
 
        FileInputFormat.addInputPath(job, new Path(args[0])); 
        FileOutputFormat.setOutputPath(job, new Path(args[1])); 
 
        System.exit(job.waitForCompletion(true) ? 0 : 1); 
    } 
}
```
### Compile and execute the code  
```
 javac WordCount.java
```
### Now go to the FILES , there we can see java code file and 3 setup files (Driver class, mapper class and reducer class)

## Creating the jar file.  
### Create new folder and name it as MM, move all 3 setup files into MM folder
```
jar -cvf MM.jar -C MM /.
```
#### Added manifest – means jar file created successfully. Go to FILE and check MM.jar file is created

## Prepare Input Data File.   
```
gedit input.txt  
```
#### enter the values to the text file and save. 
### Format: 
```
Welcome to Hadoop session 
Introduction to Hadoop 
Introducing Hive 
Hive session 
Pig session

```
### Store matrices as text files in HDFS.    
```
hdfs dfs -put input.txt /Input
``` 
### Run the MapReduce Program 
```
hadoop jar MM.jar WordCount /Input /Output
```
### View the Output
```
hdfs dfs -ls /Output
hdfs dfs -cat /Output/part-r-00000
```

### Output Will be
```

```