# Section 1: Introduction

## Introduction to Cloud Computing Software Architecture Patters

### Types of System Requirements

- **Functional Requirements**
  - Dictate the funcionality / features of the product
- **Non-Functional Requirements (Quality Attributes)**
  - Performance, Scalability, Availability, Reliability...
- System Contraints
  - Limite our design choices

---

### Unique Features vs Common Requirements

**Features**

Example:
- Ride Sharing Company
  - Get a Ride
  - Reserve Ride
  - Choose Car
- Video-On-Demand Company
  - Watch Movie
  - Save to Favorites
  - Pause / Resume
- Online Gaming Company
  - Choose Character
  - Save Game
  - Chat

It's the features that we want to deliver, that differentiate our system from it's competitors and any other system

It's the business logic and the UX that defines our software product

**Quality Requirements**

Companies are facing the exact same challenges and they can benefit from the same solutions

---

### Unique Features vs Common Requirements - Continues

Examples
- Online Store Company
- Education Platform Company
- Online Dating Company

No Common Features / Functionality

But all three have
- Performance
  - User Interface
- Scalability
  - Millions of Customers
- Data Processing and Querying
  - Store Business Data
  - Big Data Processing
  - Query / Display User Data

---

### Startup Example: Unique Features vs Common Struggles

Examples
- User Facing socia media product
- Online productivity tools

both of those companies are in need of Organizational Scalability
- Migration to Microservices

They can benefit from the same
- Scalability
- Extensibility

also all companies nowadays are expected to:
- have high Availability
- Reliability

> Common Patterns that we can apply to any system

---

### Software Architecture Patterns

- Universal
- Successfully used by many companies
- Implementation Independent

---

### "Design Patterns" vs Software Architecture Patterns

**Design Patterns**

- "Design Patterns: Elements of Reusable Object-Oriented Software"
  - by Erich Gamman, Richard Helm, Ralph Johnson, John Vlissides * 1994
  - Targeted towards
    - Standalone applications
    - Implemented with OOP languages (Java, C#, C++, ...)
- "Design Patterns" benefits
  - Better code organization
  - Reusability
  - Extensibility
  - High developer productivity
- "Design Patterns" limited to
  - Single application / library
  - Object Oriented languages

"Design Patterns" **are not very helpful** in architecting
- A Large Scale System
- Using multiple independently deployable services
- Implemented in different languages (some not Object Oriented)

"Design Patterns" **don't address**
- Performance
- User experience
- Error handling
- Processing big data or high network traffic

---

### Cloud Computing Pattern - Benefits

- Access to (virtually) infinite
  - Computation
  - Storage
  - Network capacity

commoned reffered to as **Infrastructure as a Service (IaaS)**

- Without the IaaS every company would have to
  - Hire specialists
  - Build infrastructure

Additionally
- Cloud's pricing model charges **only** for
  - Services we **use**
  - Hardware we **reserver**
- **Removes entry barrier for new companies**

---

### Cloud Computing Pattern - Additional Benefits

- Tools / Features to improve
  - Performance
  - Scalability
  - Reliability
- Examples
  - Multi-Region
  - Multi-Zone deployment

By spreading our application instances across multiple zones, if one zone loses power or internet connection
our system can continue operate

---

### Cloud Computing Pattern - More Benefits

- In addition to Infrastructure such as
  - Virtual machines
  - Network routers
  - Switches
  - Storage devices
- Platform with services like
  - Databases
  - Message Brokers
  - Load Balancers
  - Monitoring
  - Log Services
  - Function as a Service (FaaS)

---

### Disadvantages and Liminations of Cloud Computing

- We don't own our system's infrastructure
- cloud Infrastructure is out of our direct control
  - **Not** the highest end hardware
  - **Not** the newest hardware


**Concerns of a Software Architect**

- Availability
- Performance
- ...
- Cost Efficiency
- Profitability

---

## Cloud Software Architect's Job

> Building **Reliable** Systems using **Unreliable** Components


---

## Summary

- Motivation to Software Architecture Patterns
  - Solutions to common problems and non-functional requirements
- Object Oriented "Design Patterns" solve a different problem
  - Code organization in a single application / library
- Unique features of Cloud Computing
  - Which we will take advantage of
- Disadvantages / Limitations of Cloud Computing
  - Which we will learn to address

---
