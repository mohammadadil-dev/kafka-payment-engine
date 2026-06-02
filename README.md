# Kafka Payment Engine

A reference implementation and architecture guide for building scalable, event-driven payment processing systems using Apache Kafka, Spring Boot, PostgreSQL, and distributed system design principles.

## Overview

This repository demonstrates how modern payment systems can leverage Apache Kafka for reliable, scalable, and asynchronous transaction processing.

The architecture focuses on:

* Payment Initiation
* Event-Driven Processing
* Payment Status Tracking
* Retry Mechanisms
* Dead Letter Topics (DLT)
* Idempotency
* Distributed Processing
* Observability

## Architecture

```mermaid
flowchart TD

    A[Client Application]

    A --> B[Payment API]

    B --> C[(PostgreSQL)]

    B --> D[Kafka Topic<br/>payment-initiated]

    D --> E[Payment Processor]

    E --> F[External Payment Gateway]

    F --> G[Payment Status Update]

    G --> H[Kafka Topic<br/>payment-completed]

    H --> I[Notification Service]

    H --> J[Audit Service]
```

## Payment Lifecycle

```mermaid
sequenceDiagram

Client->>Payment API: Create Payment

Payment API->>Database: Save Transaction

Payment API->>Kafka: PaymentInitiated

Kafka->>Payment Processor: Consume Event

Payment Processor->>Gateway: Process Payment

Gateway-->>Payment Processor: Success

Payment Processor->>Kafka: PaymentCompleted

Kafka->>Notification Service: Notify Customer

Kafka->>Audit Service: Audit Event
```

## Core Components

| Component               | Responsibility                  |
| ----------------------- | ------------------------------- |
| Payment API             | Accept payment requests         |
| Kafka Producer          | Publish payment events          |
| Payment Processor       | Process payments asynchronously |
| Payment Gateway Adapter | External gateway integration    |
| Status Service          | Track transaction lifecycle     |
| Notification Service    | Customer notifications          |
| Audit Service           | Compliance & audit logging      |

## Kafka Topics

| Topic              | Purpose              |
| ------------------ | -------------------- |
| payment-initiated  | New payment requests |
| payment-processing | Processing status    |
| payment-completed  | Successful payments  |
| payment-failed     | Failed payments      |
| payment-retry      | Retry queue          |
| payment-dlt        | Dead Letter Topic    |

## Event Flow

```mermaid
flowchart LR

A[Payment API]
--> B[payment-initiated]

B --> C[Payment Processor]

C --> D[External Gateway]

D --> E[payment-completed]

D --> F[payment-failed]
```

## Retry & Dead Letter Topic Strategy

```mermaid
flowchart LR

A[Payment Event]
--> B[Consumer]

B --> C{Success?}

C -->|Yes| D[Payment Completed]

C -->|No| E[Retry Topic]

E --> F[Retry Consumer]

F --> G{Retry Limit Reached?}

G -->|No| B

G -->|Yes| H[Dead Letter Topic]
```

## Idempotency Design

Financial systems must prevent duplicate payment processing.

### Strategy

* Unique Payment Reference
* Idempotency Key
* Database Constraints
* Consumer Deduplication

Example:

```text
Payment Reference:
PAY-20260601-1001

Duplicate Event:
Rejected

Existing Payment:
Returned
```

## Reliability Patterns

### Retry Mechanism

* Exponential Backoff
* Configurable Retry Count
* Retry Topics

### Dead Letter Topics

Failed messages are moved to DLT after maximum retries.

### Consumer Groups

Consumer groups enable horizontal scaling and fault tolerance.

### At-Least-Once Processing

Kafka guarantees message delivery while idempotency protects against duplicate processing.

## Technology Stack

| Layer      | Technology         |
| ---------- | ------------------ |
| Backend    | Java, Spring Boot  |
| Messaging  | Apache Kafka       |
| Database   | PostgreSQL         |
| Cache      | Redis              |
| Monitoring | ELK, Grafana       |
| Security   | OAuth2, JWT        |
| Deployment | Docker, Kubernetes |

## Monitoring

Important business metrics:

* Payments Initiated
* Payments Completed
* Payments Failed
* Retry Count
* DLT Count
* Gateway Latency
* Processing Time

## Future Enhancements

* Saga Pattern
* Outbox Pattern
* Event Sourcing
* Multi-Gateway Support
* Fraud Detection Engine
* AI-Powered Transaction Monitoring

## Use Cases

* Digital Lending Platforms
* Payment Gateways
* Wallet Systems
* Banking Applications
* FinTech Platforms
* Transaction Processing Systems

## Author

Mohammad Adil

🏦 FinTech Backend Lead
☕ Java Architect
⚡ Kafka & Microservices
🤖 AI Engineer
