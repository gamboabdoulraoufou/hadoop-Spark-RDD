## hadoop-Spark-RDD

This post will cover the following points:
- what is RDD  
- how to manipulate RDD using _pyspark API_


**What is Resilient Distributed Dataset**  
RDD is the data container, the way in Spark to store data like variable or object in programming.
Data can be read from local text file, HDFS or other source.  
Caracteristiques:
- Immutable: you cannot change just a section of RDD. But every time you transform one RDD, you create a new one.  
- Distributed: partition of data are divided accross machine
- Resilient: for every point in your calculations, Spark knows which are the partitions needed to recreate the partition in case it gets lost. And if that happens, then spark automatically figures out where it can start from to recompute what's the minimum amount of processing needed to recover the lost partition

**RDD manipulation**  
***Create RDD***   
Go to your shell console and tape this command
```sh
pyspark
```

Now you are in Python Spark shell console
```python
# Create RDD in 3 partitions
integer_RDD = sc.parallelize(range(10), 3)

# Check
integer_RDD.collect()

# Check partitions
integer_RDD.glom().collect()

# Read data form txt file
text_RDD = sc.textFile("file:///home/cloudera/testfile1")

# Read data from HDFS
sc.textFile("/user/cloudera/input/testfile1")

# Outputs the first line
text_RDD.take(1)

```

***Word count in spark***

```python
# map functions
def split_words(line):
    return line.split()
    
def create_pair(word):
    return (word, 1)

pairs_RDD=text_RDD.flatMap(split_words).map(create_pair)


# reduce functions
def sum_counts(a, b):
    return a + b

wordcounts_RDD = pairs_RDD.reduceByKey(sum_counts)

# Show result
wordcounts_RDD.collect()
```


