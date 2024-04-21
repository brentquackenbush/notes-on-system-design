# System Design of Object Storage

## Introduction
This document presents a detailed system design for an object storage system akin to Amazon S3. The design focuses on creating a robust, scalable, and secure storage solution that can manage vast amounts of data efficiently. This system will provide functionality for storing, retrieving, and managing data through a simplified interface modeled on real-world object storage patterns.

## System Components

### Bucket
- **Definition**: A bucket serves as a logical container for storing objects. Each bucket is identified by a unique name which is globally unique across the system.
- **Functionality**: Before any data can be uploaded, a user must create a bucket. The bucket acts as the primary grouping of stored data, categorizing objects for organized access and management.

### Object
- **Definition**: An object represents a discrete unit of data stored within a bucket. Each object is composed of two main parts: the object data (payload) and the metadata.
    - **Object Data**: The payload can be any sequence of bytes, which encapsulates the data the user wishes to store.
    - **Metadata**: Accompanies the payload and consists of a set of name-value pairs that provide essential information about the object (e.g., content type, size, creation date, permissions).

### Versioning
- **Definition**: Versioning is a system feature that maintains multiple versions of an object within the same bucket. This feature enhances data integrity and recovery capabilities.
- **Activation**: Versioning is enabled at the bucket level and can be toggled by the user as per their data retention requirements.

### Uniform Resource Identifier (URI)
- **Purpose**: The system provides RESTful APIs to access and manipulate resources, namely buckets and objects.
- **Functionality**: Each resource in the system is uniquely identified through a URI, facilitating straightforward integration with various client applications.

### Service-Level Agreement (SLA)
- **Definition**: The SLA is a formal contract that outlines the expected level of service provided by the object storage system.
- **Commitment**: The SLA guarantees an availability of 99.9999% and durability of 99.999999%, ensuring high reliability and trustworthiness of the storage service.

## System Requirements

### Functional Requirements
- **Bucket Operations**: Create, delete, and list buckets.
- **Object Operations**: Upload, download, delete, and list objects within buckets.
- **Versioning**: Maintain historical versions of objects to enable rollback and historical data access.
- **Capacity**: Support for storing objects ranging from tens of KBs to several GBs in size.

### Non-functional Requirements
- **Durability**: Achieve data durability of 99.999999% to minimize the risk of data loss.
- **Availability**: Ensure system availability of 99.9999%, enabling reliable access to the storage resources.
- **Scalability**: Design to efficiently handle growth in data volume and request rates without degradation of performance.
- **Security**: Implement robust authentication and authorization mechanisms to protect data and access to system resources.
- **Maintenance**: Facilitate easy maintenance and upgrades without significant downtime.

## Key Properties of Object Storage

Object storage is characterized by its unique properties which differentiate it from other storage systems:

- **Immutability**: Objects in object storage are immutable, meaning they cannot be altered once created. Changes require the creation of a new version of the object rather than incremental modifications.
- **Key-Value Store**: The system utilizes object URIs as keys to retrieve the corresponding object data, which serves as the value.
- **Write Once, Read Many (WORM)**: The data access pattern for object storage typically involves writing data once and then reading it many times, which optimizes for read-heavy workloads.

### Object Storage vs. UNIX File System

The design philosophy of object storage shares similarities with that of the UNIX file system:

- **File Identification**: In UNIX, files are identified and accessed through an inode structure, which stores metadata and points to the file's data blocks. Object storage replicates this approach by using a metadata store to manage object information and a data store to house the object data itself.
- **Separation of Concerns**: Just as UNIX separates the inode from the file blocks, object storage separates metadata from data. This simplifies the design and enables independent optimization of each component.

## High-Level System Design Overview

Our object storage system is structured with several key components:

### Load Balancer

- **Purpose**: Distributes incoming RESTful API requests to various API servers to ensure even load distribution and high availability.

### API Service

- **Function**: Handles the orchestration of remote procedure calls to identity and access management services, metadata services, and data stores.
- **Scaling**: Designed to be stateless to enable horizontal scaling.

### Identity and Access Management (IAM)

- **Role**: Acts as the central authority for authentication and authorization.
- **Authentication**: Verifies user identities.
- **Authorization**: Validates the operations a user can perform based on their identity.

### Data Store

- **Function**: Manages the storage and retrieval of the actual object data, with operations keyed by the object ID (UUID).

### Metadata Store

- **Purpose**: Stores all the metadata related to objects, such as the object name, ID, version, and other pertinent information.

## Uploading an Object Workflow

Uploading an object involves several steps that interact with different parts of the system:

1. **Create Bucket**: A client initiates an upload by sending an HTTP PUT request to create a new bucket.
2. **Object Upload**: The client then sends another HTTP PUT request to upload the object into the created bucket.
3. **Metadata Creation**: The system creates metadata for the new bucket and the object being uploaded.

The above components and workflow outline the critical aspects of the object storage system. Together, they form a resilient, scalable, and efficient architecture that is foundational to the operation of our S3-like storage solution.
