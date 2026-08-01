# API Architectural Styles: REST, GraphQL, RPC 
Understand the key API architectural styles REST, GraphQL, and gRPC used in distributed systems. 
Learn how they differ in handling communication, efficiency, and flexibility to choose the ideal style based on your system’s needs.

In any complex application, from a ride-sharing service tracking vehicles to a streaming platform suggesting our next show, 
countless independent services must communicate seamlessly.

The effectiveness of this communication has a direct impact on system performance, scalability, and maintainability.
Choosing the right approach for these services to communicate with each other is one of the most critical decisions in building robust distributed systems.

This decision, API architectural styles, is a frequent topic in System Design interviews, where understanding the trade-offs is key. 
This lesson explores three dominant API architectural styles: REST, GraphQL, and gRPC. Let’s start by understanding what an architectural style means.


# API Architectural Styles

An API Architectural Style defines the rules and standards for communication between clients and services.

The three most common API styles are:

REST
GraphQL
gRPC

<img width="1037" height="475" alt="image" src="https://github.com/user-attachments/assets/48026533-d977-4fd0-9200-3d5754fae40b" />


# The RPC and evolution to modern APIs
The earliest challenge in distributed computing was making network communication feel simple.

Developers needed a way to execute code on a remote server as easily as calling a function locally.
This led to the creation of the Remote Procedure Call (RPC). An RPC call works by abstracting the network layer.
The client application makes what appears to be a normal function call.

This call is intercepted by a stub, which is a piece of code that acts as a local proxy for the remote service.

Under the hood, the client-side stub serializes the function parameters into a message and sends it across the network.
A server-side stub receives this message, deserializes it, and calls the actual function on the server. The result is then sent back to the client in the same way.


<img width="1065" height="341" alt="image" src="https://github.com/user-attachments/assets/9d1041ac-352e-47be-942d-daea0fc329f2" />



# REST (Representational State Transfer)

## Overview

REST is a **resource-based** architecture that uses standard HTTP methods.

<img width="1102" height="309" alt="image" src="https://github.com/user-attachments/assets/e67851e2-e6fa-4192-91b2-d18174bad7e5" />

### HTTP Methods

| Method | Purpose |
|---------|----------|
| GET | Read Data |
| POST | Create Data |
| PUT | Update Data |
| DELETE | Delete Data |

---

## Characteristics

- Stateless
- Resource-Oriented
- Uses HTTP
- JSON Response
- Easy to Debug
- Widely Adopted

---

## Advantages

- Simple architecture
- Easy to learn
- Highly scalable
- Browser & Postman friendly
- Great for Public APIs

---

## Limitations

### Over Fetching

Returns unnecessary data.

```
Need:
- Name
- Price

Returns:
- Name
- Price
- Description
- Reviews
- Stock
- Images
```

### Under Fetching

Requires multiple API calls.

```
GET /users/1
GET /orders/1
GET /payments/1
```
# GraphQL

## Overview

GraphQL is a query language where the client requests **exactly** the required data.

Instead of multiple endpoints, GraphQL exposes **one endpoint**.
<img width="976" height="622" alt="image" src="https://github.com/user-attachments/assets/a10ef975-2920-4fa9-95e9-cf65530dd17d" />

```
POST /graphql
```

---

## Characteristics

- Single Endpoint
- Client Controls Response
- Strong Schema
- Flexible Queries

---

## Advantages

- No Over Fetching
- No Under Fetching
- Single Request
- Excellent for Mobile Apps
- Great for React & Angular

---

## Limitations

- Difficult HTTP Caching
- Complex Security
- N+1 Query Problem


# gRPC

## Overview

gRPC (gRPC Remote Procedure Call) is a modern, open-source RPC framework developed by Google.

It is designed for high-performance, low-latency communication between microservices. gRPC achieves high performance by leveraging HTTP/2 to
create logical subchannels to offer the following benefits for client-server communication:

Efficiency:
gRPC utilizes Protocol Buffers (Protobuf) as its data format, replacing JSON, which reduces payload size and improves transmission speed.

Multiplexing:
It utilizes HTTP/2, allowing multiple requests to be sent simultaneously over a single connection.

Strict schema: 
Protobuf provides a strict schema, minimizing data communication errors.

Streaming support: 
Enables bidirectional streaming, which is beneficial for real-time applications like chat apps or live collaboration tools.

Language agnostic:
Works with multiple programming languages, making it flexible for cross-platform applications.

---

## Features

- HTTP/2
- Protocol Buffers (Protobuf)
- Binary Serialization
- Bidirectional Streaming
- Strong Schema
- Multi-language Support

---

## Advantages

- Very Fast
- Low Latency
- Small Payload
- Type Safe
- Best for Internal Communication

---

## Limitations

- Binary Format
- Hard to Debug
- Not Human Readable
- Limited Browser Support

<img width="1053" height="417" alt="image" src="https://github.com/user-attachments/assets/b1c5d576-31c7-4776-9b39-163d6f4192fa" />

