# What is System Design?

System Design = Blueprint of a software system.

It defines:
- Components
- Communication
- Data Flow
- Scalability
- Reliability
- Trade-offs

---

# Goal

Design systems that are:

- Scalable
- Reliable
- Available
- Fast
- Fault Tolerant

---

# Why System Design?

Application that works for 1,000 users may fail for 1,000,000 users.

System Design prepares the system for:
- High Traffic
- Failures
- Large Scale
- Low Latency

---

# Core Qualities

✅ Scalability → Handle more users

✅ Availability → Service stays online

✅ Reliability → Correct results

✅ Low Latency → Fast response

✅ Consistency → Same data everywhere

---
# Why System Design matters

Every successful technology company eventually faces the challenge of scale. Without a deliberate architectural strategy, an application that works for 1,000 users will likely fail under the load of 1,000,000 (a million) users.
System Design directly addresses these challenges by focusing on building systems that are not only functional but also resilient and efficient at a massive scale.
Thoughtful design anticipates and solves complex problems before they impact service quality. This means focusing on key system qualities:

**Scalability**: The ability to handle massive user loads by distributing requests so no single server is overwhelmed.

**Availability**: Ensuring the system remains operational and accessible to users even if some of its components crash.

**Low latency**: Responding to user requests quickly, regardless of their location, to create a fast and responsive experience.

**Consistency**: Making sure data is reliable and accurate across the entire distributed system.

Educative byte: In June 2019, a Google Cloud outage was triggered by a network configuration error that spread across multiple regions, disrupting services such as YouTube and Gmail. The lesson: resilient designs must contain the blast radius of even routine operational tasks.


<img width="800" height="349" alt="image" src="https://github.com/user-attachments/assets/ca9006db-77ce-4a09-b7a8-bffaf518ad02" />

The diagram above illustrates a common scalability pattern.

A load balancer distributes incoming traffic, multiple servers handle requests in parallel, a cache accelerates data access, and a replicated database ensures that information remains both durable and quickly retrievable. Each component represents a deliberate choice within the system design process.

Now that we’ve covered the what and the why, it’s important to distinguish this design workflow from the act of coding itself.



# System Design vs Coding

Coding
- Write code
- Algorithms
- Classes
- Functions
- Fix bugs

System Design
- Design architecture
- APIs
- Databases
- Caching
- Load Balancing
- Scaling

👉 Coding = Build a room
👉 System Design = Design the whole building

A strong System Design approach introduces benefits such as modularization, which enables teams to work independently, and abstraction layers, 
which simplify maintenance and upgrade processes. Understanding this distinction is a key differentiator in any software engineering career, 
especially in a System Design interview.

A direct comparison helps clarify this distinction.

<img width="941" height="205" alt="image" src="https://github.com/user-attachments/assets/f3364a76-c70d-40f9-8608-7103b7ab9893" />


This table highlights the shift in perspective from micro-level implementation to macro-level strategy. Since System Design is a broad discipline, understanding who is responsible for it within an organization is key.

---

# System Design Answers

Instead of asking:

"How do I write this function?"

Ask:

"How will this system handle 10 million users?"

---

# Common Components

- Client
- API
- Load Balancer
- Servers
- Cache
- Database
- Queue
- CDN

---

# Real World Example

Netflix Live Stream

Problem:
- Millions joined together
- Servers overloaded
- Buffering
- Audio/video issues

Lesson:
Good System Design prevents failures.

---

# Key Trade-offs

- Cost vs Reliability
- Latency vs Consistency
- Simplicity vs Flexibility

---

# Interview Definition

System Design is the process of designing scalable, reliable, highly available, and maintainable software systems that can handle real-world traffic and failures.

---

# One-Line Memory Trick

Blueprint ➜ Components ➜ Scale ➜ Reliability ➜ Trade-offs

 
# Framing System Design and Software Architecture


System Design is the process of defining the components, modules, interfaces, and data for a system to meet its requirements. It offers a high-level view that includes hardware, software, network infrastructure, and the services that connect them. Think of it as deciding you need a web server, a database, and a caching layer, and determining how they will interact with each other.

Software Architecture, on the other hand, is a subset of System Design. It focuses on the internal structure of the software itself. It involves organizing code into modules, defining how they interact, and choosing architectural patterns. Suppose System Design decides that you need a database. In that case, Software Architecture determines how the code will interact with it, which data models to use, and how to structure the code for maintainability and performance.


