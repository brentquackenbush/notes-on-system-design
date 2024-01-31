# Currency Exchange Platform System Design

I want to focus on these core features of a currency exchange, sending money, receiving money, holding money in various currencies, and managing accounts. 

## Functional Requirements

- **Account Creation and Verification**: Users should be able to create accounts, both individual and business, and undergo a verification process
- **Account Management**: Users must be able to view their transaction history, manage their personal details, and set up security features like two-factor authentication
- **Money Transfer**: The system should enable users to transfer money locally and internationally in multiple currencies. This includes entering the amount, recipient details, and choosing a payment method.
- **Currency Exchange**: Users should have the ability to exchange money into different currencies using the mid-market exchange rate. 
- **Receiving Money**: The system should facilitate receiving money from both the application users and non-application users into the user's account
- **Notification and Alerts**: Real-time notifications for transactions and account activities. 

## Non-Functional Requirements

- **Security**: High-level security measures including data encryption, fraud detection, and compliance with financial regulations. (Regular security audits and compliance with regulations like GDPR, PCI DSS.)
- **Scalability**: The system should be scalable to handle a large number of transactions and users.
- **Availability**: High availability, especially for the transaction and currency exchange services.
- **Performance**: Fast processing of transactions and currency exchanges with minimal latency.
- **Compliance**: Adherence to international financial regulations and data protection laws.

### CAP Theorem Consideration

In the context of a Currency Exchange system, the most critical aspects are **Consistency** and **Partition Tolerance**. Financial transactions demand high accuracy (Consistency), and the system should continue to function even if part of the system fails or is partitioned off (Partition Tolerance). Sacrificing some level of Availability is acceptable to ensure that transactions are always consistent and the system can handle network partitions effectively.

## API Design

