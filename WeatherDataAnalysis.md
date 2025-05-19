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
gedit  WeatherDataAnalysis.java
```
### Copy the following code
```
import java.io.*; 
import java.util.*; 
import org.apache.hadoop.conf.Configuration; 
import org.apache.hadoop.fs.Path; 
import org.apache.hadoop.io.*; 
import org.apache.hadoop.mapreduce.*; 
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat; 
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat; 
 
public class WeatherDataAnalysis { 
 
    public static class WeatherMapper extends Mapper<LongWritable, Text, Text, Text> { 
        @Override 
        protected void map(LongWritable key, Text value, Context context) throws IOException, 
InterruptedException { 
            String[] tokens = value.toString().split(","); 
 
            if (tokens.length == 5 && !tokens[0].equals("Date")) { // Ignore header line 
                String date = tokens[0]; 
                String location = tokens[1]; 
                int temperature = Integer.parseInt(tokens[2]); 
 
                String condition; 
                if (temperature > 35) { 
                    condition = "Hot"; 
                } else if (temperature < 15) { 
                    condition = "Cold"; 
                } else { 
                    condition = "Normal"; 
                } 
 
                context.write(new Text(date + "," + location), new Text(condition)); 
            } 
        } 
    } 
 
    public static class WeatherReducer extends Reducer<Text, Text, Text, Text> { 
        @Override 
        protected void reduce(Text key, Iterable<Text> values, Context context) throws IOException, 
InterruptedException { 
            for (Text value : values) { 
                context.write(key, value); 
            } 
        } 
    } 
 
    public static void main(String[] args) throws Exception { 
        Configuration conf = new Configuration(); 
        Job job = Job.getInstance(conf, "Weather Data Analysis"); 
 
        job.setJarByClass(WeatherDataAnalysis.class); 
        job.setMapperClass(WeatherMapper.class); 
        job.setReducerClass(WeatherReducer.class); 
 
        job.setMapOutputKeyClass(Text.class); 
        job.setMapOutputValueClass(Text.class); 
 
        job.setOutputKeyClass(Text.class); 
        job.setOutputValueClass(Text.class); 
 
FileInputFormat.addInputPath(job, new Path(args[0])); 
FileOutputFormat.setOutputPath(job, new Path(args[1])); 
System.exit(job.waitForCompletion(true) ? 0 : 1); 
} 
}
```
### Compile and execute the code  
```
 javac WeatherDataAnalysis.java
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
gedit wda.csv  
```
#### enter the values to the text file and save. 
### Format: 
```
Date,Location,Temperature,Humidity,Pressure 
2025-03-15,NewYork,35,60,1012 
2025-03-15,LosAngeles,40,30,1010 
2025-03-15,Chicago,28,70,1111 
2025-03-16,Manglore,32,760,1125 
2025-03-17,Madikeri,14,50,1256 
2025-04-18,Tumkur,40,47,1253
2025-04-20,Mandya,36,38,1278

```
### Store matrices as text files in HDFS.    
```
hdfs dfs -put wda.csv /Input
``` 
### Run the MapReduce Program 
```
hadoop jar MM.jar WeatherDataAnalysis /Input /Output
```
### View the Output
```
hdfs dfs -ls /Output
hdfs dfs -cat /Output/part-r-00000
```

### Output Will be
```
2025-03-15 NewYork      Hot
2025-03-15  LosAngeles  Hot
2025-03-15  Chicago     Normal
2025-03-16  Manglore    Normal
2025-03-17  Madikeri    Cold
2025-04-18  Tumkur      Hot
2025-04-20  Mandya      Hot
```