<img width="772" height="304" alt="image" src="https://github.com/user-attachments/assets/b3bb6ba5-aae2-4304-820c-45e017767e0c" />


# Where System Design and architecture intersect and diverge#

System Design and Software Architecture are deeply interconnected, not mutually exclusive. A system’s overall performance and reliability depend on both sound internal architecture and a robust external design. The key is understanding which hat to wear when making a decision.

When building a photo-sharing app, here’s how the concerns are divided:

<img width="832" height="264" alt="image" src="https://github.com/user-attachments/assets/d752607d-89ab-453e-b4a3-8ce3b3665050" />


# Monolithic vs. Microservices Architecture

# Monolithic architecture
A monolithic architecture is the traditional model of building an application as a single, indivisible unit. All components, from the user interface and business logic to the data access layer, are developed, deployed, and scaled together.

<img width="583" height="176" alt="image" src="https://github.com/user-attachments/assets/0e2cd210-cf7e-4082-93ef-6b9d1cdf8538" />

# Modular monoliths (structured flexibility)#

A modular monolith is an evolution of the traditional monolith.

It remains a single deployable unit but is internally structured into distinct, independent modules. Each module is responsible for a specific business domain, like user management, payments, or inventory. While they share the same runtime and database, they communicate through well-defined, internal APIs or interfaces.


<img width="798" height="496" alt="image" src="https://github.com/user-attachments/assets/7e836c62-74f0-458a-a41f-42ada48688d7" />

# Microservices architecture#
In a microservices architecture, an application is broken down into a collection of small, autonomous services.

Each service runs independently and communicates with others over a network, typically using APIs. This is akin to replacing a single large restaurant with a food court, where each stall operates as an independent business.

Instead of containing all responsibilities in a single monolith, microservices split them across multiple services.


<img width="847" height="416" alt="image" src="https://github.com/user-attachments/assets/d771c941-b1a5-4e22-8ec5-eebfa61a43a9" />


# Now, let’s compare these architectures side by side to better understand their respective trade-offs.

<img width="1010" height="414" alt="image" src="https://github.com/user-attachments/assets/aacdeec3-13ad-4687-9228-327151254675" />



# Functional vs. Non-Functional Requirements in System Design


## Functional Requirements (FR)

**Definition:** What the system does (Features).

### Examples
- User Sign Up
- Login
- Upload Photo
- Send Message
- Add to Cart
- Make Payment

### Memory Trick

**FR = Features = What**

---

## Non-Functional Requirements (NFR)

**Definition:** How well the system performs.

### Examples
- Scalability
- Availability
- Reliability
- Security
- Low Latency
- Throughput
- Fault Tolerance
- Consistency

### Memory Trick

**NFR = Quality = How**

<img width="752" height="410" alt="image" src="https://github.com/user-attachments/assets/587ada63-743b-40f6-aba5-a686006643f9" />

---

## Interview Formula

**FR → What?**

**NFR → How Well?**

---

## Example (WhatsApp)

### Functional Requirements

- User can Sign Up
- User can Login
- Send Message
- Receive Message
- View Chat History
- Show Online Status
- Receive Notifications

<img width="667" height="616" alt="image" src="https://github.com/user-attachments/assets/1f34853b-b091-4cdf-8365-5577e476f0b0" />


### Non-Functional Requirements

- 99.99% Availability
- <100ms Latency
- End-to-End Security
- Millions of Concurrent Users
- Fault Tolerance

---

## Payment Gateway Example

### Functional

- Make Payment
- Refund Payment
- Check Transaction Status

### Non-Functional

- 99.999% Availability
- No Duplicate Transactions
- Low Latency
- PCI DSS Compliance
- Strong Security

---

## Design Impact

| Functional | Non-Functional |
|------------|---------------|
| Features | Quality |
| What | How |
| Business Need | Performance Goal |

---

## Common Trade-offs

- **Consistency vs Availability**
- **Latency vs Security**
- **Cost vs Reliability**
- **Performance vs Accuracy**

---

## Interview Tip

Always ask these questions first:

### Functional
- What features are required?
- What is in scope?
- What is out of scope?

### Non-Functional
- Expected Users?
- Expected RPS?
- Latency?
- Availability?
- Consistency?
- Security?

---

## Memory Formula

**FR → WHAT**

**NFR → HOW**

**Design = FR + NFR**
