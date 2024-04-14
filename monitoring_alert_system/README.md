# Metrics Monitoring and Alerting System

 > This document outlines the high-level design of a monitoring and alerting system that is capable of handling metrics at scale. The design ensures the collection, processing, and alerting of system metrics to maintain high availability and reliability of services.

### Pre-Design Knowledge Requirements
Before attempting to design a metrics monitoring and alerting system, one should be familiar with the following high-level components:

- **Data Collection and Ingestion**: [Understanding the methods and technologies used to gather and ingest data from various sources.](https://levelup.gitconnected.com/common-data-ingestion-use-cases-and-methods-in-data-engineering-a-brief-introduction-a2849c07f4a)
- **Message Queues and Brokers**: [Knowledge of how to buffer and distribute messages across the system, often using a message broker like Kafka.](https://digitalscholarship.unlv.edu/cgi/viewcontent.cgi?article=4749&context=thesesdissertations)
- **Storage and Databases**: Familiarity with different types of databases, particularly time series databases, which are optimized for storing and querying time-stamped data.
- **Data Processing and Computation**: Insight into processing pipelines and real-time computation frameworks to analyze metrics.
- **Caching Mechanisms**: Comprehending the use of caching to reduce database load and improve query response times.
- **Querying and API Design**: Understanding how to design APIs for querying data and the importance of efficient query languages.
- **Visualization Tools**: Awareness of tools and platforms for visualizing data in a user-friendly manner.
- **Alerting and Notification Systems**: Knowledge of systems that can trigger alerts based on predefined metrics thresholds and how to route these notifications through various channels.
- **Scalability and High Availability**: Principles to ensure the system can handle growth in data volume and maintain service during faults or high demand.

### Prominent Technologies in 2024
The following technologies are widely recognized in 2024 for building metrics monitoring and alerting systems:

- **DataDog**: Provides monitoring services for cloud-scale applications, including real-time performance tracking.
- **Prometheus**: An open-source system monitoring and alerting toolkit, often used with Kubernetes.
- **Grafana**: For turning time series database data into beautiful graphs and visualizations.
- **New Relic**: Offers full-stack observability and analytics for software environments.
- **Elasticsearch and Kibana**: A search engine for log and time-series analytics, with Kibana providing visualization capabilities.
- **Splunk**: Specializes in searching, monitoring, and analyzing machine-generated big data via a web-style interface.
- **Nagios**: An open-source computer-software application that monitors systems, networks, and infrastructure.
- **InfluxDB**: A time series database designed to handle high write and query loads.
- **PagerDuty**: An incident management solution that integrates with monitoring tools to provide on-call scheduling and alerting.
- **Apache Kafka**: A distributed streaming platform that is used to build real-time data pipelines and streaming apps.

## System Design

### Understanding the scope

It's important to understand the scope of the system we are asked to build since we only have a certain amount of time to build out a high-level design.

Let's focus on important questions to ask.
- What type of metrics do we want to collect?
- How long should we retain data for?
- What type of alerts do we want to support?
- Is this going to be an in-house system for tech companies needing to evaluate their metrics and alerts, or are we designing a SaaS like `Datadog`?

I will be answering this questions here, so we know what the functional requirements are for this specific system, instead of trying to build out a full-fledged Monitoring and Alert system.

**What type of metrics do we want to collect?**

Operational System Metric. This can be low-level usage data of the OS, such as the memory usage, disk space consumption, and CPU load. Also collecting metrics such as RPS of a service. 

**How long should we retain data for?**

6 months. 

**What type of alerts do we want to support?**

PagerDuty, Webhooks, Email, Phone.

**Is this going to be an in-house system for tech companies needing to evaluate their metrics and alerts, or are we designing a SaaS like Datadog?**

Internal use. 

### Functional Requirements

- **Metrics**: We will collect operational system metrics, such as memory, disk usage, CPU load, and service RPS.
- **Data Retention**: The system will retain metric data for a period of six months.
- **Alert Types**: Supports various alerting mechanisms like PagerDuty, webhooks, emails, and phone calls.
- **Usage**: Designed for internal use by tech companies to monitor and alert on their metrics, not as a SaaS product like Datadog.


### Non-functional Requirements

- **Scalability**: The ability to gracefully handle and adapt to increasing workloads is paramount. Our system will utilize horizontal scaling strategies, with the capability to dynamically add more resources as the demand for metric data ingestion and processing grows.

- **High Availability & Reliability**: Given the critical nature of alerting services, our design will incorporate active-active and active-passive cluster configurations to achieve 99.999% uptime.

- **Performance**: Response time is critical, especially when dealing with time-sensitive metrics and alerts. Our system will optimize data paths, minimize latency, and employ efficient algorithms to process and retrieve metric data in sub-second timescales.

- **Durability & Data Integrity**: Data will be replicated across multiple geographic zones, preventing data loss and ensuring integrity. Write-ahead logging and atomic operations will be standard to guarantee that once a metric is recorded, it's preserved despite system failures.

- **Cost-efficiency**: The architecture will be designed to maximize resource utilization and minimize waste, keeping operational costs in check. This will be achieved by leveraging spot instances, efficient data storage formats, and auto-scaling capabilities.

### Core Functions of the Monitoring System

- **Data Collection**: Efficiently gathers thousands of metrics from various sources without data loss, ensuring scalability.
- **Data Storage**: Manages and retains historical metric data for context analysis, supporting time-based data sampling and aggregation.
- **Data Aggregation**: Summarizes data from multiple sources, providing statistics like averages, counts, and extremes over specific intervals.
- **Data Visualization**: Utilizes diverse visualization tools for efficient trend recognition and system component interaction understanding.
- **Alerting**: Notifies users about critical events or metric thresholds being reached to maintain constant awareness of system health.

### Metrics: Definition and Types

Metrics are the quantitative measures of data, often used to assess the performance, health, or behavior of different components within a system. Here’s an overview of the various types of metrics that a monitoring system might collect:

- **Host Metrics**: These provide insights into the health and performance of individual computers, excluding the services running on them. Common host metrics include CPU usage, disk space, and memory consumption.

- **Application Metrics**: These reflect the efficiency and stability of applications, such as average response times, error rates, request rates, and occurrences of service failures or restarts.

- **Network Performance Metrics**: These metrics are crucial for real-time and long-term assessments of network performance, including packet loss, availability, connectivity, and throughput.

- **Server Pool Metrics**: For a collective group of servers, metrics such as the number of running instances, total instance count, and scaling adjustments are monitored for efficient resource management.

- **External Dependencies Metrics**: When services rely on external APIs or systems, it’s important to monitor these for service status, error rates, and response speeds.

### High-level Design

![High Level Design](https://github.com/brentquackenbush/notes-on-system-design/assets/50753562/5b817e04-dcf2-49fd-b871-ec2a3e8e5a70)

### Metrics Collection Service

The `Metrics Collection Service` forms the initial and one of the most critical components of our monitoring and alerting system. It is designed to interface with a variety of metric sources to ingest data that will be used for monitoring and analysis.

#### Responsibilities

- **Data Harvesting**: Actively collects or pulled/pushed data from metric sources, including application servers, databases, and message queues.
- **Pre-processing**: Standardizes and preprocesses the data to ensure consistency. This may include tasks like normalization, timestamping, and data enrichment.
- **Buffering and Batching**: Implements temporary storage for incoming data, allowing for batch processing which enhances the efficiency of database writes.

#### Improvements for Scalability and Reliability

- **Queueing System**: Integrate Apache Kafka to establish a robust, distributed queue that provides a buffer between metric sources and our collection service.
- **Load Management**: Kafka helps in managing load by acting as a buffer. This ensures that data ingestion is smooth and consistent, preventing data loss during spikes in metric generation.
- **Distributed Processing**: Kafka’s distributed nature allows the Metrics Collection Service to scale horizontally, accommodating an increasing number of metric sources without service degradation.
- **Guaranteed Delivery**: Kafka’s delivery semantics ensure that data is either successfully processed or retried, eliminating the risk of data loss in case of system failures.

> By introducing Kafka into the system design, we ensure that the Metrics Collection Service can handle high volumes of data inflow from diverse sources without compromising on data integrity or system performance.

### Query Service + Database Implementation

The design and implementation of the Query Service are crucial considerations in our monitoring and alerting system. This service acts as the bridge between the time-series database and the client interfaces.

#### Query Service Implementation

The Query Service's primary role is to decouple the time-series database from its clients, which include visualization tools and alerting systems. This separation ensures flexibility and scalability in our architecture by allowing changes to the database or client applications without impacting the other components. A cluster of query servers makes up the Query Service, handling and responding to client requests. To enhance performance, we can introduce a caching layer to temporarily store frequent query results, minimizing the load on the time-series database and expediting data retrieval.

#### Rationale for Excluding a Query Service in SaaS

In scenarios where we are not building a SaaS platform that requires extensive customization and multi-tenancy support, the need for a dedicated Query Service may be superfluous. Modern visualization and alerting systems come equipped with robust plugins that can interface directly with prevalent time-series databases. This can render the intermediary Query Service redundant, simplifying the system design and reducing operational complexity.

#### Integration with Tools Like Prometheus

An exemplar of direct integration without the need for a standalone Query Service is the combination of Grafana and Prometheus:

- **Grafana-Prometheus Integration**: Grafana offers a comprehensive plugin for Prometheus that allows for direct queries against the time-series data stored in Prometheus, without necessitating a Query Service.

    ```plaintext
    Visualization (Grafana) → Prometheus → Time Series Data
    ```

- **PromQL**: Prometheus's specialized query language, PromQL, is tailored for querying time-series data. Grafana harnesses PromQL to empower users to build insightful dashboards that query Prometheus in real time.

- **Alerting via Grafana**: Grafana also facilitates the assessment of metric thresholds and triggers alerts based on data from Prometheus. This integrated alerting capability further reduces the need for a distinct alerting service.

#### Choosing Prometheus as the Time-Series Database

The selection of Prometheus as the time-series database for our monitoring and alerting system is backed by its:

- **Domain-Specific Query Language**: PromQL is purpose-built for time-series data and circumvents the complexities associated with using SQL for such datasets.
- **Native Functionalities**: Prometheus itself includes features that would typically be delegated to a Query Service, like data aggregation and direct alerting capabilities. It allows us to simplify our system design, leaning on the strengths of Prometheus to efficiently manage time-series data.

### Alert Service and Visualization Service

The Alerting System is a critical component, responsible for notifying users of potential issues based on metrics thresholds. Here's how it's typically implemented:

- **Rule-Based Triggering**: Alert rules are defined in configuration files, often in a YAML format, which specify the conditions and thresholds for triggering an alert.

  ```yaml
  rules:
    - alert: InstanceDown
      expr: up == 0
      for: 5m
      labels:
        severity: page
  ```
  
`Alert Manager`: Manages the lifecycle of alerts. It ingests these rules, evaluates metric values against them, and then triggers notifications.

`Alert Deduplication`: To prevent alert fatigue, the system merges similar alerts (e.g., disk usage > 90% on the same instance) into a single notification.

#### Utilizing Existing Tools
Rather than building an alerting system from scratch, we can leverage existing tools:

**Pre-Built Alert Managers**: Tools like Prometheus include their own alert managers that can handle alert rules and notifications out-of-the-box.

#### Visualization System Implementation

A Visualization System takes raw metrics and transforms them into insightful graphical representations.

**Data Layer Integration**: The visualization tool sits on top of the data layer and interfaces directly with the time-series database to retrieve metrics data.

**Dashboard Creation**: It allows users to build dashboards that display key metrics, like memory/CPU usage, page load time, and traffic.

#### Utilizing Existing Tools

The use of established visualization systems offers several advantages:

`Grafana` as a Solution: Grafana is a robust visualization tool that supports a wide range of data sources, including `Prometheus`, and offers a rich set of features for creating dashboards.


`Direct Data Source Plugins`: `Grafana`, for instance, has plugins for various time-series databases that allow users to pull data directly for visualization without additional overhead.