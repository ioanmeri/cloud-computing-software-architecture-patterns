# Section 5: Reliability, Error Handling and Recovery Software Architecture Patterns

- [Throttling and Rate Limiting Pattern](#throttling-and-rate-limiting-pattern)

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

---


### Example: Stock Trading Company

**Dropping Requests**

We can throttle the number of requests / sec a client can send us to get the price of a stock.

If the client attempts to send us more requests, we can simply drop and ignore them on the server side.









