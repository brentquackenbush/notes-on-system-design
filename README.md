# SystemDesignNotes

## Table of Contents
1. [Indexing Data Structures in Database Systems](#indexing-data-structures-in-database-systems)
    - [Skiplist](#skiplist)
    - [Hash Index](#hash-index)
    - [SSTable (Sorted String Table)](#sstable-sorted-string-table)
    - [LSM tree (Log-Structured Merge-Tree)](#lsm-tree-log-structured-merge-tree)
    - [B-tree](#b-tree)
    - [Inverted Index](#inverted-index)
    - [Suffix Tree](#suffix-tree)
    - [R-tree](#r-tree)
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

<a name="indexing-data-structures-in-database-systems"></a>
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
