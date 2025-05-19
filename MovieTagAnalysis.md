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
gedit  MovieTagAnalysis.java
```
### Copy the following code
```
import org.apache.hadoop.conf.Configuration; 
import org.apache.hadoop.fs.Path; 
import org.apache.hadoop.io.IntWritable; 
import org.apache.hadoop.io.Text; 
import org.apache.hadoop.mapreduce.Job; 
import org.apache.hadoop.mapreduce.Mapper; 
import org.apache.hadoop.mapreduce.Reducer; 
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat; 
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat; 
 
import java.io.IOException; 
import java.util.HashSet; 
 
public class MovieTagAnalysis { 
 
    // Mapper Class: Extracts movieId and tag 
    public static class MovieTagMapper extends Mapper<Object, Text, IntWritable, Text> { 
        private IntWritable movieId = new IntWritable(); 
        private Text tag = new Text(); 
 
        public void map(Object key, Text value, Context context) throws IOException, 
InterruptedException { 
            String line = value.toString(); 
 
            // Skip header line 
            if (line.startsWith("userId")) { 
                return; 
            } 
 
            String[] fields = line.split(","); 
 
            if (fields.length >= 3) { 
                try { 
                    int id = Integer.parseInt(fields[1].trim()); 
                    movieId.set(id); 
                    tag.set(fields[2].trim()); 
                    context.write(movieId, tag); 
                } catch (NumberFormatException e) { 
                    // Ignore invalid lines 
                } 
            } 
        } 
    } 
 
    // Reducer Class: Aggregates tags per movie 
    public static class MovieTagReducer extends Reducer<IntWritable, Text, IntWritable, Text> { 
        public void reduce(IntWritable key, Iterable<Text> values, Context context) throws IOException, 
InterruptedException { 
            HashSet<String> uniqueTags = new HashSet<>(); 
 
            for (Text val : values) { 
                uniqueTags.add(val.toString()); 
            } 
 
            String result = String.join(", ", uniqueTags); 
            context.write(key, new Text(result)); 
        } 
    } 
 
    // Driver Class: Sets up and runs the MapReduce job 
    public static void main(String[] args) throws Exception { 
        if (args.length != 2) { 
            System.err.println("Usage: MovieTagAnalysis <input path> <output path>"); 
            System.exit(-1); 
        } 
 
        Configuration conf = new Configuration(); 
        Job job = Job.getInstance(conf, "Movie Tags Analysis"); 
 
        job.setJarByClass(MovieTagAnalysis.class); 
        job.setMapperClass(MovieTagMapper.class); 
        job.setReducerClass(MovieTagReducer.class); 
 
        job.setMapOutputKeyClass(IntWritable.class); 
        job.setMapOutputValueClass(Text.class); 
        job.setOutputKeyClass(IntWritable.class); 
        job.setOutputValueClass(Text.class); 
 
        FileInputFormat.addInputPath(job, new Path(args[0])); 
        FileOutputFormat.setOutputPath(job, new Path(args[1])); 
 
        System.exit(job.waitForCompletion(true) ? 0 : 1); 
    } 
}
```
### Compile and execute the code  
```
 javac MovieTagAnalysis.java
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
gedit tags.txt  
```
#### enter the values to the text file and save. 
### Format: 
```
userId,movieId,tag,timestamp
15,339,sci-fi,1217897793
20,592,comedy,1199058625
12,106782,drugs,1445715054
2,106782,Leonardo DiCaprio,1445715051
2,106782,Martin Scorsese,1445715056
7,48516,way too long,1169687325
18,431,Al Pacino,1462138765
18,431,gangster,1462138749
18,431,mafia,1462138755
18,1221,Al Pacino,1461699306
18,1221,Mafia,1461699303
18,5995,holocaust,1455735472
18,5995,true story,1455735479
18,44665,twist ending,1456948283
18,52604,Anthony Hopkins,1457650696
18,52604,courtroom drama,1457650711
18,52604,twist ending,1457650682
18,88094,britpop,1457444500
18,88094,indie record label,1457444592
18,88094,music,1457444609
18,144210,dumpster diving,1455060381
18,144210,Sustainability,1455060452
21,1569,romantic comedy,1419805413
21,1569,wedding,1419805419
21,118985,painter,1419805477
21,119141,bloody,1419793962
49,109487,black hole,1493093306
49,109487,sci-fi,1493093332
49,109487,time-travel,1493093356
62,2,fantasy,1528843929
62,2,magic board game,1528843932
62,2,Robin Williams,1528843907
62,110,beautiful scenery,1528152541
62,110,epic,1528152532,
62,110,historical,1528152523
62,110,inspirational,1528152527
62,110,Medieval,1528152528
62,110,mel Gibson,1528152521
62,110,Oscar (Best Cinematography),1528152539
62,110,revenge,1528152531
62,110,sword fight,1528152535
62,410,black comedy,1525636607

```
## OR
```
gedit movielensData.csv
```
movielensData : [movielensData](/Lab%20manual/movielensData.csv)

### Store matrices as text files in HDFS.    
```
hdfs dfs -put tags.txt /Input
``` 
### Run the MapReduce Program 
```
hadoop jar MM.jar MovieTagAnalysis  /Input /Output
```
### View the Output
```
hdfs dfs -ls /Output
hdfs dfs -cat /Output/part-r-00000
```

### Output Will be
```

```