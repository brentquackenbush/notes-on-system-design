# SystemDesignNotes

## Table of Contents
1. [Database Systems](#database-systems)
   - [Indexing Data Structures in Database Systems](#indexing-data-structures-in-database-systems)
     - [Skiplist](#skiplist)
     - [Hash Index](#hash-index)
     - [SSTable (Sorted String Table)](#sstable-sorted-string-table)
     - [LSM tree (Log-Structured Merge-Tree)](#lsm-tree-log-structured-merge-tree)
     - [B-tree](#b-tree)
     - [Inverted Index](#inverted-index)
     - [Suffix Tree](#suffix-tree)
     - [R-tree](#r-tree)
   - [Replication](#replication)
   - [Choosing a Database for System Design](#choosing-a-database-for-system-design)
2. [Cache](#cache)
    - [Client Apps (Browser Caching)](#client-apps-browser-caching)
    - [Content Delivery Network (CDN)](#content-delivery-network-cdn)
    - [Load Balancer Caching](#load-balancer-caching)
    - [Messaging Infrastructure (e.g., Kafka)](#messaging-infrastructure-eg-kafka)
    - [Services Caching](#services-caching)
    - [Distributed Cache (e.g., Redis)](#distributed-cache-eg-redis)
    - [Full-text Search (e.g., Elasticsearch)](#full-text-search-eg-elasticsearch)
    - [Database Caching](#database-caching)
3. [Load Balancer and API Gateway: An Overview](#load-balancer-and-api-gateway-an-overview)
    - [Load Balancer](#load-balancer)
    - [API Gateway](#api-gateway)
4. [Which Comes First: Load Balancer or API Gateway?](#which-comes-first-load-balancer-or-api-gateway)
5. [Detailed Interaction](#detailed-interaction)
6. [Shared Nothing Architecture](#shared-nothing-architecture)
7. [Shared Everything Architecture](#shared-everything-architecture)
8. [Comparison and Context in Distributed Systems](#comparison-and-context-in-distributed-systems)
9. [OAuth 2.0 and OpenID Connect](#oauth-20-and-openid-connect)

<a name="database-systems"></a>
## Database Systems
This section covers fundamental and advanced topics related to database systems, focusing on indexing data structures critical for performance optimization and criteria for selecting appropriate database systems for specific system design requirements.

<a name="indexing-data-structures-in-database-systems"></a>
### Indexing Data Structures in Database Systems

Indexing is a technique used to speed up the retrieval of records by reducing the number of disk accesses. Different data structures support various indexing methods, each with its own set of advantages and limitations.

#### SSTable (Sorted String Table)
- **Data Structure:** Consists of immutable, sorted key-value pairs stored on disk, designed to support efficient sequential reads and range queries.
- **Pros:** Offers high throughput for read operations, especially beneficial for range scans. SSTables support efficient disk storage through compaction, which merges multiple SSTables, reducing storage overhead and improving read performance.
- **Cons:** Writing data initially requires more time since it involves writing to an in-memory structure first (such as a MemTable in LSM trees) before being flushed to disk as SSTables. This process can introduce latency in write operations.
- **Technologies:**
    - **Google's Bigtable:** A distributed storage system for managing structured data that scales to a very large size.
    - **Apache Cassandra and Apache HBase:** NoSQL databases that use SSTables for efficiently storing data on disk, enabling effective range queries and data compaction.


#### LSM tree (Log-Structured Merge-Tree)
- **Data Structure:** Integrates a memory-resident structure (e.g., a balanced binary search tree or skiplist) with SSTable-based disk storage. Writes are first recorded in the memory-resident structure and then flushed to disk as SSTables once a certain threshold is reached.
- **Pros:**  Provides superior write performance by buffering writes in memory and reducing disk I/O through batched writes and compaction. Ideal for write-heavy applications where write throughput is critical.
- **Cons:** Read operations, particularly point lookups, can be slower due to the need to search across multiple components (in-memory structure, multiple SSTables). Compaction processes, while optimizing storage and read performance, can consume significant system resources.
- **Technologies:**
    - **Apache Cassandra:** A distributed NoSQL database designed for handling large amounts of data across many commodity servers.
    - **RocksDB and LevelDB:** High performance embedded databases for key-value data, using LSM trees for managing write-heavy workloads with efficient disk writes and compactions.

#### B-tree
- **Data Structure:** A self-balancing tree that maintains data in a sorted order, allowing for efficient searches, sequential access, and insertions/deletions. Each node can have more than two children, reducing the tree's height and disk reads.
- **Pros:** Highly efficient for a balanced mix of read and write operations due to its ability to maintain sorted data and support logarithmic time complexity for basic operations. B-trees are widely used in relational database systems.
- **Cons:** While B-trees minimize disk I/O by keeping the tree height low, they can still be disk I/O intensive for very large datasets. Balancing the tree during insertions and deletions adds overhead.
- **Technologies:**
    - **Oracle Database, PostgreSQL, and MySQL:** Relational database management systems that employ B-trees for indexing table data, supporting efficient access patterns for both point queries and range queries.


#### Skiplist
- **Data Structure:**  A probabilistic alternative to balanced trees, skiplists consist of multiple levels of linked lists, where each level is a subset of the level below, allowing for logarithmic search times.
- **Pros:** Simplified implementation compared to balanced trees, with efficient support for insertions, deletions, and searches. Skiplists are used in databases like Redis for sorted sets.
- **Cons:** The probabilistic nature of skiplists means that performance guarantees are not deterministic. Additionally, skiplists require extra memory for pointers.
- **Technologies:**
    - **Redis:** An in-memory data structure store, using skiplists for implementing sorted sets, facilitating efficient range queries and sorted data manipulation.

#### Hash Index
- **Data Structure:** Employs a hash table where a hash function is used to map keys directly to an array of buckets or slots, facilitating immediate access to the value associated with a given key.
- **Pros:** Offers constant time complexity (O(1)) for search, insert, and delete operations on average, making it highly efficient for point queries with known keys.
- **Cons:** Not suitable for range queries. Handling collisions (when multiple keys hash to the same value) can complicate the implementation and impact performance.
- **Technologies:**
    - **MongoDB:** A document-oriented NoSQL database that uses hash indexes to support efficient equality queries.
    - **Memcached:** An in-memory key-value store for small chunks of arbitrary data, utilizing hash tables for rapid data retrieval.

#### Inverted Index
- **Data Structure:** A mapping from content (such as words or terms) to its locations in a database or set of documents. Essentially, it inverts the data structure to enable efficient full-text search.
- **Pros:** Crucial for search engines, enabling quick full-text searches by directly pointing to locations of terms across documents.
- **Cons:** The size of the index can grow significantly with the corpus size, requiring substantial processing power to update and maintain.
- **Technologies:**
    - **Elasticsearch and Apache Lucene:** Search engines that leverage inverted indexes for fast full-text searching capabilities across extensive datasets, making them highly efficient for search operations.

#### Suffix Tree
- **Data Structure:** Represents all possible suffixes of a given text in a compressed trie, allowing for efficient substring and pattern matching queries.
- **Pros:** Highly efficient for operations that involve searching for patterns within text, such as DNA sequence analysis or text editing applications.
- **Cons:** Complex to implement and can be space-intensive, although advanced variants like the suffix array offer more space-efficient alternatives.
- **Technologies:**
    - **Bioinformatics tools (e.g., BLAST):** Utilize suffix trees for DNA sequencing and pattern matching, aiding in the analysis of biological sequences.
    - **Text editing software:** Employs suffix trees for efficient substring search operations, although specific software names are less commonly cited in generic descriptions.

#### R-tree
- **Data Structure:** A balanced tree structure optimized for indexing spatial data, such as geographical coordinates, rectangles, or polygons. It organizes spatial data by nesting bounding boxes.
- **Pros:** Enables efficient processing of spatial queries, including range searches, nearest neighbor searches, and intersection queries.
- **Cons:** The complexity of balancing the tree and managing overlapping bounding boxes can impact performance, especially with high-dimensional data.
- **Technologies:**
    - **PostGIS:** A spatial database extender for PostgreSQL, using R-trees for indexing spatial data such as geographical locations and maps.
    - **Spatial indexing in MongoDB:** Supports geospatial queries and data management by utilizing R-trees for efficient spatial operations.

<a name="replication"></a>
### Partitioning, Sharding, and Replication

Understanding **Partitioning**, **Sharding**, and **Replication** is crucial for designing scalable, reliable, and efficient database systems. Here's a breakdown of these concepts to aid in your system design interviews.

### Partitioning

Partitioning is the process of dividing a database into smaller, manageable pieces, known as partitions, which can be spread across different storage spaces or systems. Partitioning is often used to improve manageability, performance, and availability. There are two main types of partitioning:

- **Horizontal Partitioning (or Sharding)**: This involves dividing a table into rows, where each partition contains a subset of the rows based on a partition key. It's akin to dividing a long book into volumes where each volume contains consecutive chapters.

- **Vertical Partitioning**: This involves dividing a table into columns, where each partition contains a subset of the columns. It's like splitting a wide chart into narrower, focused charts.

### Sharding

Sharding, a form of horizontal partitioning, specifically involves distributing data across multiple machines or databases, each called a shard, which is a distinct subset of the data. Sharding is primarily used to scale databases horizontally, enabling them to handle more data and traffic by distributing the load across several servers. Each shard is a self-contained database, and the collection of shards makes up the entire logical database.

Sharding is particularly useful in scenarios where the dataset is too large to be stored on a single database server or when read/write operations exceed the capacity of a single machine.

### Replication

Replication involves creating copies of a database, or parts of a database, and synchronizing them across multiple servers or locations. The primary purposes of replication are to increase the availability of data and to ensure data redundancy for backup and failover scenarios. Replication can be implemented in various forms:

- **Master-Slave Replication**: Changes made on the master server are replicated to one or more slave servers. Slaves can serve read queries to distribute the load.

- **Peer-to-Peer Replication**: Every node in the network acts as both a master and a slave, providing high availability and load distribution.

- **Read Replica Replication**: Similar to master-slave but specifically optimized for scaling out read operations.

### Key Differences and Integration

- **Purpose**: Partitioning (including sharding) is primarily about enhancing performance and manageability by dividing data into more manageable segments. Replication focuses on increasing data availability and redundancy.

- **Data Duplication**: Replication involves creating duplicates of data across servers to ensure availability and redundancy. Sharding/partitioning divides the data without necessarily duplicating it.

- **Scalability**: Sharding is a strategy for horizontal scalability, addressing the growth of data and workload by adding more servers. Replication can aid in horizontal scalability, especially for read-heavy workloads, and enhances availability and disaster recovery.

### Partition Keys and Shard Keys

Understanding **Partition Keys** and **Shard Keys** is essential for designing scalable and efficient database systems. These keys play a critical role in how data is distributed and accessed across NoSQL and SQL databases.

### Partition Keys

A **partition key** is used in database systems to distribute data across multiple partitions. It determines how data is organized and distributed across the system. Partition keys are crucial for optimizing data access and storage efficiency.

#### Types of Partition Keys

- **Single-Attribute Partition Key**: Involves using a single attribute (column) of the data as the partition key. For example, a user ID or timestamp could be used to distribute user data or logs across different partitions, respectively.

- **Composite Partition Key**: Combines multiple attributes to form a partition key. It's useful when no single attribute can ensure even distribution of data. For example, combining country code with user ID might be used in a global application to ensure data is spread evenly and is also partitioned by geographical region.

### Shard Keys

**Shard keys** are specific to sharded databases and are used to distribute data across multiple shards - physical or logical partitions in a distributed database system. The choice of shard key affects the database's performance, scalability, and balance of data distribution.

#### Types of Shard Keys

- **Hash-based Shard Key**: Involves applying a hash function to the shard key value to distribute data evenly across shards. The hash function transforms the key into a hash value, which is then used to assign the data to a shard. This method ensures a uniform distribution of data but can make range queries more challenging.

- **Range-based Shard Key**: Data is distributed based on ranges of shard key values. Each shard holds data for a specific range. This approach is beneficial for queries that retrieve data in a range but can lead to hotspots if the data isn't evenly distributed across the key ranges.

<a name="choosing-a-database-for-system-design"></a>
### Choosing a Database for System Design

#### SQL Databases

SQL (Structured Query Language) databases, also known as relational databases, have been the cornerstone of data storage and retrieval systems for decades. Despite the rise of various database technologies, SQL databases remain a critical part of the technological landscape, particularly for applications where data integrity, consistency, and relationships are paramount.

**Key Features:**

- **Relational and Normalized Data:** SQL databases are designed around a structured format where data is stored in tables. These tables are related to each other through foreign keys, enabling complex queries across multiple tables. The normalization process reduces data redundancy and ensures data integrity.

- **ACID Guarantees:** One of the hallmark features of SQL databases is their adherence to ACID (Atomicity, Consistency, Isolation, Durability) properties. These guarantees are crucial for applications that cannot afford to lose or corrupt data, such as financial systems that keep a log of transactions. Atomicity ensures that each transaction is all or nothing, Consistency ensures that any transaction will bring the database from one valid state to another, Isolation ensures that concurrent execution of transactions results in a system state that would be obtained if transactions were executed serially, and Durability ensures that once a transaction has been committed, it will remain so, even in the event of a power loss, crashes, or errors.

- **Two-Phase Commit:** In distributed SQL databases, a two-phase commit protocol can be used to ensure data consistency across multiple nodes, which is essential for maintaining the ACID properties in a distributed environment. However, this process can be expensive in terms of performance, as it requires a coordination phase and a commit phase, potentially increasing the latency of transactions.

- **Indexing with B-trees:** SQL databases commonly use B-trees for indexing, which allows for efficient data retrieval. B-trees help in maintaining a balanced tree structure that reduces the time complexity of search, insert, and delete operations to O(log n), where n is the number of entries in the database.

**Pros:**

- **Data Integrity and Reliability:** Thanks to ACID guarantees, SQL databases are highly reliable for critical applications where data accuracy and integrity are non-negotiable.
- **Complex Querying:** The relational model and SQL language allow for complex queries and data analysis, making it easier to derive meaningful insights from related data.
- **Standardization:** SQL is a widely understood and standardized language, making it easier for developers and database administrators to work with various SQL databases without steep learning curves.

**Cons:**

- **Scalability Challenges:** While modern SQL databases have made significant strides in horizontal scalability, they traditionally face challenges in scaling out compared to NoSQL solutions, especially for write-heavy applications.
- **Performance Overhead:** The mechanisms that ensure data consistency and integrity, such as locking and the two-phase commit protocol, can introduce performance overhead, making SQL databases slower for certain types of applications, particularly those requiring high throughput and low latency.
- **Schema Rigidity:** SQL databases require a predefined schema, which can make them less flexible when dealing with rapidly evolving data models or unstructured data.

#### NoSQL Databases

As the digital landscape continues to evolve, NoSQL databases have emerged as pivotal components within modern data management strategies. Characterized by their schema-less nature, these databases offer flexible solutions to data storage and retrieval, catering to a wide range of applications from web services to big data processing. Their design principles diverge significantly from traditional relational databases, prioritizing scalability, performance, and ease of development.

**Key Features:**

- **Schema-less Data Models:** NoSQL databases support dynamic schema for unstructured data, facilitating the storage of data in various formats including document, key-value, wide-column, and graph. This versatility allows developers to rapidly prototype and iterate on applications without the constraints of a fixed schema.

- **Scalability:** Designed with distributed architecture in mind, NoSQL databases excel in horizontal scalability, allowing systems to scale out across multiple servers and data centers with ease. This is particularly advantageous for applications requiring high throughput and large volumes of data.

- **CAP Theorem:** NoSQL databases often navigate the trade-offs between consistency, availability, and partition tolerance (CAP theorem), with many opting to prioritize availability and partition tolerance. However, some NoSQL systems allow for configurable consistency levels to meet specific application requirements.

- **Big Data and Real-Time Processing:** The inherent scalability and flexible data models of NoSQL databases make them well-suited for handling big data and real-time analytics, offering efficient solutions for processing and querying large datasets.

**Notable Implementations:**

- **MongoDB:** A document-oriented database that stores data in JSON-like formats, MongoDB is renowned for its flexibility, powerful query capabilities, and ease of use, making it a popular choice for web applications.

- **Cassandra:** Optimized for high availability and scalability, Cassandra is a wide-column store that ensures operational continuity, even in the face of hardware failures or network partitions, ideal for mission-critical applications with no tolerance for downtime.

- **Redis:** An in-memory key-value store, Redis stands out for its exceptional speed and versatility, supporting a variety of data structures and use cases, including caching, session management, and real-time analytics.

- **Neo4j:** A graph database designed to handle complex and connected data, Neo4j excels in scenarios where relationships between data points are key, such as social networks, recommendation engines, and fraud detection.

**Advantages:**

- **Development Agility:** The schema-less nature and flexible data models of NoSQL databases reduce the time and complexity involved in application development, enabling faster iterations.
- **Performance at Scale:** NoSQL databases are engineered to provide robust performance and scalability, effectively handling large volumes of data and high user loads.
- **Diverse Data Handling:** They accommodate a wide array of data types and structures, from simple key-value pairs to complex graphs, providing versatile solutions for varied application needs.

**Challenges:**

- **Data Consistency:** Achieving strict data consistency can be challenging in distributed NoSQL systems, requiring careful selection of consistency models based on application requirements.
- **Complexity in Transaction Support:** Comprehensive transaction support, especially for operations spanning multiple data items, can be more complex to implement compared to traditional SQL databases.
- **Operational Overhead:** Managing and optimizing NoSQL databases, particularly in distributed environments, can introduce operational complexities, necessitating specialized skills and tools.

<a name="cache"></a>
## Cache

Caching is a critical performance optimization strategy used across various layers of application architecture. Below, we explore the unique characteristics and industrial use cases for each layer of caching.

### Client Apps (Browser Caching)

**Caching Mechanism:** Browsers store copies of files in a local cache, serving them on future requests without going to the network.

**Use Cases:**
- **Web Application Speed:** Improving load times for returning users to web applications.
- **Bandwidth Reduction:** Saving bandwidth by not downloading the same resources repeatedly.
- **User Experience:** Providing an enhanced user experience with quicker interaction feedback.

### Content Delivery Network (CDN)

**Caching Mechanism:** CDNs distribute and cache static content across multiple geographic locations to serve data from the closest point to the user.

**Use Cases:**
- **Global Reach:** Reducing latency for international users by serving content from local nodes.
- **Scalability:** Handling spikes in web traffic by distributing load.
- **Content Availability:** Ensuring content availability during network or server outages through redundant storage.

### Load Balancer Caching

**Caching Mechanism:** Intelligent load balancers can cache content, reducing the request load on back-end services.

**Use Cases:**
- **Traffic Management:** Mitigating the effect of traffic spikes on back-end resources.
- **Security:** Acting as the first line of defense against DDoS attacks by absorbing malicious traffic.

### Messaging Infrastructure (e.g., Kafka)

**Caching Mechanism:** Messages are cached on disk in a broker, like Kafka, to ensure they are not lost if a consumer is not ready to process them immediately.

**Use Cases:**
- **Data Streaming:** Enabling real-time data streaming and processing for event-driven architectures.
- **Log Aggregation:** Collecting and processing logs from distributed systems.

### Services Caching

**Caching Mechanism:** Services employ various layers of caching, from CPU cache to in-memory data stores, and optionally, disk storage.

**Use Cases:**
- **Performance Optimization:** Reducing the time to access frequently requested data by keeping it in faster storage.
- **Cost Efficiency:** Minimizing the use of more expensive persistent storage resources.

### Distributed Cache (e.g., Redis)

**Caching Mechanism:** Key-value pairs are stored in a distributed system to provide highly available and fast data access across services.

**Use Cases:**
- **Session Storage:** Maintaining user session data in web applications for state management.
- **Real-time Analytics:** Supporting real-time analytics with in-memory data sets for immediate computation.

### Full-text Search (e.g., Elasticsearch)

**Caching Mechanism:** A full-text search system indexes data to return fast query results on large volumes of text data.

**Use Cases:**
- **Search Engines:** Powering search functionalities in eCommerce, websites, and enterprise applications.
- **Log Analysis:** Allowing for complex queries against large datasets of logs for operational insights.

### Database Caching

**Caching Mechanism:** Databases implement a variety of caching mechanisms to optimize query performance and data manipulation operations.

- **WAL (Write-ahead Log):** Caching changes before committing them to the data files ensures transactional integrity and fast recovery.
- **Bufferpool:** Improving read performance by keeping active data in memory rather than on disk.
- **Materialized View:** Enhancing read performance for complex queries by precalculating and storing the results.
- **Transaction log:** Preserving a record of all transactions allows for accurate point-in-time recovery.
- **Replication Log:** Facilitating data replication across a distributed database system for high availability and disaster recovery.

**Use Cases:**
- **Transaction Speed:** Ensuring quick transaction processing for financial and commerce systems.
- **Query Performance:** Providing faster response times for reporting and business intelligence applications.
- **Data Integrity:** Maintaining consistent data states across replicated environments and during system failures.

<a name="load-balancer-and-api-gateway-an-overview"></a>
## Load Balancer and API Gateway: An Overview

### Load Balancer

Its primary function is to distribute incoming network traffic across multiple servers to ensure no single server becomes overloaded, improving the reliability and availability of applications. Load balancers operate at various levels of the OSI model; some work on the transport layer (Layer 4 - TCP/UDP) to distribute traffic based on IP range or port number, while others work on the application layer (Layer 7 - HTTP/HTTPS) to distribute requests based on content type, headers, cookies, or application-specific data.

### API Gateway

It acts as a front-door to facilitate and manage incoming API requests to backend services. An API gateway typically handles a host of concerns, such as request routing, composition, and protocol translation, and often includes additional responsibilities like authentication, rate limiting, and analytics.

<a name="which-comes-first-load-balancer-or-api-gateway"></a>
## Which Comes First: Load Balancer or API Gateway?

### Entry Point
The load balancer acts as the initial entry point for all incoming traffic. It's responsible for ensuring that user requests are distributed such that no single entry point becomes a bottleneck or single point of failure.

### High Availability
By placing the load balancer at the forefront, you can provision multiple instances of your API gateway to ensure high availability. The load balancer can then distribute incoming API requests across these various gateway instances.

### Auto Scaling
In cloud environments, load balancers integrate with auto-scaling groups that dynamically adjust the number of API gateway instances based on demand. This scalability is critical during traffic spikes.

### Edge Routing
The load balancer can also serve as an edge router, making routing decisions based on simple factors like domain name or path prefix before passing traffic to the more complex routing logic within the API gateway.

### Security
By positioning the load balancer in front, any potential DDoS attack or traffic surge is managed at the network's edge, reducing exposure to the API gateway and the internal services.

### SSL Termination
Load balancers often handle SSL termination, decrypting incoming requests and then passing on unencrypted traffic to the API gateway. This offloads the cryptographic workload from the API gateway.

<a name="shared-nothing-architecture"></a>
## Shared Nothing Architecture

**Concept:**
- In a "Shared Nothing" architecture, each node in a distributed system is self-sufficient. This means that each node has its own private memory and disk storage. Nodes do not share these resources with others.

**Key Characteristics:**
1. **Independence:** Each node operates independently. There is no single point of contention across the system because resources aren't shared.
2. **Scalability:** It's highly scalable. Adding more nodes to the system often linearly increases the total capacity and performance because each node operates independently.
3. **Fault Tolerance:** It enhances fault tolerance. The failure of one node doesn't directly impact others.

**Applications:**
- Commonly used in distributed databases, web services, and certain types of parallel processing systems.

**Example:**
- Imagine a warehouse with several independent storage units. Each unit operates independently. If one unit is busy or fails, it doesn't directly hinder the operations of the others.


<a name="shared-everything-architecture"></a>
## Shared Everything Architecture

**Concept:**
- In contrast, a "Shared Everything" architecture involves nodes sharing all resources, such as memory, disk storage, and even CPU cycles.

**Key Characteristics:**
1. **Resource Pooling:** All nodes can access the entire pool of resources, making it easier to balance loads and manage resources.
2. **Complexity in Management:** Managing and synchronizing shared resources can be complex, particularly as the system scales.
3. **Potential Bottlenecks:** There can be bottlenecks due to resource contention when multiple nodes try to access the same resource simultaneously.

**Applications:**
- Often seen in traditional symmetric multiprocessing systems.

**Example:**
- Think of a large, central library where everyone in town comes to share books and resources. If a popular book is in use by someone, others must wait their turn.

<a name="comparison-and-context-in-distributed-systems"></a>
## Comparison and Context in Distributed Systems

- **Scalability:** Shared Nothing typically scales better because adding more nodes doesn't increase contention for shared resources.
- **Performance:** Shared Everything might offer better performance for certain workloads due to the close proximity and sharing of resources, but it can suffer from resource contention issues.
- **Fault Tolerance:** Shared Nothing has an edge in fault tolerance, as each node's failure is isolated.
- **Complexity:** Shared Nothing is simpler in terms of resource management but requires more sophisticated strategies for data distribution and coordination.

In summary, the choice between Shared Nothing and Shared Everything architectures depends on the specific requirements and constraints of the system you're designing. Shared Nothing is favored for systems that require linear scalability and high fault tolerance, while Shared Everything might be chosen for its efficient resource utilization in environments where resource contention can be managed effectively.

<a name="oauth-and-openid-connect"></a>
## OAuth 2.0 and OpenID Connect

### Resources
- [OAuth 2.0 RFC6749](https://datatracker.ietf.org/doc/html/rfc6749)
- Upcoming version: OAuth 2.1

### Overview of OAuth 2.0 and OpenID Connect (OIDC)
OAuth 2.0 and OpenID Connect are standards for secure authorization and authentication on the internet. OAuth 2.0 provides authorization flows for web, desktop, mobile applications, and smart devices, while OpenID Connect is built on top of OAuth 2.0 and adds an identity layer for authenticating users.

### Use Cases

#### OIDC for User Authentication
- Delegates user authentication to external identity providers, such as social logins (Google, Facebook) or enterprise Single Sign-On (SSO).
- The application receives an identity token to confirm user identity.
- Uses only OpenID Connect.

#### OAuth for API Access
- Allows applications to act on behalf of the user to use an API.
- Uses only OAuth.

#### OIDC + OAuth for Authentication and API Access
- The application receives both an identity token and an access token.
- Used for applications that need to authenticate the user and access APIs on their behalf, such as mobile apps.

### OAuth and OIDC Flows (Grants)

- Implicit Flow (deprecated in OAuth 2.1)
- Resource Owner Password Credentials Flow
- **Authorization Code Flow (recommended)**
- Client Credentials Flow (for service-to-service communication)
- Device Flow (for devices without browsers)
- Client-Initiated Backchannel Authentication (CIBA) Flow

### Authorization Code Flow with OAuth 2.0

1. **Initialization**: A request by the user or application starts the flow for accessing a protected resource.
2. **STS Redirection**: The application redirects the user to the Security Token Service (STS) to authenticate.
3. **STS Request**: The STS initializes the authentication process upon the request.
4. **Authentication Challenge**: The STS prompts the user to authenticate.
5. **User Credentials**: The user submits their credentials to the STS.
6. **Authorization Code Issuance**: The STS issues an authorization code after successful authentication.
7. **Client Callback**: The user's browser is redirected to the application's callback endpoint.
8. **Token Exchange**: The application exchanges the authorization code for tokens.
9. **Token Issuance**: The STS issues the appropriate tokens for the use case.
10. **Token Handling**: The application handles the tokens as required by the use case.

### Securing OAuth with PKCE

Proof Key for Code Exchange (PKCE) secures the Authorization Code flow, particularly for public clients that cannot securely store a client secret.

#### PKCE Flow Steps

1. **Code Verifier Creation**: The client generates a cryptographically random string as the code verifier.
2. **Code Challenge Calculation**: The client calculates a SHA256 hash of the code verifier to create the code challenge.
3. **Authorization Request**: The client starts the flow by sending the code challenge to the STS during the authorization request.
4. **Code Verifier Storage**: The client securely stores the code verifier, associating it with the user's session.
5. **STS Authentication**: The STS prompts the user for authentication and receives the code challenge.
6. **User Authentication**: The user authenticates with the STS.
7. **Code Challenge Association**: The STS stores the code challenge with the issued authorization code.
8. **Redirect to Client**: The STS redirects the user to the client with the authorization code.
9. **Code Exchange**: The client sends the authorization code and code verifier to the STS.
10. **Code Challenge Verification**: The STS validates the code verifier against the stored code challenge.
11. **Token Issuance**: The STS issues the tokens if the code verifier is confirmed.

#### PKCE Details

- Code verifier: A high-entropy cryptographic random string, between 43 and 128 characters.
- Code challenge: A Base64 URL-encoded SHA256 hash of the code verifier, ensuring a secure connection between the challenge and verifier.

### Refresh Token Flow

The Refresh Token Flow in OAuth 2.0 is a critical mechanism designed to provide applications with new access tokens without further user interaction. This process ensures that even if an access token is compromised, it has a limited window of vulnerability.

#### Detailed Steps:

1. **Request New Access Token**: When an access token nears expiration, the client application requests a new one by sending the refresh token along with its authentication credentials to the Security Token Service (STS).

2. **Access Token Issued**: Upon successful validation of the refresh token and client credentials, the STS issues a new access token.

3. **API Request with New Access Token**: The client uses the new access token to securely access the backend API.

4. **API Response**: The backend processes the API call and returns the response to the client.

#### Strategic Notes on Refresh Tokens:

- **Proactive Token Refresh**: Client applications typically request a new access token before the current one expires, minimizing downtime.

- **Infrequent One-Shot Access**: For sporadic API access, clients might avoid storing access tokens and instead use a refresh token to request a new access token as needed.

#### Security Considerations:

- **Sensitivity**: Refresh tokens should be protected as stringently as user credentials, as they allow for the issuance of new access tokens.

- **Compromise Impact**: If both the refresh token and client authentication credentials are compromised, users are exposed to significant risk.

- **Confidential Storage**: Ensuring the secure storage of refresh tokens is a minimal security requirement, typically achieved through encryption and secure storage practices.

### User Sessions with the STS

The management of user sessions is exclusively under the control of the STS, which dictates how sessions are created, maintained, and terminated.

#### Considerations:

- **Active Sessions**: As long as the user maintains an active session with the STS, no logout occurs, and the session can be reused for subsequent authorization flows.

- **Single Logout**: Deciding to implement Single Logout is often necessary for application architectures, where logging out from one application should terminate sessions across all applications.

### The 'Prompt' Parameter

The `prompt` parameter influences user interaction during the authentication process, defined in the OIDC specification and commonly supported by OAuth 2.0:

- **Options**:
   - `none`: No user interaction is required, suitable for silent authentication.
   - `login`: The user is prompted to re-authenticate.
   - `consent`: The user is asked to grant permission again.
   - `select_account`: The user is prompted to select an account.

### OAuth 2.0 and OIDC for Mobile Apps

Mobile applications use a variant of the Authorization Code Flow with PKCE, which is optimized for environments where securely storing secrets is not feasible.

#### Detailed Steps for Mobile Apps:

1. **Embedded System Browser**: A web view is launched to initiate authentication with the STS, creating a secure environment separate from the main browser.

2. **Code Challenge Creation**: The mobile app generates a code verifier and calculates its SHA256 hash, known as the code challenge, enhancing security through PKCE.

3. **Secure Storage of Verifier**: The code verifier is securely stored on the device, ensuring that only an application with the correct verifier can exchange the authorization code for tokens.

4. **Authentication Request**: The mobile app requests user authentication from the STS, initiating the flow.

5-6. **User Credentials**: The user provides their credentials to the STS for authentication.

7. **Code Challenge Association**: The STS associates the authorization code with the code challenge, linking the authentication process to the initial request.

8-9. **Redirect and Authorization Code**: Upon successful authentication, the STS redirects back to the app with the authorization code.

10. **Code Exchange**: The mobile app exchanges the authorization code and code verifier for tokens.

11. **Verifier Validation**: The STS validates the code verifier against the stored code challenge to ensure the integrity of the token exchange process.

12. **Token Issuance**: The STS issues the access and refresh tokens to the mobile app.

13. **Token Handling**: The app securely handles the tokens, using the access token for API requests and the refresh token for obtaining new access tokens.
