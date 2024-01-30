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

