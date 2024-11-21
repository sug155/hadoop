### Step-by-Step Hadoop Installation and WordCount MapReduce Example on Ubuntu

This guide will walk you through installing Hadoop and running a simple WordCount MapReduce example on Ubuntu.

---

### Step 1: Install Java
Hadoop requires Java, so make sure you have it installed.

1. **Install OpenJDK**:
   ```bash
   sudo apt update
   sudo apt install openjdk-11-jdk
   ```

2. **Verify Java Installation**:
   ```bash
   java -version
   ```

   Make sure the output shows `java version "11"` or something similar.

---

### Step 2: Download and Install Hadoop

1. **Install Required Dependencies**:
   ```bash
   sudo apt-get install ssh rsync
   ```

2. **Create a User for Hadoop**:
   Hadoop runs as a non-root user, so let's create a new user for it.
   ```bash
   sudo adduser hadoop
   sudo usermod -aG sudo hadoop
   ```

3. **Download Hadoop**:
   Go to the [Apache Hadoop download page](https://hadoop.apache.org/releases.html) to get the latest stable version of Hadoop. For example, version 3.3.0:
   ```bash
   wget https://downloads.apache.org/hadoop/common/hadoop-3.3.0/hadoop-3.3.0.tar.gz
   ```

4. **Extract the Hadoop archive**:
   ```bash
   tar -xvzf hadoop-3.3.0.tar.gz
   sudo mv hadoop-3.3.0 /usr/local/hadoop
   ```

5. **Set Hadoop Environment Variables**:
   Add the following environment variables to `~/.bashrc` for the `hadoop` user.
   ```bash
   nano ~/.bashrc
   ```

   Add the following at the end of the file:
   ```bash
   export HADOOP_HOME=/usr/local/hadoop
   export HADOOP_INSTALL=$HADOOP_HOME
   export HADOOP_COMMON_HOME=$HADOOP_HOME
   export HADOOP_HDFS_HOME=$HADOOP_HOME
   export HADOOP_MAPRED_HOME=$HADOOP_HOME
   export HADOOP_YARN_HOME=$HADOOP_HOME
   export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
   ```

   After editing, reload the bash profile:
   ```bash
   source ~/.bashrc
   ```

6. **Configure Hadoop**:
   You need to configure Hadoop by editing its configuration files. Navigate to the Hadoop configuration directory:
   ```bash
   cd /usr/local/hadoop/etc/hadoop
   ```

   - Edit `hadoop-env.sh` to set `JAVA_HOME`:
     ```bash
     nano hadoop-env.sh
     ```
     Add this line:
     ```bash
     export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
     ```

   - Edit `core-site.xml` to set the default filesystem:
     ```bash
     nano core-site.xml
     ```

     Add the following inside the `<configuration>` tag:
     ```xml
     <property>
         <name>fs.defaultFS</name>
         <value>hdfs://localhost:9000</value>
     </property>
     ```

   - Edit `hdfs-site.xml` to set Hadoop's HDFS settings:
     ```bash
     nano hdfs-site.xml
     ```

     Add the following inside the `<configuration>` tag:
     ```xml
     <property>
         <name>dfs.replication</name>
         <value>1</value>
     </property>
     <property>
         <name>dfs.namenode.name.dir</name>
         <value>file:///usr/local/hadoop/hdfs/namenode</value>
     </property>
     <property>
         <name>dfs.datanode.data.dir</name>
         <value>file:///usr/local/hadoop/hdfs/datanode</value>
     </property>
     ```

   - Edit `mapred-site.xml`:
     ```bash
     cp mapred-site.xml.template mapred-site.xml
     nano mapred-site.xml
     ```

     Add the following:
     ```xml
     <property>
         <name>mapreduce.framework.name</name>
         <value>yarn</value>
     </property>
     ```

   - Edit `yarn-site.xml`:
     ```bash
     nano yarn-site.xml
     ```

     Add this inside the `<configuration>` tag:
     ```xml
     <property>
         <name>yarn.resourcemanager.address</name>
         <value>localhost:8032</value>
     </property>
     ```

---

### Step 3: Format Hadoop Filesystem

Before running Hadoop, format the HDFS (Hadoop Distributed File System):

```bash
hdfs namenode -format
```

---

### Step 4: Start Hadoop Daemons

Start the Hadoop daemons (NameNode, DataNode, ResourceManager, and NodeManager):

```bash
start-dfs.sh
start-yarn.sh
```

To verify if Hadoop is running, check the following URLs:

- NameNode: `http://localhost:9870`
- ResourceManager: `http://localhost:8088`

---

### Step 5: Running the WordCount MapReduce Example

1. **Create an Input Directory**:
   Create an input directory on HDFS:
   ```bash
   hdfs dfs -mkdir /user/hadoop/input
   ```

2. **Upload Input Data**:
   Upload some input data to the `input` directory. For example, you can use a text file like `input.txt`:
   ```bash
   hdfs dfs -put input.txt /user/hadoop/input/
   ```

3. **Run the WordCount Example**:
   Run the following command to execute the WordCount MapReduce job:

   ```bash
   hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar wordcount /user/hadoop/input /user/hadoop/output
   ```

   This command runs the WordCount example, reading from `/user/hadoop/input` and writing the output to `/user/hadoop/output`.

4. **View the Output**:
   Once the job completes, check the output directory:
   ```bash
   hdfs dfs -cat /user/hadoop/output/part-r-00000
   ```

   The output will show the word count for each word in the input file.

---

### Step 6: Stop Hadoop Daemons

When done, stop the Hadoop daemons:
```bash
stop-dfs.sh
stop-yarn.sh
```

---

This process should help you install Hadoop on Ubuntu and run the WordCount MapReduce example. Let me know if you need help with any specific step or further details!
