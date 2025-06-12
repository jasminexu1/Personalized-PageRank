# Personalized PageRank

Java implementation of multi-source Personalized PageRank using Hadoop MapReduce.

## Overview

- Computes Personalized PageRank with respect to multiple source nodes
- Uses log-space probabilities to reduce floating-point precision errors
- Single-phase MapReduce approach (handles random jumps and dangling mass inline)
- Runs on both small (Gnutella) and large-scale (Wikipedia) graphs using HDFS

This project was developed based on the reference implementation provided in [Bespin](https://github.com/lintool/bespin), an open-source Hadoop framework by Professor Jimmy Lin (University of Waterloo).


## Usage

1. Clone the repo and compile using Maven or your preferred build tool.

2. Download `p2p-Gnutella08-adj.txt` from the [Bespin Data repository](https://github.com/lintool/bespin-data) and place it in the `data/` folder.

3. Run the following command to use the personalized PageRank:
```
# Step 1: Create HDFS directory for initial PageRank records
hadoop fs -mkdir pagerank-records

# Step 2: Build personalized PageRank node records
hadoop jar target/personalized-pagerank-0.1.jar \
  BuildPersonalizedPageRankRecords \
  -input data/p2p-Gnutella08-adj.txt \
  -output pagerank-records \
  -numNodes 6301 -sources 367,249,145

# Step 3: Create HDFS directory for iteration outputs
hadoop fs -mkdir pagerank-output

# Step 4: Partition the graph into reducers
hadoop jar target/personalized-pagerank-0.1.jar \
  PartitionGraph \
  -input pagerank-records \
  -output pagerank-output/iter0000 \
  -numPartitions 5 -numNodes 6301

# Step 5: Run personalized PageRank for 20 iterations
hadoop jar target/personalized-pagerank-0.1.jar \
  RunPersonalizedPageRankBasic \
  -base pagerank-output \
  -numNodes 6301 -start 0 -end 20 -sources 367,249,145

# Step 6: Extract top 10 nodes by PageRank score
hadoop jar target/personalized-pagerank-0.1.jar \
  FindMaxPageRankNodes \
  -input pagerank-output/iter0020 \
  -output pagerank-top10 \
  -top 10

```
