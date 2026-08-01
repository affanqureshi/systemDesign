# Communication Patterns in Distributed Systems



# Table of Contents

- Pull Communication
- Push Communication
- Polling
- Long Polling
- WebSockets
- Webhooks
- Server-Sent Events (SSE)
- Heartbeats
- Timeouts
- Comparison Table
- Interview Cheat Sheet

---

# Pull Communication

## Definition

The client requests data from the server.

<img width="1025" height="612" alt="image" src="https://github.com/user-attachments/assets/5b8fe6fa-1cde-4af7-b24c-671e7f87dc25" />


```
Client --------> Server
       Request

Client <-------- Server
      Response
```

### Characteristics

- Client initiates communication
- Stateless
- Easy to implement
- Higher latency

### Use Cases

- Weather App
- Dashboard
- News Feed

---

# Polling

Client continuously checks the server at fixed intervals.

```
Every 5 Seconds

Client --> Server
Any Updates?

Server --> Client
No

Client --> Server
Any Updates?

Server --> Client
Yes
```

### Pros

- Simple
- Easy to scale

### Cons

- Wasted requests
- Higher bandwidth usage
- Increased latency

---

# Long Polling

The client sends one request.

The server waits until new data becomes available.

```
Client -------> Server

(Server waits...)

New Data Available

Server -------> Client
```

### Pros

- Lower latency than polling
- Fewer unnecessary requests

### Cons

- More server resources
- Request recreated after every response

### Use Cases

- Simple Chat
- Notifications

---

# Push Communication

The server automatically pushes updates.

```
Server
   │
   ▼
Client
```

### Characteristics

- Server initiates communication
- Real-time
- Low latency

### Use Cases

- Live Notifications
- Chat Applications
- Gaming

---

# WebSockets

## Definition

Persistent Full-Duplex communication.
<img width="972" height="642" alt="image" src="https://github.com/user-attachments/assets/a8fbc6b8-bccd-4e77-b7c9-05eff82275fe" />


```
Client <=================> Server

Open Connection

Client ---> Message

Server ---> Message

Client ---> Message
```

### Features

- Persistent TCP connection
- Bidirectional communication
- Low latency
- Real-time

### Advantages

- Fast
- No repeated HTTP requests
- Excellent user experience

### Challenges

- Stateful
- Higher memory usage
- Load balancing is difficult

### Real-world Examples

- WhatsApp
- Slack
- Discord
- Google Docs
- Microsoft Teams
- Multiplayer Games

---

# Webhooks

## Definition

Server-to-server communication using HTTP POST.

```
Provider

    │

HTTP POST

    │

Consumer
```

### Workflow

```
Customer

↓

Stripe

↓

Webhook

↓

Backend

↓

Update Order Status
```

### Best Practices

- Retry failed requests
- Verify signatures
- Idempotent endpoint
- Store Event IDs

### Examples

- Stripe
- PayPal
- GitHub
- Jenkins
- GitLab CI/CD


---

# Server-Sent Events (SSE)

## Definition

One-way communication from Server → Client.

<img width="1146" height="612" alt="image" src="https://github.com/user-attachments/assets/eecccf37-61cd-4561-ad31-f7118f435b96" />


```
Server

↓

↓

↓

Client
```

### Features

- One-way only
- Long-lived HTTP connection
- Browser native support

### Use Cases

- Live Scores
- Stock Market
- News Feed
- Job Progress

### Limitations

- No client-to-server messaging
- Text-only events



# Server-Sent Events (SSE) – File Upload Progress

> **Scenario:** A user uploads a large file (e.g., 2 GB video). The upload and post-processing (virus scan, compression, cloud storage) take 30–60 seconds. Instead of polling, the backend streams progress updates to the frontend using **Server-Sent Events (SSE)**.

---

# Why Use SSE?

Without SSE:

```text
User Uploads File
        │
        ▼
Uploading...

(Wait 30 seconds...)

Upload Complete
```

❌ The user has no visibility into the upload progress.

