# Section 5: Reliability, Error Handling and Recovery Software Architecture Patterns

- [Throttling and Rate Limiting Pattern](#throttling-and-rate-limiting-pattern)
- [Retry Pattern](#retry-pattern)

---

## Throttling and Rate Limiting Pattern


### Problem Statement

**2 main problems this pattern helps us address**

- Overconsumption of resources in our system
  - our system can't handle this high rate of requests / data
    - service instances run out of CPU / Memory
    - violates the service level agreement for all clients ➡️ financial implications
  - can respond quickly and scale out our system using Load Balancer and auto scaling policies
    - scale out costs way more money
- Overconsumption of external system / external API
  - Example: Big Data Analysis
  - perform analysis as batch processing job (e.g. trends in user behavior of a social media app)
    - Generate recommendations for users
  - we may consume storage resources / network of cloud provider
  - we may use an external ML API

**Overconsumption - Conclusions**

- We need to protect our systems against traffic spikes
- Overconsumption of external resources may end up costing us a lot of money

**Solution**: **Throttling / Rate Limiting Pattern**

---

### Throttling Pattern

We set a limit on the **number of requests** that can be made during a period of time

![Throttling based on number of requests](assets/94.png)

We can also impose limits **on the bandwidth and set a maximum number of MBs / GBs** of data that can either be sent or read from our system at a given period of time

This period can be
- / second
- / minute
- / day etc

![Throttling based on bandwidth](assets/95.png)

When we are the service providers, we need to protect our system from over consumption
- Server Side Throttling


When we are the clients, calling external services and we want to protect ourselfs from overspending
- Client Side Throttling

---

### Throttling & Rate Limiting - Open Questions


- What should we do if a client exceeds a set limit?

---

### Strategies for Handling a Client Exceeding the Limit

- Dropping Requests
  - Send back a response with error code 429 - "Too many requests"
- Queue up the requests and process them later
- Reduce Quality / Limit Bandwidth

---


### Example: Stock Trading Company

**Dropping Requests**

We can throttle the number of requests / sec a client can send us to get the price of a stock.

If the client attempts to send us more requests, we can simply drop and ignore them on the server side.

**Queue up**

We can simply queue those requests either in a log or a message broker and then execute those trades at a pace that does not exceed the limit we set for that client
- we simply slow down or degrate the service we provide to that client


![Throttling with a queue](assets/96.png)

We can combine those strategies and set a limit on the number of trades we can perform per day, and if that daily limit is exceeded we start dropping those requests.

This will prevent a situation where the client keeps sending us more and more trading requests, which may in turn overload the capacity of our queue

---

### Example: Streaming Platform

We can throttle the client by reducing the resolution or bitrate of the video / audio.
- we essentially throttle the bandwidth without denying the service

If we detect that the client is maliciously trying to consume too much data, we can also set an upper limit on the number of movies or songs that they can watch or listen too

---

### Throttling & Rate Limiting - Important Considerations

- Whether to throttle on
  - API basis
  - Customer basis
- Multi-service throttling

---

### Global Rate Limit

We can look at the API endpoint and set a global rate limit for all the clients together to that endpoint.

The benefit of this approach is we can easily guarantee that our system does not go over the request rate that we can handle or budgeted for

The obvious downside is that one client can suddently send us a very high number of requests and therefore unfairly deprive the other clients of getting service

![Throttling global limit](assets/97.png)


On the other hand we can implement throttling on a per customer basis.

This way we guarantee that each customer gets a fair share of our resources and their level of service is isolated and independent from other customers.

The downside of this approach is now a lot harder for us to control the total request rate from all the customers
- especially gets harder if we constantly get new customers
- can also get very complex if we have multiple tiers of customers (Premium, Basic, Free) where each customer get different quotas based on the level of subscription


![Throttling per customer](assets/98.png)

---

## Example: Stock Trading Company with multi-service throttling

![Throttling mutli service throttling](assets/99.png)


Each client can request a price of either one or multiple stocks / bonds or other asset classes, as a **single** request to our API endpoint.

Internally, that single request can result in multiple concurrent requests to different services

If we only throttle externally on an API basis or customer basis the we may end up overwhelming different parts of our system at different times depending on the workload - complex throttling.

---

### Summary

- Throttling - Setting a limit of the:
  - Number of requests
  - Amount of data can be sent / received per unit of time
- Types of throttling
  - Client Side Throttling
  - Server Side Throttling
- Important considerations
  - Customer base throttling vs Global (API level) throttling
  - External throttling vs Service based throttling
- Conclusion: The best approach depends on
  - Use-case
  - Requirements 

---

## Retry Pattern

### Problem Statement

![Retry Pattern - Problem Statement](assets/100.png)

---

### Cloud Environment Issues

- Software / Hardware / Network errors introduce
  - Delays
  - Timeouts
  - Failures

**Two scenarios**
- 1. Successful Response
- 2. Error or Timeout

---

### Error Categorization

The first thing we need to do in the second scenario is error categorization
- User Error
  - HTTP 403 (Not Authorized)
    - We simply send the error back to the user: Error Info
- System Error
  - We need to try our best to hide the error from the user and recover if possible

Recover from system error solution: **Retry Pattern**

---

### Retry Pattern

We simply retry the same operation by resending the same request to the remote server until
we get a successful response

![Retry Pattern](assets/101.png)

If we do get a successful response in a reasonable amount of time then we succeeded in hiding our internal issues
from the user

---

### Retry Pattern - Important Considerations

- Which errors to retry?
- What delay / backoff strategy to use?
- Adding randomization / Jitter between retries

---

### Which Errors to Retry?

Only if we have reason to believe that the error is
- **Short**
- **Temporary**
- **Recoverable**

Example: HTTP 503 (Service Unavailable: Busy or down temporarily) 

Request may end up being routed by the load balancer to a different instance.

![Retry Pattern Load Balancer](assets/102.png)

If the request ends up to the same instance there is a high chance that the instance has already recovered

---

### Retry Storm

> A situation that can cause unrecoverable cascading failure in the system

Example: 2 instances encountered failures and are in the process of getting fixed and restarted

For some duration are not available
- All the requests to them will fail
- Calling service will start sending retries
- Will be routed by the load balancer to the rest of the services

If we don't implement a delay or backoff in between retries, we may end up overwhelming the healthy instances
with more requests that they can handle
- This in turn will cause more timeouts and errors
- Will result in more retries

We can get to a point that the entire service goes down

Solution: Add a delay betweeen subsequent retries

---

### Retry Pattern - Backoff Strategy

- Allows the faulty service to fully recover
- Strategies
  - Fixed Delay
  - Incremental Delay
  - Exponential Backoff
- Note: The approach you choose depends on your system

![Retry Pattern Backoff Strategy - Fixed](assets/103.png)

![Retry Pattern Backoff Strategy - Incremental](assets/104.png)

![Retry Pattern Backoff Strategy - Exponential](assets/105.png)


---

### Retry - Jitter

> RetryDelay(i) = i*(100 + rand(-15, 15))[ms]

All instances of one service see that instance crash at the same time

We may start sending retry requests to the rest of the remote service instances
- in complete or almost complete synchronization

Even with fixed or exponential delay - No delay randomization causes high load

---

### Synchronized Retries

![Retry Pattern - Synchronized retries](assets/106.png)

---

### Retries with Jitter

![Retry Pattern - Retries with Jitter](assets/107.png)

---











