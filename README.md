
# WordCount-Using-MapReduce-Hadoop

This repository is designed to test MapReduce jobs using a simple word count dataset.

## Project overview
This project is a Word Count application built on Hadoop that uses the MapReduce framework to process big text files in a distributed fashion. After tokenizing the words in an input text file, the computer calculates the frequency of each distinct word in the dataset. There are three primary parts to it:

Job configuration and execution are managed by the Controller (Driver) Class.
The Mapper Class generates key-value pairs by extracting words from input text.

Reducer Class: Generates final word counts by aggregating word occurrences.
This method makes use of Hadoop's distributed computing capabilities to process and analyze massive amounts of text data efficiently.


## Objectives

By completing this activity, students will:

1. **Understand Hadoop's Architecture:** Learn how Hadoop's distributed file system (HDFS) and MapReduce framework work together to process large datasets.
2. **Build and Deploy a MapReduce Job:** Gain experience in compiling a Java MapReduce program, deploying it to a Hadoop cluster, and running it using Docker.
3. **Interact with Hadoop Ecosystem:** Practice using Hadoop commands to manage HDFS and execute MapReduce jobs.
4. **Work with Docker Containers:** Understand how to use Docker to run and manage Hadoop components and transfer files between the host and container environments.
5. **Analyze MapReduce Job Outputs:** Learn how to retrieve and interpret the results of a MapReduce job.

## Approach and Implementation

###### Mapper Logic (WordMapper Class)

Input: A line of text (key: byte offset, value: line content).
Processing:
Converts the line into a string.
Splits the string into words based on whitespace.
Converts words to lowercase and emits each word as a key with a value of 1.
Output: (word, 1) pairs.
###### Reducer Logic (WordReducer Class)

Input: Key-value pairs where the key is a word, and values are a list of occurrences.
Processing:
Iterates over values to sum up the occurrences.
Stores the total count for each unique word.
Output: (word, total count) pairs.


## Setup and Execution

### 1. **Start the Hadoop Cluster**

Run the following command to start the Hadoop cluster:

```bash
docker compose up -d
```

### 2. **Build the Code**

Build the code using Maven:

```bash
mvn install
```

### 3. **Move JAR File to Shared Folder**

Move the generated JAR file to a shared folder for easy access:

```bash
mv target/*.jar shared-folder/input/code/
```

### 4. **Copy JAR to Docker Container**

Copy the JAR file to the Hadoop ResourceManager container:

```bash
docker cp shared-folder/input/code/<your-jar-file>.jar resourcemanager:/opt/hadoop-3.2.1/share/hadoop/mapreduce/
```

### 5. **Move Dataset to Docker Container**

Copy the dataset to the Hadoop ResourceManager container:

```bash
docker cp shared-folder/input/data/input.txt resourcemanager:/opt/hadoop-3.2.1/share/hadoop/mapreduce/
```

### 6. **Connect to Docker Container**

Access the Hadoop ResourceManager container:

```bash
docker exec -it resourcemanager /bin/bash
```

Navigate to the Hadoop directory:

```bash
cd /opt/hadoop-3.2.1/share/hadoop/mapreduce/
```

### 7. **Set Up HDFS**

Create a folder in HDFS for the input dataset:

```bash
hadoop fs -mkdir -p /input/dataset
```

Copy the input dataset to the HDFS folder:

```bash
hadoop fs -put ./input.txt /input/dataset
```

### 8. **Execute the MapReduce Job**

Run your MapReduce job using the following command:

```bash
hadoop jar /opt/hadoop-3.2.1/share/hadoop/mapreduce/<your-jar-file>.jar com.example.controller.Controller /input/dataset/input.txt /output
```

### 9. **View the Output**

To view the output of your MapReduce job, use:

```bash
hadoop fs -cat /output/*
```

### 10. **Copy Output from HDFS to Local OS**

To copy the output from HDFS to your local machine:

1. Use the following command to copy from HDFS:
    ```bash
    hdfs dfs -get /output /opt/hadoop-3.2.1/share/hadoop/mapreduce/
    ```

2. use Docker to copy from the container to your local machine:
   ```bash
   exit 
   ```
    ```bash
    docker cp resourcemanager:/opt/hadoop-3.2.1/share/hadoop/mapreduce/output/ shared-folder/output/
    ```
3. Commit and push to your repo so that we can able to see your output

## Challenges Faced & Solutions

##### 1. Challenge: 
If the resourcemanager container is not running or the target path is not valid, the docker cp command may not succeed.

##### Solution:
To see if the resourcemanager container is running, run docker ps.
Verify that the container contains the destination path (docker exec -it resourcemanager /bin/bash and ls /opt/hadoop-3.2.1/share/hadoop/mapreduce/).

##### 2. Challenge: 
The resourcemanager container cannot be accessed if the container name is either absent or wrong.

##### Solution:
Run docker ps to confirm the container name before executing docker exec -it resourcemanager /bin/bash.

##### 3. Challenge: 
Errors when creating HDFS directories or copying files, such as No such file or directory or Permission denied.
##### Solution:
Verify HDFS is running properly: hadoop fs -ls /.

## Sample Input and Output

#### Input Format

Hello world
Hello Hadoop
Hadoop is powerful
Hadoop is used for big data
    
#### Expected Output

Hadoop 3
Hello 2
is 2
used 1
for 1
big 1
data 1
powerful 1
world 1
    


