# Section 5: Reliability, Error Handling and Recovery Software Architecture Patterns

- [Throttling and Rate Limiting Pattern](#throttling-and-rate-limiting-pattern)

---

## Throttling and Rate Limiting Pattern


## Problem Statement

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

