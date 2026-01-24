# Section 4: Software Extensibility Architecture Patterns

- [Sidecar & Ambassador Pattern](#sidecar--ambassador-pattern)

---

## Sidecar & Ambassador Pattern


## Problem Statement

**Core Functionality of Services**

- Web Server
  - Server web content
- Customers Service
  - Store customer info.
- Payment Service
  - Collect payments

**Additional Functionality** beyond it's core business logic

- Web Server
  - Internal metrics
  - Log events
  - Connect to registry
  - Pull config
  - ...
- Customers Service
  - Internal metrics
  - Log events
  - Connect to registry
  - Pull config
  - ...
- Payment Service
  - Internal metrics
  - Log events
  - Connect to registry
  - Pull config
  - ...

---

### Code Reusability - First Attempt (Library)

We can reuse a library in each codebase

**Issue**: We may need to utilize different programming languages for different problems

We can't use the same library across multiple services

**Additional Issues with a Shared Library**

- Scalability
- Incompatibility or inconsistency between different languages
  - Different data types
  - Bugs in different versions of implementations
- Deploying shared functionalities as separate services
  - Overkill and problematic


**Best solution**: Sidecar Pattern

---

### Sidecar Pattern

We take the additional functionality and run it as a separate process / separate container
on the same server as the main application

**Benefits**

- Isolation between main server and sidecar process
- Still share the same host
  - Fast, Reliable Communication
- Main application + sidecar Process have access to same resources
  - File system
  - CPU
  - Memory

![Sidecar](assets/81.png)

This way the sidecar can
- Monitor the CPU / memory
- Report it
- Can also read the application log files
- Update it's configuration files easily (no network communication)

Also the isolation we get by running the sidecar as a separate process
- Allows us to implement the sidecar in one language once and reuse it
- Deploy one upgrade to all instances

![Sidecar 2](assets/82.png)

---

### Ambassador Sidecar Pattern

An ambassador is a special sidecar that is responsible for sending all the network requests on behalf of the service

It is like a **Proxy** but it runs on the same host as the core application

**Benefits**
- We offload all the complex network communication logic outside of the service
- Codebase of the core service has only Business Logic

**Ambassador responsibilities**
- Retries
- Disconnections
- Authentication
- Routing
- Protocol Versions

We can perform distributed tracing across multiple services

Example: Troubleshoot a transaction that spans multiple services

---

### Summary

- Sidecar Pattern extends the functionality of a service
  - No need to **reimplement** in every programming language
  - No need to **deploy** as a service on additional hardware
- Sidecar benefits
  - **Isolation** between the sidecar and the core application
  - Has **access** to the same **resources**
  - **Low overhead** of interprocess communication
- Ambassador Sidecar Pattern offloads
  - **Network** communication
  - **Security** from the core application to the sidecar

---





