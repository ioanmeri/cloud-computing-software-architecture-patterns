# Section 3: Performance Patterns for Data Intensive Systems

- [Map Reduce Pattern for Big Data Processing](#map-reduce-pattern-for-big-data-processing)

---

## Map Reduce Pattern for Big Data Processing

### Introduction and Motivation

- Simplified "processing model"
- Came from the paper: "MapReduce: Simplified Data Processing on Large Clusters" by Jeffrey Dean and Sanjay Ghemawat, Google Inc. - 2004

---

### Problem Statement

The complexity involved in running computations on very large inputs

**Dominant factors**
- Parallelizing
- Distribution of data
- Scheduling execution
- Result aggregation
- Handling & recovering from errors

**Sources of overhead**
- A lot of boilerplate code
- Infrastructure

---

### The solution of the Map Reduce Pattern

- Follow the same programming model for every task / computation

---

### Map Reduce Framework - Implementations

- There have been multiple
  - Open source implementations
  - Proprietary, Cloud based implementations

of the Map Reduce Framework

---

### Map Reduce Pattern - Use Cases

- Machine Learning
- Filtering / Analyzing Logs
- Inverted Index Construction
- Web Link Graph Traversal
- Disributed Sort
- Distributed Search
- etc.

---

### Map Recude - Programming Model

![Map reduce framework](assets/19.png)

1. Describe input data as Key / Value Pairs
2. Map reduce framework passes all the input key value pairs through the map function
3. Produces a new set of Intermediate Key / Value Pairs
4. Map Reduce framework shuffles and groups all intermediate key value pairs by key
5. Passes them to the reduce function
6. Reduce function merges all the values for each such intermediate key

---

### Map Recude - Word Count Example


![Map reduce framework 1](assets/20.png)

![Map reduce framework 2](assets/21.png)

---

### Map Reduce Pattern - Open Questions

- How does the Map Reduce programming model help with scalability?
- What does Map Reduce have to do with Software Architecture?

---

### Map Reduce Architecture

We can define the general software architecture of the map reduce using two main components
- Master
  - The computer that schedules and orchestrates the entire map reduce computation
- Workers machines
  - hundreds or thousands

![Map reduce framework 2](assets/22.png)


The Master breaks the entire set of input key value pairs into **M chunks**
- This way we can split the entire data set accross **M different machines**

![Map reduce framework 2](assets/23.png)

![Map reduce framework 2](assets/24.png)


Run the developer supplied map function on those M machines
- Completely in parallel

![Map reduce framework 2](assets/25.png)

Each map worker runs the map function on every key value pair that was assigned to it
- Periodically stores the results in the local storage
- It partitions the output pairs into R regions (instead of one file)
  - `Region = hash(key) mod R`


![Map reduce framework 2](assets/26.png)


In the same time the Master also peaks another R workers
- Assings R chunks to R machines

![Map reduce framework 2](assets/27.png)

When the master notifies a Reduce Worker that the data is ready
- Reduce worker takes the data from the intermediate location
- Groups all the same key value - pairs together
- Sorts it by key for consistency (shuffling)
- Reduce workers invoke the developer supplied Reduce function on each key and list of values
- Outputs the results to a Globally accessible location

![Map reduce framework 2](assets/28.png)

Reduce workers are independent of each other
- Can run entirely in parallel

Reduce workers don't need to wait for all the map workers to finish
- Each reduce worker can start processing data immediately 

---




