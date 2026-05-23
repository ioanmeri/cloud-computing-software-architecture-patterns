# Section 6: Deployment and Production Testing Patterns

- [Rolling Deployment Pattern](#rolling-deployment-pattern)
- [Blue-Green Deployment Pattern](#blue-green-deployment-pattern)

---

## Deployment and Testing

- There's no value in "theorizing" about Highly Scalable / Fault Tolerant architectures without knowing how to:
  - Deploy
  - Upgrade
  - Test
them in production

---

### Problem Statement

Typically for upgrades it's required some downtime window, during that window we can:
- Shut down application instances
- Replace them with the new version

However, if for some reason, something goes wrong and the new version can't start up, 
we would need to shut those instances down and **Downgrade**
- Works fine if we can find a **No Traffic window**
- not an option for 24/7 busy applications

---

### Rolling Deployment Pattern

Instead of taking down all the servers, and deploying a new version on them,
- we use the **Load Balancing** service to stop sending traffic to the application servers
- one at a time

Once, no more traffic is going to a **particular server**:
- we can stop the application instance on it
- deploy a new instance with the new version

![Rolling Deployment Pattern](assets/115.png)

we can even run some tests if we want to

Then, we add that server back to the load balancer's group of backend servers
- we repeat the same process on all the servers

We can rollback the release, if we see errors in dashboards (steps to the reverse)

![Rolling Deployment Pattern](assets/116.png)

----

### Benefits

- No System Downtime
- Gradually release a new version - safer
- Fast and cheap to release a new version
  - No need to provision extra Hardware

---

### Downsides

- There is no isolation between the new old version servers
  - Risk of starting a cascade of failures
- Traffic will be sent to the remaining healthy old version
  - Old version has to handle more load (may cause them to start failing)

If the API has changed drastically, having two versions of the same service may cause issues

---

### Rolling Deployment - Conclusion

- Rolling Deployment is
  - One of the most popular deployment patterns
  - Used by many companies and projects

---

### Summary

- Rolling Deployment benefits
  - No downtime
  - No additional cost for hardware
  - We can rollback quickly if something goes wrong
- Downsides
  - Potential for Cascading Failures
  - 2 versions in production at the same time

---

## Blue-Green Deployment Pattern

### Problem Statement

Rolling Deployment: One instance at a time -> keep rolling until entire service has new version
- Cascading failures
- 2 versions in production

---

### Blue / Green Deployment

- Blue Environment
  - we keep the old version of our application instances running **throughout the entire duration** of the release
- Green Environment
  - deploy the new version on new servers

after we verified that the instances
- started up fine
- run a few tests on them

> We use the **load balancer to shift traffic** from the blue to the green environment

If during this transition we start seeing issues in logs / monitor dashboard, we can easily stop the release and shift the traffic back to the blue environment (old version).

![Blue Green Deployment](assets/117.png)

Otherwise, we fully transfer the traffic to the new version and shut down the old environment (or keep it available for the next release)

---

### Blue-Green Deployment - Benefits

- We have an equal amount of servers for Blue and Green environemnts
  - if green environment fails, we can switch back to blue
  - can take the full load of traffic
- We can have a single version of the software at any given moment

---

### Blue-Green Deployment - Downsides

- We need twice as many servers as we normally need
  - in cloud we need to wait until all those servers start up
  - pay for additional hardware
  - if we use the **servers only for the release** - cost may not be high

> Most popular choice for releasing new version

---