With SSE:

```text
Uploading...

10%
25%
40%
60%
80%
100%

✔ Upload Completed
```

✅ The backend pushes progress updates in real time over a single HTTP connection.

---

# Architecture

```text
                    POST /upload
+-----------+ -----------------------------> +-------------------+
|  Browser  |                                |  Spring Boot API  |
+-----------+                                +-------------------+
                                                   |
                                                   |
                                            Store File
                                                   |
                                            Process File
                                                   |
                                            Generate Progress
                                                   |
                 <====== SSE Stream ===============|
                                                   |
                           10% → 25% → 50% → 75% → 100%
```

---

# Workflow

```text
1. User selects a file.

        │

2. Browser uploads the file.

        │

3. Backend returns an Upload ID.

        │

4. Browser opens an SSE connection.

        │

5. Backend continuously sends progress events.

        │

6. Frontend updates the progress bar.

        │

7. Upload completes and SSE connection closes.
```

---

# Step 1: Upload File

### Request

```http
POST /api/files/upload
Content-Type: multipart/form-data
```

### Response

```json
{
    "uploadId": "UPL-1001"
}
```

---

# Step 2: Open SSE Connection

```http
GET /api/files/upload/UPL-1001/progress
Accept: text/event-stream
```

The browser keeps this HTTP connection open until the upload is complete.

---

# Step 3: Backend Streams Progress

```text
event: progress
data: 10

event: progress
data: 25

event: progress
data: 50

event: progress
data: 75

event: progress
data: 100

event: completed
data: Upload Successful
```

---

## Spring Boot Implementation

```java
@GetMapping(value = "/progress/{uploadId}",
        produces = MediaType.TEXT_EVENT_STREAM_VALUE)
public SseEmitter progress(@PathVariable String uploadId) {

    SseEmitter emitter = new SseEmitter();

    Executors.newSingleThreadExecutor().submit(() -> {

        try {

            while (true) {

                Integer progress = progressCache.get(uploadId);

                emitter.send(
                        SseEmitter.event()
                                .name("progress")
                                .data(progress)
                );

                if (progress == 100) {

                    emitter.complete();

                    break;
                }

                Thread.sleep(500);

            }

        } catch (Exception ex) {

            emitter.completeWithError(ex);

        }

    });

    return emitter;
}
```
---

# Frontend JavaScript

```javascript
const uploadId = "UPL-1001";

const eventSource =
    new EventSource(`/api/files/upload/${uploadId}/progress`);

eventSource.addEventListener("progress", (event) => {

    const progress = Number(event.data);

    console.log(progress + "%");

    progressBar.value = progress;

});

eventSource.addEventListener("completed", (event) => {

    alert(event.data);

    eventSource.close();

});
```

---

# UI Progress

```text
Uploading File...

█░░░░░░░░░ 10%

██░░░░░░░░ 25%

█████░░░░░ 50%

████████░░ 75%

██████████ 100%

✔ Upload Successful
```

---

# Sequence Diagram

```text
+---------+                      +----------------+
| Browser |                      | Spring Boot API|
+---------+                      +----------------+
     |                                    |
     | POST /upload                       |
     |----------------------------------->|
     |                                    |
     | Upload ID                          |
     |<-----------------------------------|
     |                                    |
     | GET /progress (SSE)                |
     |----------------------------------->|
     |                                    |
     | 10%                                |
     |<-----------------------------------|
     | 25%                                |
     |<-----------------------------------|
     | 50%                                |
     |<-----------------------------------|
     | 75%                                |
     |<-----------------------------------|
     | 100%                               |
     |<-----------------------------------|
     | Completed                          |
     |<-----------------------------------|
```

---

# Real-World Use Cases

- Google Drive file uploads
- Dropbox uploads
- OneDrive uploads
- YouTube video uploads
- PDF generation progress
- ZIP compression progress
- Virus scan progress
- Data import/export status
- Report generation
- Backup and restore progress

---

# Why SSE Instead of Polling?

