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
gedit  MatrixMultiplication.java
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
import java.util.ArrayList; 
 
public class MatrixMultiplication { 
 
    public static class MatrixMapper extends Mapper<Object, Text, Text, Text> { 
        public void map(Object key, Text value, Context context) throws IOException, 
InterruptedException { 
            String[] tokens = value.toString().split(","); 
            String matrixName = tokens[0]; 
            int row = Integer.parseInt(tokens[1]); 
            int col = Integer.parseInt(tokens[2]); 
            int val = Integer.parseInt(tokens[3]); 
 
            if (matrixName.equals("A")) { 
                for (int k = 0; k < 3; k++) {  // Assuming B has 3 columns 
                    context.write(new Text(row + "," + k), new Text("A," + col + "," + val)); 
                } 
            } else if (matrixName.equals("B")) { 
                for (int i = 0; i < 3; i++) {  // Assuming A has 3 rows 
                    context.write(new Text(i + "," + col), new Text("B," + row + "," + val)); 
                } 
            } 
        } 
    } 
 
    public static class MatrixReducer extends Reducer<Text, Text, Text, IntWritable> { 
        public void reduce(Text key, Iterable<Text> values, Context context) throws IOException, 
InterruptedException { 
            ArrayList<int[]> aElements = new ArrayList<>(); 
            ArrayList<int[]> bElements = new ArrayList<>(); 
 
            for (Text val : values) { 
                String[] tokens = val.toString().split(","); 
                int[] pair = {Integer.parseInt(tokens[1]), Integer.parseInt(tokens[2])}; 
 
                if (tokens[0].equals("A")) { 
                    aElements.add(pair); 
                } else { 
                    bElements.add(pair); 
                } 
            } 
 
            int sum = 0; 
            for (int[] a : aElements) { 
                for (int[] b : bElements) { 
                    if (a[0] == b[0]) {  // Multiply only if column index matches row index 
                        sum += a[1] * b[1]; 
                    } 
                } 
            } 
 
            context.write(key, new IntWritable(sum)); 
        } 
    } 
 
    public static void main(String[] args) throws Exception { 
        Configuration conf = new Configuration(); 
        Job job = Job.getInstance(conf, "Matrix Multiplication"); 
        job.setJarByClass(MatrixMultiplication.class); 
        job.setMapperClass(MatrixMapper.class); 
        job.setReducerClass(MatrixReducer.class); 
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
 javac MatrixMultiplication.java
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
gedit matrix.txt  
```
#### enter the values to the text file and save. 
### Format: Matrix (m x n):
```
A,0,0,7
A,0,1,7
A,0,2,7
A,1,0,7
A,1,1,7
A,1,2,7
A,2,0,7
A,2,1,7
A,2,2,7 
B,0,0,5
B,0,1,5
B,0,2,5
B,1,0,5
B,1,1,5
B,1,2,5
B,2,0,5
B,2,1,5
B,2,2,5 
```
### Store matrices as text files in HDFS.    
```
hdfs dfs -put matrix.txt /Input
``` 
### Run the MapReduce Program 
```
hadoop jar MM.jar MatrixMultiplication /Input /Output
```
### View the Output
```
hdfs dfs -ls /Output
hdfs dfs -cat /Output/part-r-00000
```

### Output Will be
```
0,0     105
0,1     105
0,2     105
1,0     105
1,1     105
1,2     105 
2,0     105
2,1     105
2,2     105 
```