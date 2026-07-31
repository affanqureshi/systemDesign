# Essential Network Protocols for Distributed Systems

Explore essential network protocols crucial for distributed systems design. 
Understand how HTTP enables stateless communication, and compare TCP and UDP transport 
protocols to balance speed and reliability. Learn the role of these protocols within the 
TCP/IP model to build scalable and robust distributed systems.

A distributed system’s greatest strength and weakness are that its components are separate. Without a robust communication 
backbone, it’s just a collection of isolated computers. The choice of how these components talk to each other is a critical 
decision in System Design, influencing a system’s performance, reliability, and scalability.

This lesson will break down the essential network protocols that enable this communication. Let’s start with a simple model.

<img width="873" height="213" alt="image" src="https://github.com/user-attachments/assets/126ad86f-f4dc-4bda-a20f-b8598f037265" />


#  HTTP as an architectural backbone
The Hypertext Transfer Protocol (HTTP) is the foundation of communication on the web. As an application-layer protocol,
it defines how clients and servers exchange messages. For System Design, knowing how HTTP works is essential for building APIs and web services.

The stateless nature of HTTP

# Core components of an HTTP request
An HTTP request sent by a client is made up of the following parts:

Methods: Verbs that define the desired action, for example, GET is used to retrieve data, POST to submit data, PUT to replace a resource, PATCH to apply a partial update, and DELETE to delete a record.

Headers: Key-value pairs that carry metadata about the request, such as authentication tokens or the format of the data being sent.

Body: The actual data being sent to the server, such as a JSON object in a POST request.

# Core components of an HTTP response
An HTTP response sent by the server includes:

Status code: A three-digit code that indicates the outcome of the request (e.g., 200 OK, 404 Not Found, 500 Internal Server Error).

Headers: Key-value pairs that carry metadata about the response, such as caching instructions or the content type.

Body: The actual content being returned. Note that some responses (like a HEAD request or a 204 No Content status) intentionally have no body.

<img width="920" height="572" alt="image" src="https://github.com/user-attachments/assets/818017e6-d8f0-47f1-8e5f-32a2c6f9eae8" />

<img width="1049" height="484" alt="image" src="https://github.com/user-attachments/assets/1a70cee0-a72e-4a0a-bd19-2659e29382e3" />


RPC/gRPC: Remote Procedure Calls make services feel like they’re calling local functions. gRPC (Google’s RPC framework) is a modern, 
high-performance option built on HTTP/2 that uses a binary format for very efficient data exchange between internal services.

WebSockets: This protocol enables a stateful, full-duplex (two-way) connection. It’s essential for real-time applications 
like chat apps or live notifications, where the server needs to push data to the client without waiting for a request.

Comparing TCP and UDP for distributed systems#
At the transport layer, engineers often face a trade-off between reliability and speed. Two core protocols 
embody this trade-off: Transmission Control Protocol (TCP) 

<img width="990" height="525" alt="image" src="https://github.com/user-attachments/assets/fc0bab12-f523-4ac7-8733-6cec700167a5" />

and User Datagram Protocol (UDP). The following diagram illustrates the fundamental difference in their communication styles.

<img width="1039" height="273" alt="image" src="https://github.com/user-attachments/assets/ddf33521-5152-4f15-a35f-234a9a68885f" />






# TCP/IP model
The seven-layer OSI Model is often taught as the theoretical standard for networking. But in practice, most real-world systems, including the internet, use the simpler TCP/IP model.

# The OSI Model
The TCP/IP model has four layers, but in System Design, you’ll primarily work with the top three:

Application layer: This is where user-facing protocols live. It's where you’ll make decisions about HTTP, gRPC, APIs, and data formats like JSON.

Transport layer: This layer handles end-to-end communication. As we’ve discussed, you choose TCP (for reliability) or UDP (for speed) here.

Network layer: This layer handles addressing and routing packets across networks using the Internet Protocol (IP).

Link layer: This is the physical foundation (e.g., Ethernet, Wi-Fi) that the other layers are built on, but it’s rarely a direct concern in application design.

The following diagram shows how these layers work together in a real-world flow.

<img width="934" height="467" alt="image" src="https://github.com/user-attachments/assets/e8624cb8-b828-4ee8-b38f-e89afc1094ef" />

Understanding these layers is invaluable for troubleshooting. When a service is unreachable, 
is it an application error (application layer), a blocked port (transport layer), 
or a routing problem (internet layer)? This layered thinking helps narrow down the possibilities.

The following table summarizes the TCP/IP layers and their relevance to System Design.

<img width="1039" height="264" alt="image" src="https://github.com/user-attachments/assets/23463fde-b012-45a4-98dd-21fad1ff9a15" />


# Synchronous vs. Asynchronous Communication in Distributed Systems

In synchronous communication, the sender dispatches a request and must wait for a response from the receiver before continuing its own execution.

This waiting period is why it’s known as a blocking communication method. This interaction model is often compared to a phone call, 
where both parties must be present and engaged in the conversation simultaneously. The client is temporarily coupled to the service it calls, awaiting a direct answer.

Asynchronous communication
In asynchronous communication, the sender dispatches a message and immediately continues with other tasks instead of waiting for a response. Because the sender’s 
execution is not paused, this is known as a non-blocking method. This model is like sending an email or a text message: you send the message and then move on 
to other activities, confident that the receiver will see it when they are available. The client and server are decoupled, allowing them to operate independently.

This paradigm is often implemented using a middle layer known as a message broker, which temporarily stores messages until they can be processed by the receiver.
Popular examples of message brokers include Apache Kafka and RabbitMQ. The following diagram shows how a message broker enables this non-blocking communication:


<img width="839" height="378" alt="image" src="https://github.com/user-attachments/assets/81eb750b-5f00-4259-9b3a-c46d74f87a3c" />



