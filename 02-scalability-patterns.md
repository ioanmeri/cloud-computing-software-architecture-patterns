# Section 2: Scalability Patterns

- [Load Balancing Pattern - Software Architecture & Cloud Computing Use Cases](#load-balancing-pattern---software-architecture--cloud-computing-use-cases)
- [Pipes and Filters Pattern](#pipes-and-filters-pattern)
- [Scatter Gather Pattern](#scatter-gather-pattern)

---

## Load Balancing Pattern - Software Architecture & Cloud Computing Use Cases

### Scalability Patterns

- The most important patterns that allow us to
  - Architect
  - Deploy a highly scalable system
- Scalability patterns enable us
  - Handle billions of requests / day
  - Process petabytes of data
  - Save costs

---

### Problem Description

Example

Digital News System with millions of customers reading the news on mobile / web.

When the incoming traffic, exceeds the CPU, memory or network capacity of a single server it will either crash or 
significantly degrate it's performance.
- upgrading that server to a more powerful VM will only delay this problem

---

### Load Balancing Pattern

- Load Balancing pattern helps us take advantage of "infinite" access to cloud computers


The Load Balancing as a pattern places a dispatcher between the source of the data (requests) and the workers that process that data

The dispatcher routes each request from the client to only one of the available workers
- if the number of clients grows we can add more workers and spread the load
- allows us to scale our system very quickly without changes needed to our system

---

### HTTP requests from the frontend to the backend

The requests all go to the load balancing dispatcher. 

The dispatcher routes each request to one of the application instances.

![Dispatcher](assets/01.png)

---

### Microservices Architecture

If we have a multi-service architecture, we can deploy each service as a group of identical application instances.

All the communication to that group of instances will also be through this dispatcher

This way we can **scale each service completely independently**

![Microservices](assets/02.png)

---

### Load Balancing - Implementation Techniques

### 1. Loading Balancing with Cloud Load Balancing Service

Based on the configured algorithms routes requests to a group of servers

If we deploy our servers instances in different isolation zones, then the load balancing service may also run as multiple instances
one in each zone

![Cloud Load Balancing](assets/03.png)


As the traffic to our system grows, that cloud load balancing service may scale itself up and run as a group of LB instances

Additionally, each Load Balance instance is monitored and automatically restarted if something happens
- No single point of failure

We can use Cloud Load Balancing Service internally between groups of services

---

### 2. Load balancing Pattern with Message Broker

We can have one or multiple publishers sending message to the message broker

On the receiving end, we can have a group of consumer workers that read those messages
- we can scale the consumer instances up / down as we need to

**Message Broker - Implementation Notes**
- The main purpose of a message broker is **not** load balancing
- Nevertheless, in the right use case it can do a good job at load balancing
  - When the comminication between the publisher and consumer service is **One directional / asynchronous**
  - The receiver doesn't need to / expect to get any response of the consumer after the message has been published
- Should be used **internally between services only**

Modern message broker solutions are implemented as distributed systems that use
- Replication
- Redundancy
- run as a group of instances

![LB with Message Broker](assets/04.png)

---

### Load Balancing Pattern - Implementation Considerations

- Which algorithm to use for routing requests to workers

---

### Load Balancing Pattern - Routing Algorithms

### Round Robin Algorithm

The Load balancer is routing each incoming request sequentally to the next worker instance
- Default Load Balancing algorithm
- Works well when the application is stateless
  - Each request can be handled by any application servers
- Doesn't work when we need to maintain an active session between the client and the server


**Authentication**

Example of active session:

After authenticating the user, we keep the user's credentials on the server side to make any subsequent request faster.
- if we use round robin, the client will have to re-authenticate every time

**Multipart File Upload**

The client upload a very big file which is broken down into multiple requests
- we need to make sure that all requests are going to the **same server**

---

### Sticky Session / Session Affinity Algorithm

The Load Balancer tries it's best to send traffic of a given client to the same server, as long as that server is healthy

Can be achieved by **placing a cookie** on the client's device which will be sent with every request to the Load Balancer

Another way is to inspect user's IP address
- every request can be tracked and routed to the same server
- works great for relatively short sessions

Too many connections ➡️ Disproportionally High Load

---

### Least Connections Algorithm

The Load Balancer will route new request to servers that have the least number of already established connections open

Particularly usefull for tasks associated with long term connections
- SQL, LDAP, etc

---

### Auto Scaling 

Every cloud computing instance such as a physical or VM, runs a few background processes called agents.

Agents collect data about the network traffic, memory consumption, CPU utilization on each host.

Using the real time data collected from each server we can define **Auto-Scaling Policies** 
to increase or decrease the number of servers dynamically base on those metrics.

Cloud ventors allows to tie those autoscaling policies to the Load Balancing service.

Load Balancing is aware of the size and addresses of the backend servers at any given moment.

**Combination**
- Dynamic resizing
- Load Balancing

allows us to auto scale up / down

---

### Summary

- Learned about the Load Balancing Pattern
- General overview - dispatching every request from a sender to **only one** worker
- Ways to implement Load Balancing
  - Cloud Load Balancing Service
  - Message Broker
- Strategies and algorithms based on
  - Statelessness / Statefulness of the application
  - Duration of the connection / session between the client and the server
- Combination between Auto-scaling and Load Balancing

---

## Pipes and Filters Pattern

Follows the analogy of a stream of water flowing through a series of pipes

A well designed filter performs only a single operation on the incoming data and it's completed unaware of
the rest of the pipeline

---

### Terminology

- **Data Source**
  - The origin of the incoming data
  - Examples: Backend Service / FaaS element
- **Data Sink**
  - The final destination of the data
  - Examples: Database / Distributed File System / External Service

---

### Problems Solved by Pipes and Filters Pattern

- Tight Coupling
  - Can't use different programming languages for different tasks
    - Machine Learning
    - Business Logic
    - CPU Intensive Workload
- Hardware Restrictions
  - Each task may require different hardware
    - Specialized Hardware
    - Extra CPU
    - Extra Memory
    - Fast Networking
- Low Scalability
  - Each task may require a different number of instances

---

### Example Application - Monolithic Approach

![Monolithic](assets/05.png)

⬇️

![Pipes and Filters](assets/06.png)

⬇️

![Pipes and Filters](assets/07.png)

⬇️

![Pipes and Filters](assets/08.png)

---

### Benefits of Pipes and Filters Pattern

- We can use a different programming language for each operation
- The entire pipeline can run optimally in terms of
  - Performance
  - Cost
- High Scalability
  - We can use a different number of instances for each operation
- High Troughput
  - Executing different operations in **parallel**

---

### Use Cases of Pipes and Filters Pattern

- Processing of User Activity data in digital advertising business
- Processing data from IOT (Internt Of Things) Devices
- Ingesting and Processing of Video and Audio files

---

### Video Sharing Service Architecture

When a new video is uploaded, it needs to go through a few stages, before it can be 
optimally streamed simultaneously to different devices in different geographical locations


![Video Sharing Service](assets/09.png)

---

### Important Considerations

- Pipes and Filters involves additional **overhead** and **complexity**
- Each Filter needs to be **stateless**, and should be provided with enough information as part of its input
- **Not** a good fit for **transactions** (where atomicity and data consistency are important)
  - Performing a Distributed Transaction across multiple independent components it's extremely difficult and inefficient 

---

### Summary

- Pipes and Filters following the analogy of water flowing through pipes, and getting filtered on the way downstream
- Problems solved by Pipes and Filters
  - Freedom to choose technologies / Programming languages for each operation
  - Running each operation on optimized hardware
  - Ability to scale each component as needed
- Walked through an example of ingesting / processing video / audio
- Important Considerations
  - Complexity and overhead
  - Statelessness of each processing component
  - Not a good fit for data *transactions*

---

## Scatter Gather Pattern

### Introduction

In the Scatter Gather Pattern the Dispatcher, routes the requests to **all the workers**

Later, the dispatcher
- gathers the response from all the workers
- Aggregates them into a single response
- Sends it back to the sender

---

### Load Balancing Pattern vs Scatter Gather Pattern

- In the Load Balancing pattern the workers are **identical**
- In the Scatter Gather pattern the workers are **different**
  - Same Application Instances - Different Data
  - Different Services - Different Functionality
  - External Services

---

### Core Principles

- Each request to a worker is **independent** from each other
- We can perform a large number of requests in **parallel**
- We can provide a large amount of information in a **constant time**

---

### Use Cases for the Scatter Gather Pattern

**Search Services - Internal Workers**

- Search query to Backend Service on Cloud
- Dispatch search query - as is - to a group of internal search worker instances
- Each instance search for the important keywords
  - with only a subset of documents, videos, images etc

Once each worker finishes performing the search on each own subset of data
- Sends back a response for aggregation on the backend server
- Aggregation Component within the Backend Server can
  - combine the data
  - rank each quality
- Sends one big response to the client (webpage)

![Scatter Gather Pattern - Search](assets/10.png)


---

**Hospitality Services - External Workers**

- Request for Hotels in Backend Service
- Backend Service sends it to a large number of hotels asking for a quote

Once we receive all the quotes
- We can send back a list of all the available hotels
- Sorted by price, star rating, any other criteria

![Scatter Gather Pattern - 3rd party services](assets/11.png)

---

### Observations

- Users are not aware of the number of workers
  - all the communication is done in parallel
- Users are not aware of whether the workers are
  - Internal
  - External
- Scatter Gather Pattern is a great choice for High Scalability

---

### Important Considerations

**Workers can become unreachable / unavailable at any moment**

- The dispatcher has to make sure it sets an upper limit for the time it waits for the response
- If the time limit has passed, we simply ignore them
- Aggregate with the data we already gathered
---

**Decoupling the dispatcher from the workers**

- If we use a message queue between the dispatcher and the workers
  - we can remove the tight coupling dependency
- Workers subscribe to **Queries Topic** (asynchronous communication between dispatcher and workers)
- Another **topic for workers publishing** the results
  - when a time has passed, the dispatcher can aggregate those results and send them to the user

![Scatter Gather Pattern - Using a message queue between dispatcher and workers](assets/12.png)

---

**The time between the request and the result**

- Immediate Result (<1000ms)
  - Dispatcher and aggregator into a single service works well

For tasks that take longer e.g. deep analysis / reporting, 
we can separate the dispatcher and the aggregator into different services 

For a user to be able to track that report / request
- The dispatcher generates a unique Identifier
- That identifier is sent back immediately to the user
- Also published together with the message to the workers
- When workers are done performing their part of the analysis they send the results to a **separate aggregation service** accompanied by that same identifier
- The aggregatinon service, can aggregate the results from the individual workers
- The aggregator can use that identifier as a key to store it's final result into it's own NoSQL database


![Scatter Gather Pattern - Separating dispatcher and aggregator into different services](assets/13.png)

End user can use the same id to talk to the aggregation service and easily retrive 
- either the current status
- get the final result

---

### Conclusion

- The Scatter Gather Pattern is very powerful
- It's used by many companies and services we use

---

### Summary

- General structure includes
  - Requester
  - Workers
  - Dispatcher / Aggregator
- 3 Use Cases for the Pattern, where the workers can be
  - Internal instances of the **same service** with access to **different data**
  - **Different services** with different functionality
  - **External services** that belong to other companies
- 3 Important Considerations
  - Upper limit on the time waiting for the responses
  - Decoupling between the dispatcher / aggregator and the workers
  - Using a unique identifier to allow efficient tracking of response and gathering the results, for long running reports

---



