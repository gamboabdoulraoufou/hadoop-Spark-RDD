## hadoop-Spark RDD

This post will cover the following points:
- what is RDD  
- how to manipulate RDD using _pyspark API_ (mapreduce example)
- Some RDD actions
- Caching data 
- Shared Variables
- Accumulator


**What is RDD (Resilient Distributed Dataset)**  
RDD is the data container, the way in Spark to store data like variable or object in programming.
Data can be read from local text file, HDFS or other source. 
  
Caracteristiques:
- Immutable: you cannot change just a section of RDD. But every time you transform one RDD, you create a new one.  
- Distributed: partition of data are divided accross machine
- Resilient: for every point in your calculations, Spark knows which are the partitions needed to recreate the partition in case it gets lost. And if that happens, then spark automatically figures out where it can start from to recompute what's the minimum amount of processing needed to recover the lost partition

**RDD manipulation**  
Go to your shell console and tape this command
```sh
pyspark
```

Create RDD
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
text_RDD = sc.textFile("/user/cloudera/input/testfile1")

# Outputs the first line
text_RDD.take(1)

```

Word count in spark  
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

**Some RDD actions**
- rdd_name.copy: copy all elements to the driver
- rdd_name.take(n): copy first n elements
- rdd_name.reduce(func) - aggregate elements with func (takes 2 elements, returns 1)
- rdd_name.saveAsTextFile(filename) - save to local file or HDFS


**Caching data**  
Once you have prepared your data for your iterative algorithm, for example, a machine learning algorithm, it's a good idea to cache that data. So that each iteration of your algorithm can read data from memory and be very fast.

```python
rdd_name.cache()
```

**Shared Variables**    
```python
config = sc.broadcast({"order":3, "filter":True})
config.value
```

**Accumulator**  
```python
accum = sc.accumulator(0)

def test_accum(x):
    accum.add(x)
    
sc.parallelize([1, 2, 3, 4]).foreach(test_accum)
accum.value
```
