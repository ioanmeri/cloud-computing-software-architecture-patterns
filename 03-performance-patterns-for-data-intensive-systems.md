# Section 3: Performance Patterns for Data Intensive Systems

- [Map Reduce Pattern for Big Data Processing](#map-reduce-pattern-for-big-data-processing)
- [The Saga Pattern](#the-saga-pattern)

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

### Map Reduce Pattern - Benefits

- Map Reduce programming model allows us to
  - Reuse the same software architecture
  - Scale the processing of a task
  - Run computations in parallel by many workers
- The result
  - Processing of massive amounts of data in a short time

---

### Map Reduce Pattern - Handling Failures & Recovery

- Many worker computers ➡️ high chance of failure while processing data
  - Master can ping reduce workers periodically
  - Master can Reschedule to new worker
    - If map worker failed, master needs to modify reduce worker for the new location
- The failure of the master is less likely
  - It's still a possibility
  - Option 1: abort the entire map - reduce computation, start over with a new master
    - Time penalty from redoing some computation
  - Option 2: make the master take frequent Snapshots of the current scheduling
    - Replay log to sync new master
  - Can have a backup Master: Inactive but staying in sync
    - Begins taking the role of master   

---

### Map Reduce Pattern - Cloud Applications

Map Reduce Pattern is a perfect fit for the cloud environment because

1. Cloud providers Instant access to as many machines as we need
2. Map Reduce is a Batch Processing model (on demand / on schedule execution)
  a. We pay only for the resources we use during the processing
  b. We don't pay for maintaining of machines

**Note**: We do pay for storing / accessing data, which is cheaper than compute resources

---

### Map Reduce Pattern - Implementation in Practice

- We almost never implement Map Reduce on our own
- We pick an open source / cloud vendor implementation
- We still follow the same
  - Data modeling
  - Programming model (map + reduce)
  - Defining execution parameters

---

### Summary

- Introduction to Map Reduce Pattern
- Motivation: Architecture and implementation for processing big data sets
- Map Reduce programming model
  - Map
  - Reduce
- Parts of the Map Reduce Architecture
- Benefits
  - Scaling any computation across massive amounts of data
  - Processing in short time
- Strategies for handling and recovering from failures of
  - Workers
  - Master

---

## The Saga Pattern

### Saga Pattern - Problem Statement

- Reminder: Microservices Architecture benefits
  - High engineering velocity
  - Scalability
  - Etc
- Important principle of Microservice
  - One Database Per Microservices

If we allow multiple microservices to share a database ➡️ we couple them with each other

E.g if one teams want sto change the schema of a database, they need to coordinate with the other team
- Those two teams they will have a meeting
- Agree on changes first
- Coordinate the migration so nothing breaks
- Those changes have to be made for all microservices at the same time

This defeats the purpose of microservices architecture

---

### Shared Database - Distributed Monolith Anti-Pattern

We are back to the anti-pattern of a distributed monolith.

The worst case scenario that we need to avoid.

If we have a separate database per microservice we will not have that problem

---

### One Database per Microservice

Each team can change
- the schema
- the entire stack

without affecting other service

---

### Saga Pattern - Problem Statement

- With one database per microservice **we loce ACID Transaction** properties
- A transaction is a "sequence of operations that for an external observer should appear as a single operation"
- So how do we manage data consistency across microservices within a distributed transaction?
  - Solution: **Saga Pattern**

---

### Saga Architecture Pattern

In the Saga Pattern we perform the operations that are part of a transaction as a sequence of local transactions in each database.

Each successful operation triggers the next operation in the sequence.

![Saga Architecture Success](assets/29.png)

If a certain operation fails, the Saga pattern rolls back the previous operation / operations by applying **Compensating Operations** that have the opposite effect from the original operation 

After we roll back, we can either abort the transaction entirely or Retry multiple times until we succeed.

![Saga Architecture Failure](assets/30.png)

---

### Saga Pattern - Implementations

- Saga Pattern can be implemented using
  - Execution Orchestrator Pattern
  - Choreography Pattern
- With either of the implementations we can execute transactions
  - That span multiple services
  - Without a centralized database

---

### Saga with Execution Orchestrator Pattern

In Execution Orchestrator Pattern we have an orchestrator service that manages the entire distributed transaction by calling different services in order and waiting for the response from all of them.

![Saga Implementation with Execution Orchestrator](assets/31.png)

Depending on the response of each service, the Orchestrator service decides whether to 
- proceed with the transaction and call the next service
- or Roll back the transaction and call the previous service with a compensating operation



---

### Saga with Choreography Pattern

In the Choreography implementation of the Saga Pattern, we have a Message Broker in the middle
- the services are subscribed to relevant events

![Saga Implementation with Choreography Pattern](assets/32.png)

we don't have a centralized service to manage the transaction, so each service is responsible for either
- triggering the next event in the transaction sequence
- or triggering a compensating event for the previous service in the sequence

---

### Saga Pattern - Real Life Example

Example: A company that sells tickets to events such as
  - Live performances
  - Movies
  - Shows

**Business requirements**

Given a pool of tickets for a given event and each ticket corresponds to a particular seat

When a customer purchases a ticket they get an assigned seat reserved specifically for them

---

### Ticket Selling implementation Monolith

![Saga Implementation Example](assets/33.png)

---

### Ticket Selling Service - Requirements

When a ticket reservation is complete, we guarantee

1. User is not a bot/black listed agent
2. Payment is collected from the customer
3. We sell only one ticket for a seat



![Saga Implementation Example](assets/34.png)

![Saga Implementation Example](assets/35.png)

![Saga Implementation Example](assets/36.png)

![Saga Implementation Example](assets/37.png)

![Saga Implementation Example](assets/38.png)

![Saga Implementation Example](assets/39.png)

![Saga Implementation Example](assets/40.png)

![Saga Implementation Example](assets/41.png)

![Saga Implementation Example](assets/42.png)

![Saga Implementation Example](assets/43.png)

![Saga Implementation Example](assets/44.png)

![Saga Implementation Example](assets/45.png)

![Saga Implementation Example](assets/46.png)

![Saga Implementation Example](assets/47.png)

![Saga Implementation Example](assets/48.png)

![Saga Implementation Example](assets/49.png)

![Saga Implementation Example](assets/50.png)

![Saga Implementation Example](assets/51.png)

![Saga Implementation Example](assets/52.png)

![Saga Implementation Example](assets/53.png)

![Saga Implementation Example](assets/54.png)

![Saga Implementation Example](assets/55.png)

![Saga Implementation Example](assets/56.png)

![Saga Implementation Example](assets/57.png)


---

### Saga Pattern - Choreography Implementaton

- Each service is responsible for
  - Sending events in case of **success**
  - Sending **compensating** events to previous services in the flow, in case of **failure**

![Saga Implementation Example](assets/58.png)

---

### Summary

- Saga Software Architecture Pattern helps with data consistency managament in Microservices Architecture
- Allows performing distributed transactions across multiple databases
- Saga Pattern implementations
  - Execution Orchestrator Pattern
  - Choreography Pattern
- Saga Pattern principles
  - Proceed with the transaction in case of **success**
  - Roll back by performing *compensating* operations in case of **failure**
  
---














