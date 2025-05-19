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