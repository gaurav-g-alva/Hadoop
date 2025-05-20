## Update The Environment
```
sudo apt update
sudo apt upgrade
```

## 1. Install Java
### Install the latest version of Java.
```
sudo apt install openjdk-8-jdk -y
```
## 2. Create Hadoop User and Configure Password-less SSH
### Add a new user hadoop.
```
sudo adduser hadoop
```
### Add the hadoop user to the sudo group.
```
sudo usermod -aG sudo hadoop
```
### Switch to the created user.
```
sudo su - hadoop
```
### Install the OpenSSH server and client.
```
sudo apt install openssh-server openssh-client -y
```
### Generate public and Private Key pairs
```
ssh-keygen -t rsa -P '' -f  ~/.ssh/id_rsa
sudo cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
sudo chmod 640 ~/.ssh/authorized_keys
ssh localhost
```
### Download the latest stable version of Hadoop. To get the latest version, go to Apache Hadoop official download page.
```
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.1/hadoop-3.3.1.tar.gz
```
### Extract the downloaded file
```
tar -xvzf hadoop-3.3.1.tar.gz
```
### Move the extracted directory to the /usr/local/ directory.
```
sudo mv hadoop-3.3.1 /usr/local/hadoop
```
### Create directory to store system logs.
```
sudo mkdir /usr/local/hadoop/logs
```
```
sudo mv hadoop-3.3.1 /usr/local/hadoop
```

### Change the ownership of the hadoop directory.
```
sudo chown -R hadoop:hadoop /usr/local/hadoop
```
## 4. Configure Hadoop
### Edit file ~/.bashrc to configure the Hadoop environment variables.
```
sudo nano ~/.bashrc
```
### Add the following lines to the file. Save and close the file.
```
export HADOOP_HOME=/usr/local/hadoop
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"
```
### Activate the environment variables.
```
source ~/.bashrc
```
## 5. Configure Java Environment Variables
## Find the Java path.
```
which javac
```
## Find the OpenJDK directory.
```
readlink -f /usr/bin/javac
```
### Edit the hadoop-env.sh file.
```
sudo nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```
### Add the following lines to the file. Then, close and save the file.
```
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export HADOOP_CLASSPATH+=" $HADOOP_HOME/lib/*.jar"
```
### Browse to the hadoop lib directory.
```
cd /usr/local/hadoop/lib
```
### Download the Javax activation file.
```
sudo wget https://jcenter.bintray.com/javax/activation/javax.activation-api/1.2.0/javax.activation-api-1.2.0.jar
```
### Verify the Hadoop version.
```
hadoop version
```
### Edit the core-site.xml configuration file to specify the URL for your NameNode.
```
sudo nano $HADOOP_HOME/etc/hadoop/core-site.xml
```
### Add the following lines. Save and close the file.
```
<configuration>
   <property>
      <name>fs.default.name</name>
      <value>hdfs://0.0.0.0:9000</value>
      <description>The default file system URI</description>
   </property>
</configuration>
```
### Create a directory for storing node metadata and change the ownership to hadoop.
```
sudo mkdir -p /home/hadoop/hdfs/{namenode,datanode}
sudo chown -R hadoop:hadoop /home/hadoop/hdfs
```
### Edit hdfs-site.xml configuration file to define the location for storing node metadata, fs-image file.
```
sudo nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```
### Add the following lines. Close and save the file.
```
<configuration>
   <property>
      <name>dfs.replication</name>
      <value>1</value>
   </property>

   <property>
      <name>dfs.name.dir</name>
      <value>file:///home/hadoop/hdfs/namenode</value>
   </property>

   <property>
      <name>dfs.data.dir</name>
      <value>file:///home/hadoop/hdfs/datanode</value>
   </property>
</configuration>
```
### Edit mapred-site.xml configuration file to define MapReduce values.
```
sudo nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
```
### Add the following lines. Save and close the file.
```
<configuration>
   <property>
      <name>mapreduce.framework.name</name>
      <value>yarn</value>
   </property>
</configuration>
```
### Add Above code OR Use below
```
<configuration>
   <property>
      <name>mapreduce.framework.name</name>
      <value>yarn</value>
   </property>
   <property>
      <name>mapreduce.application.classpath</name>
      <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/sha
re/hadoop/mapreduce/lib/*</value>
   </property>

   <property>
      <name>yarn.app.mapreduce.am.env</name>
      <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
   </property>

   <property>
      <name>mapreduce.map.env</name>
      <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
   </property>

   <property>
      <name>mapreduce.reduce.env</name>
      <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
   </property>
   
</configuration>

```
### Edit the yarn-site.xml configuration file and define YARN-related settings.
```
sudo nano $HADOOP_HOME/etc/hadoop/yarn-site.xml
```
### Add the following lines. Save and close the file.
```
<configuration>
   <property>
      <name>yarn.nodemanager.aux-services</name>
      <value>mapreduce_shuffle</value>
   </property>
</configuration>
```
### Log in with hadoop user.
```
sudo su - hadoop
```
### Validate the Hadoop configuration and format the HDFS NameNode.
```
hdfs namenode -format
```
## 6. Start the Apache Hadoop Cluster
### Start the NameNode and DataNode.
```
start-dfs.sh
```
### Start the YARN resource and node managers.
```
start-yarn.sh
```
### Verify all the running components.
```
jps
```