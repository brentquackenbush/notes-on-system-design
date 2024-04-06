# Distributed Message Queue System Design

> A Distributed Message Queue (DMQ) facilitates reliable, scalable, and asynchronous communication between different parts of a system. By implementing a DMQ, systems can achieve decoupling of components, enhanced scalability, increased availability, and improved performance. This markdown highlights key benefits and design considerations using real-world examples in designing a DMQ for a system design interview.

## Key Benefits

### Decoupling

- **Description**: Message queues eliminate the tight coupling between components, allowing them to be updated, maintained, or scaled independently.
- **Example**: Amazon SQS (Simple Queue Service) enables decoupling of microservices, distributed systems, and serverless applications, thereby enhancing developer agility and resilience.

### Improved Scalability

- **Description**: DMQs allow for the independent scaling of producers and consumers based on traffic load, which is crucial for handling variable workloads efficiently.
- **Example**: During Black Friday sales, e-commerce platforms can dynamically add more consumers to their order processing queues to handle the surge in orders, ensuring timely processing without overloading the services.

### Increased Availability

- **Description**: With DMQs, if one part of the system goes offline, the other components can continue to interact with the queue, which acts as a buffer and ensures that messages are not lost.
- **Example**: In a video streaming service, even if the video processing service is temporarily unavailable, videos uploaded by producers can still be queued for processing, ensuring continuity of service.

### Better Performance

- **Description**: DMQs facilitate asynchronous communication, enabling producers to enqueue messages without waiting for a response. Consumers can process messages whenever they are available, following either a Pull or Push-based architecture.
- **Example**: In a social media platform, new post notifications can be queued and then pushed to consumers in batches, optimizing network usage and reducing the need for constant polling.

## Understand the Problem and Establish Design Scope

**Interviewer**: We're looking to build a distributed message queue system. It should be robust and scalable. Let's begin by defining the scope.

### Problem Clarification

**Candidate**: For the message queue system we're architecting, what type of data are we handling predominantly?

**Interviewer**: Primarily, it will be text-based messages. We're talking about payloads that are not excessively large, usually within a few kilobytes.

**Candidate**: Understood. And will there be a need for these messages to be consumed more than once or by multiple consumers?

**Interviewer**: Yes, the messages should support being processed by different consumers. The ability for messages to be consumed multiple times by various systems is desirable.

**Candidate**: Does the order in which messages are produced need to be strictly preserved during consumption?

**Interviewer**: Generally, yes, but absolute strict ordering is not a must-have. Prioritizing certain messages over others could be necessary depending on the context.

**Candidate**: What about data durability and retention? How critical is it for our system?

**Interviewer**: We need a pragmatic approach to message durability that ensures messages are not lost but also balances system performance. Think in terms of data retention of no more than a couple of weeks.

## Functional Requirements

Following our conversation, the candidate then summarizes the functional requirements as:

- **Message Production**: The system must allow producers to send messages to the queue.
- **Message Consumption**: Consumers must be able to retrieve and process messages from the queue.
- **Repeatable Consumption**: The system should allow messages to be consumed repeatedly by different consumers.
- **Ordering**: While not strictly necessary, the system should maintain the order of message delivery to a reasonable degree.
- **Durability and Retention**: Messages should be stored durably with a retention policy that provides a balance between availability and performance, tentatively set to a maximum of two weeks.
- **Size Management**: The message system needs to handle message payloads that are typically in the range of a few kilobytes.

## Non-Functional Requirements

- High throughput or low latency configurable based on preferences
- Scalable, the system should be distributed in nature. It should be able to handle large amounts of messages with spikes and surges. 
- Persistent and durable. Data should be persisted on disk and replicated across multiple nodes.

## High-Level Design
![DMQ High Level.png](..%2F..%2FDownloads%2FDMQ%20High%20Level.png)