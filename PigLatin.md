##  Install Hadoop and Pig 
## Install Java  
## Install Hadoop 
### Install Apache Pig 

## Download Apache Pig 
### Open a terminal and run: 
```
cd /opt
sudo wget https://downloads.apache.org/pig/latest/pig-0.17.0.tar.gz
```
## Extract the Pig archive 
```
sudo tar -xvzf pig-0.17.0.tar.gz
```
```
sudo mv pig-0.17.0 pig
```
## Set Environment Variables 
### Edit your .bashrc 
```
nano ~/.bashrc 
```
### Add these lines at the bottom:
```
export PIG_HOME=/opt/pig 
export PATH=$PATH:$PIG_HOME/bin 
export PIG_CLASSPATH=$HADOOP_HOME/etc/Hadoop
```
```
Note: 
• HADOOP_HOME must already be set (you said Hadoop is installed). 
• If not, you might have to set it too: 
export HADOOP_HOME=/opt/hadoop 
export PATH=$PATH:$HADOOP_HOME/bin
```
### Now, reload the bash profile: 
```
source ~/.bashrc
```
## Test Pig Installation 
```
pig -version
```
## Running Pig 
### You can run Pig in two modes: 
#### Local mode (no need for Hadoop): 
```
pig -x local 
 ```

### MapReduce mode (with Hadoop cluster) 
```
pig 
```
#### You’ll get into the grunt> shell to start writing Pig Latin scripts.

## Prepare Your Data Files 
 
### Create a directory to work: 
```
mkdir pig_project 
cd pig_project
```
### Create a data file : 
```
nano students.txt
```
```
1,Jayan,Math,85 
2,Akshara,English,78 
3,Bararth,Math,92 
4,John,English,88 
5,Charan,Math,90
```
## Write the Pig Script 
### Create a script file: 
```
nano student_operations.pig
```
### Type the following script:
```
students = LOAD 'students.txt' USING PigStorage(',') AS (student_id:int, name:chararray, subject:chararray, score:int); 

high_scores = FILTER students BY score > 80; 

projected = FOREACH high_scores GENERATE name, score;

grouped = GROUP projected BY name;

flattened = FOREACH grouped { 
    sorted = ORDER projected BY score DESC; 
    GENERATE group AS name, sorted; 
};

DUMP flattened;

```

## Run the Pig Script 
### Since we are not using a Hadoop cluster, run in local mode 
```
pig -x local student_operations.pig 
```
#### Output will be printed on the terminal.