## Install Apache Spark
## Install required packages.
```
sudo apt install curl mlocate git scala -y
```
### Download Apache Spark. Find the latest release from the downloads page.
```
curl -O https://archive.apache.org/dist/spark/spark-3.2.0/spark-3.2.0-bin-hadoop3.2.tgz
```
### Extract the Spark tarball.
```
sudo tar xvf spark-3.2.0-bin-hadoop3.2.tgz
```
### Create an installation directory /opt/spark.
```
sudo mkdir /opt/spark
```
### Move the extracted files to the installation directory.
```
sudo mv spark-3.2.0-bin-hadoop3.2/* /opt/spark
```
### Change the permission of the directory.
```
$ sudo chmod -R 777 /opt/spark
```
### Edit the bashrc configuration file to add Apache Spark installation directory to the system path.
```
sudo nano ~/.bashrc
```
### Add the code below at the end of the file, save and exit the file:
```
export SPARK_HOME=/opt/spark
export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin
```
## Save the changes to take effect.
```
source ~/.bashrc
```
### Start the standalone master server.
```
start-master.sh
```

```
/opt/spark/bin/spark-shell
```

```
echo "hello world hello spark hadoop hadoop mapreduce spark" > ~/input.txt
```
```
val input = sc.textFile("file:///home/ailab/input.txt")
val words = input.flatMap(line => line.split("\\s+"))
val wordPairs = words.map(word => (word, 1))
val wordCounts = wordPairs.reduceByKey(_ + _)
wordCounts.collect().foreach(println)
```
<hr>

### Install PySpark
```
pip install pyspark
```
### Run the PySpark shell:

```
pyspark
```
If Spark opens successfully, installation is good.

### Sample file: 
```
sample.txt
```
```
hello world
hello spark
apache spark is fast
```
### Python WordCount Script: 
```
wordcount.py
```
```
from pyspark import SparkContext

sc = SparkContext("local", "WordCount")

# Load the text file
text_file = sc.textFile("sample.txt")

# WordCount logic
counts = (
    text_file.flatMap(lambda line: line.split(" "))
             .map(lambda word: (word, 1))
             .reduceByKey(lambda a, b: a + b)
)

# Save or display result
counts.saveAsTextFile("output")
# counts.foreach(print)  # To print instead of saving

sc.stop()
```
### Run the script:
```
python3 wordcount.py
```
### Check results in the output/ directory:
```
cat output/part-*
```

<hr>