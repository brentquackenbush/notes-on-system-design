# SystemDesignNotes


## Indexing Data Structures in Database Systems

A comprehensive understanding of indexing data structures is crucial for database management and system design. Below, we discuss various data structures and their significance in indexing.

### Skiplist

**What it is:** 
A skiplist is a data structure that allows fast search within an ordered sequence of elements. It consists of multiple layers with the bottom layer containing all elements and each higher layer serving as an express lane with fewer elements for quicker access.

**Analogy:** 
It's like a multi-level shopping mall where express escalators take you past a selection of floors directly to your destination level quickly.

**Use case:** 
Used in Redis for its capabilities of fast insertion, deletion, and lookup operations which are vital for a caching system's performance.

### Hash Index

**What it is:** 
A hash index is implemented using a hash table where a hash function maps keys to array indices correlating to value storage locations.

**Analogy:** 
Like a book index that tells you the exact pages to find a word on, allowing direct access without needing to read the entire book.

**Use case:** 
Ideal for direct point queries with known keys, typically employed for primary key lookups in databases.

### SSTable (Sorted String Table)

**What it is:** 
An SSTable is a read-only data structure storing key-value pairs in sorted order on disk, facilitating efficient range queries.

**Analogy:** 
Similar to a telephone directory that lists contacts by last name, enabling quick searches for a set of surnames like all names starting with "M".

**Use case:** 
Used by systems like Bigtable and Cassandra for managing large datasets where sorted data enhances the efficiency of range scans.

### LSM tree (Log-Structured Merge-Tree)

**What it is:** 
An LSM tree is a strategy that combines the in-memory efficiency of skiplists for writes with the on-disk persistence and organization of SSTables.

**Analogy:** 
Comparable to jotting quick notes on sticky notes (in-memory) and later sorting and pasting them into a journal (on-disk) at the day's end.

**Use case:** 
Optimized for environments with high write volume, reducing disk I/O which is common in databases like LevelDB.

### B-tree

**What it is:** 
A B-tree is a self-balancing tree structure that keeps data sorted and allows efficient operations logarithmically with respect to the number of items.

**Analogy:** 
Resembles a filing cabinet with well-organized folders allowing quick access, addition, or removal of files.

**Use case:** 
Widely used in SQL databases such as MySQL for achieving consistent performance across read and write operations.

### Inverted Index

**What it is:** 
An inverted index associates content like words or numbers with locations in a document or a set of documents.

**Analogy:** 
Works as the reverse of a book index by providing the locations of words across documents, instead of content for a specific page.

**Use case:** 
Extremely useful in search engines such as Lucene, which is at the core of Elasticsearch, for full-text searches.

### Suffix Tree

**What it is:** 
A suffix tree represents all possible suffixes of a given string as paths from a common root, optimizing substring searches.

**Analogy:** 
Imagine a family tree for words, with endings branching out, showing the lineage or pattern connection for substrings.

**Use case:** 
Primarily used for quick pattern searches in strings, such as DNA sequencing in bioinformatics.

### R-tree

**What it is:** 
An R-tree indexes spatial data, handling dimensions efficiently for queries on geographic location-based data.

**Analogy:** 
Like a map sectioned into areas and subareas, an R-tree assists in location-based queries within specified bounds.

**Use case:** 
Employed in GIS, spatial database access, and for indexing multidimensional information in computer graphics.

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

## Load Balancer and API Gateway: An Overview

### Load Balancer
Its primary function is to distribute incoming network traffic across multiple servers to ensure no single server becomes overloaded, improving the reliability and availability of applications. Load balancers operate at various levels of the OSI model; some work on the transport layer (Layer 4 - TCP/UDP) to distribute traffic based on IP range or port number, while others work on the application layer (Layer 7 - HTTP/HTTPS) to distribute requests based on content type, headers, cookies, or application-specific data.

### API Gateway
It acts as a front-door to facilitate and manage incoming API requests to backend services. An API gateway typically handles a host of concerns, such as request routing, composition, and protocol translation, and often includes additional responsibilities like authentication, rate limiting, and analytics.

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

## Detailed Interaction

### Traffic Entry
Requests enter the system and hit the load balancer first. The load balancer performs health checks on API gateway instances to ensure traffic is only sent to operational nodes.

### SSL Termination
If SSL Termination is used, the load balancer decrypts the request to read certain details if needed for intelligent distribution, then re-encrypts if required before passing it along.

### Routing to API Gateway
After initial processing, the load balancer forwards the request to the API gateway. If the system uses multiple gateways for different services or API versions, the load balancer can direct traffic based on the intended destination.

### Processing by API Gateway
The API gateway receives the request from the load balancer. It then performs various functions such as authentication, rate limiting, and request shaping. The gateway reads the request headers and URL to determine how to route the request to the appropriate services.

### Service Invocation
Finally, the API gateway sends the request to the correct service within the internal network. This service then processes the request and sends the response back through the API gateway and load balancer to the client.