| Polling | SSE |
|----------|-----|
| Multiple HTTP requests | Single long-lived HTTP connection |
| Wastes bandwidth | Efficient |
| Delayed updates | Real-time updates |
| Client continuously asks | Server pushes updates |
| Higher server load | Lower network overhead |

---

# Advantages

- One HTTP connection
- Real-time progress updates
- Built-in browser support (`EventSource`)
- Automatic reconnection
- Easy to implement
- Lower bandwidth than polling

---

# Limitations

- One-way communication only (Server → Client)
- Cannot send binary data
- Not suitable for chat or gaming
- Client must open a new HTTP request to send data

---

# Interview Question

### Q: A user uploads a 2 GB file that takes 45 seconds to process. The UI should display live upload progress. Which communication pattern would you use?

**Answer:** **Server-Sent Events (SSE)**

**Reason:**

- The client only receives progress updates.
- No bidirectional communication is required.
- A single HTTP connection streams updates in real time.
- More efficient and simpler than polling for this use case.


---

# Heartbeats

## Definition

Heartbeat messages determine whether a service is alive.

```
Worker

↓

I'm Alive

↓

Master

↓

I'm Alive

↓

Master
```

### Purpose

- Failure Detection
- Online Status
- Cluster Health

### Examples

- Kubernetes
- WhatsApp Presence
- Kafka Brokers

---

# Timeouts

## Definition

Maximum wait time before cancelling a request.

```
Service A

↓

Request

↓

Service B

↓

No Response

↓

Timeout

↓

Fallback
```

### Benefits

- Prevent hanging requests
- Avoid cascading failures
- Enable retries

### Examples

- Database Query
- Payment Gateway
- External API

---

# Communication Comparison

| Pattern | Initiated By | Direction | Connection | Latency | Best For |
|----------|--------------|-----------|------------|----------|----------|
| Polling | Client | Client → Server | HTTP | High | Dashboard |
| Long Polling | Client | Client → Server | HTTP | Medium | Notifications |
| WebSocket | Both | Two-way | Persistent TCP | Low | Chat |
| Webhook | Server | Server → Server | HTTP POST | Low | Event Notification |
| SSE | Server | Server → Client | Long HTTP | Low | Live Feed |

---

# Reliability Mechanisms

| Mechanism | Purpose |
|------------|---------|
| Heartbeat | Detect failures |
| Timeout | Prevent waiting forever |
| Retry | Handle temporary failures |
| Idempotency | Prevent duplicate processing |

---

# Which One Should You Use?

| Requirement | Recommended Pattern |
|-------------|---------------------|
| Refresh dashboard every few seconds | Polling |
| Wait for new notification | Long Polling |
| Real-time chat | WebSocket |
| Payment success notification | Webhook |
| Live cricket score | SSE |
| User online/offline | Heartbeat |
| Slow API protection | Timeout |

---

# Interview Cheat Sheet

### Polling

> Client repeatedly asks the server.

### Long Polling

> Client waits until data is available.

### WebSocket

> Persistent two-way communication.

### Webhook

> Server notifies another server.

### SSE

> Server continuously pushes updates to client.

### Heartbeat

> "I'm Alive" signal.

### Timeout

> Maximum waiting period before failure.

---

# Key Takeaways

- Pull = Client requests data.
- Push = Server sends data automatically.
- Polling = Simple but inefficient.
- Long Polling = Better polling.
- WebSocket = Best for real-time bidirectional communication.
- Webhook = Best for server-to-server events.
- SSE = Best for one-way live updates.
- Heartbeat = Detect service health.
- Timeout = Prevent hanging requests.

---

## Interview Memory Trick

| Pattern | Remember As |
|----------|-------------|
| Polling | "Keep Asking" |
| Long Polling | "Wait for Answer" |
| WebSocket | "Always Connected" |
| Webhook | "I'll Notify You" |
| SSE | "Server Talks Only" |
| Heartbeat | "I'm Alive" |
| Timeout | "Don't Wait Forever" |