Before we start, I want to mention that it is of up-most importance we comply with local financial laws and regulations. We need to ensure adherence to Anti-Money Laundering [AML](https://sanctionscanner.com/blog/aml-and-kyc-guide-for-mobile-payment-providers-185) directives and Know Your Customer [KYC](https://sanctionscanner.com/blog/aml-and-kyc-guide-for-mobile-payment-providers-185) standards. This involves validating user identities and monitoring transactions for suspicious activities.

**Why Compliance is Important?** It's crucial for preventing money laundering and terrorist financing, complying with financial regulations, and maintaining the trust of users and regulators.

### Money Transfer API

- **Endpoint**: `POST` `/v1/transfers`: Initiates a money transfer.

- **Parameters**:
```json
{
  "senderAccountId": "String",
  "recipientAccountId": "String",
  "amount": "BigDecimal",
  "currency": "String",
  "transferType": "String", // e.g., "domestic", "international"
  "purpose": "String" // e.g., "personal", "business"
}
```

---

- **Endpoint** `GET` `/v1/transfers/{transferId}`: Retrieves the status of a transfer.

- **Parameters**:
```json
{
  "transferId": "String"
}
```

### Currency Exchange API

- **Endpoint**: `POST` `/v1/exchange` Exchange currency.

- **Parameters**:
```json
{
    "userId": "string",
    "fromCurrency": "string",
    "toCurrency": "string",
    "amount": "decimal"
}
```

**Functionality**: Convert amount from fromCurrency to toCurrency based on real-time exchange rates. Apply transaction fees if applicable.

**Compliance**: Ensure the transaction complies with cross-border financial regulations, including real-time rate monitoring for market manipulation.

---

- **Endpoint**: GET /api/v1/exchange/rates

- **Parameters**: 
```json
{
  "fromCurrency":  "String", 
  "toCurrency": "String"
}
```

- **Response**:
```json
{
"rate": "decimal",
"lastUpdated": "timestamp"
}
```

**Functionality**: Provide the latest exchange rate for the specified currency pair. Useful for pre-transaction quotes.

### Account Management API


- **Endpoint**: `GET` `/accounts/{userId}`: Retrieves account details.

- **Parameters**: 
  - **userId**: String

**Response**:
```json
{
"userId": "string",
"email": "string",
"accounts": [
  {
    "accountId": "string",
    "currency": "string",
    "balance": "decimal"
    }
  ]
}
```

---
- **Endpoint**: `PUT` `/accounts/{userId}`: Updates account information.

- **Parameters**: 
  - **userId**: String, 
  - **accountDetails**:

## High Level Design

```
   +------------+                     +-------------+
   |            |                     |             |
   |   Client   +-------------------->+ Load Balancer|
   |  (Browser, +<--------------------+             |
   |   Mobile)  |                     +------+------+
   +------------+                             |
                                              |
                                              |
                                              v
+-------------+  +----------------+  +----------------+  +----------------+
|             |  |                |  |                |  |                |
| User Service|  | Transfer       |  | Exchange       |  | Notification   |
| (Account    |  | Service        |  | Service        |  | Service        |
| Management, |  | (Money         |  | (Currency      |  | (Alerts &      |
| Verification)| | Transfer)      |  | Exchange)      |  | Notifications) |
|             |  |                |  |                |  |                |
+------+------+  +-------+--------+  +-------+--------+  +-------+--------+
       |                 |                   |                   |
       |                 |                   |                   |
       |                 |                   |                   |
       +-----------------+-------------------+-------------------+
                                     |
                                     |
                                     v
                             +-------+-------+
                             |               |
                             |   Database    |
                             |               |
                             +-------+-------+
                                     |
                                     |
                                     v
                             +-------+-------+
                             |               |
                             |     Cache     |
                             |               |
                             +---------------+

```

## Low Level Design

### Using PostgreSQL for Account Management and Transactions

- **ACID Compliance**:
  - Importance: Essential for financial transactions to ensure the integrity of account balances and transaction histories.
  - Guarantees that all transactions are processed reliably, preventing issues like partial updates or inconsistent data states.

- **Complex Queries**:
  - PostgreSQL excels at handling complex queries crucial for account management, such as calculating balances and generating financial statements.

- **Relationships**:
  - PostgreSQL's relational model effectively manages relationships between entities like users, accounts, and transactions.

- **Security and Compliance**:
  - PostgreSQL offers robust security features vital for protecting sensitive financial data and ensuring regulatory compliance.
  - Explore the intricacies of PostgreSQL security through an insightful article by **Dave Page**, a distinguished member of PostgreSQL's core team. His piece, [How to Secure PostgreSQL: Security Hardening Best Practices & Tips](https://www.enterprisedb.com/blog/how-to-secure-postgresql-security-hardening-best-practices-checklist-tips-encryption-authentication-vulnerabilities)

- **Handling Data Anomalies**:
  - Prevents "dirty writes" and race conditions, common in non-ACID compliant databases, ensuring reliable and consistent transaction processing.

### Leveraging Cache

Let's talk about giving our system a turbo boost. How? By smartly caching currency rates. Imagine this: every time someone asks for a currency rate, we go out and fetch it from the Forex market. That's a lot of trips for something that changes just a few times a day, right?

#### Enter Redis: Our Caching Superhero
- **Why Redis?** It's like having a super-fast memory bank. Redis stores data right where we can access it instantly. This means we can remember the latest currency rates without having to ask the Forex market every time.
- **Staying Up-to-Date**: We'll make sure our Redis cache doesn't hold onto outdated rates for too long. It's all about giving you the freshest data without the wait.
- **Plan B**: Missed the rate in our cache? No worries, we'll fetch it and remember it for next time.

In short, by using Redis, we're not only speeding things up but also making sure you always get the latest and greatest rates without unnecessary delays. Fast, efficient, and smart - that's how we like it!

### Supercharging Our System with Apache Kafka

Let’s explore how Apache Kafka, our data handling powerhouse, is driving efficiency in our currency exchange platform.

#### Kafka: The Heartbeat of Our Messaging System

- **The Heavy Lifter**: Kafka is our go-to for managing the deluge of messages, adept at handling billions seamlessly – vital for our Money Transfer and Currency Exchange operations.
- **Reliability is Key**: Kafka's distributed setup ensures we're always up, avoiding any service interruptions.
- **Speed is King**: For real-time data streaming and quick transaction processing, Kafka is unbeatable.
- **Seamless Communication**: Kafka ensures smooth, orderly messaging across services like Account Management and Currency Exchange.

#### Kafka in Action

- **Money Transfer Service**: Kafka meticulously queues and processes each money transfer request, guaranteeing efficient and orderly handling.
- **Currency Exchange Service**: Acting like a real-time news channel, Kafka disseminates current currency exchange rates, ensuring our customers get the best deals.
- **Account Management Service**: Kafka meticulously records every account activity, aiding in accurate auditing and compliance.

#### Our Kafka Strategy

- **Producers and Consumers**: Our services communicate seamlessly with Kafka, ensuring a synchronized flow of information.
- **Stream Processing**: Kafka isn't just about messaging; it's a whiz at real-time data analysis, making sense of our operations on the go.

With Kafka, we’re not just keeping pace – we’re setting the pace in the financial tech world.

