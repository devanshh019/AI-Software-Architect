# Amazon (Amazon.com E-Commerce)

## Overview

* **Domain:** E-Commerce, Retail, Cloud Computing (AWS as a separate but related business)
* **Product Type:** Online marketplace and retail platform
* **Estimated Scale:** Hundreds of millions of customers worldwide; millions of sellers; one of the largest e-commerce platforms globally
* **Primary Users:** Consumers, third-party sellers, Amazon-branded sellers/fulfillment partners

## Core Features

* Product catalog browsing and search
* Shopping cart and checkout
* Order management and fulfillment tracking
* Personalized recommendations
* Reviews and ratings
* Prime membership benefits (shipping, video, etc.)
* Third-party marketplace seller tools

## High-Level Architecture

Amazon.com is one of the most cited origin stories of microservices architecture in the industry. It began as a large monolithic application (internally called "Obidos") and, in the early 2000s, was deliberately decomposed into independently deployable services, each owned by small autonomous teams ("two-pizza teams") communicating only through well-defined service interfaces/APIs. This service-oriented approach is widely credited as a precursor to AWS itself, since the internal need for a service-based infrastructure platform led Amazon to build internal tools that were eventually externalized as AWS (EC2, S3, DynamoDB, SQS, etc.). Amazon.com today runs on a vast number of internal microservices, most of which run on AWS's own infrastructure (the company being both AWS's largest customer and its creator).

## Frontend

* **Technologies:** Server-rendered pages historically; modern front-end uses a mix of internal JS frameworks and React-based components in newer surfaces
* **Client Applications:** Web (amazon.com and country-specific domains), iOS app, Android app, Alexa integration
* **Rendering Strategy:** Hybrid server-side rendering with client-side enhancement for interactive components (cart, recommendations widgets)
* **Mobile Stack:** Native iOS (Swift/Objective-C) and Android (Kotlin/Java) apps

## Backend

* **Architecture Style:** Service-Oriented Architecture (SOA)/Microservices, pioneered internally before the term "microservices" was popularized
* **Main Services:** Catalog service, Search/Discovery service, Cart service, Order Management service, Payments service, Recommendations service, Fulfillment/Inventory service
* **Service Communication:** Internal RPC frameworks and REST/HTTP-based service calls; asynchronous messaging via SQS/SNS for decoupled workflows
* **API Type:** REST internally for most services; some internal RPC protocols predate REST adoption

## Databases

* **Primary Database:** DynamoDB (Amazon's own NoSQL database, originally built to address Amazon.com's scaling needs and later externalized as an AWS product)
* **Secondary Databases:** Amazon Aurora/RDS (relational workloads), Oracle (historically used extensively before migration efforts to internal databases), ElastiCache-backed stores
* **Data Model:** Key-value/document model (DynamoDB) for high-scale, low-latency lookups (e.g., cart, sessions); relational model for order/financial data requiring strong consistency
* **Replication Strategy:** Multi-region replication via DynamoDB Global Tables and Aurora cross-region replicas
* **Sharding Strategy:** DynamoDB's automatic partitioning based on hashed partition keys; manual sharding historically for legacy relational systems

## Caching Layer

* **Cache Technology:** Amazon ElastiCache (Redis/Memcached-compatible)
* **Cache Usage:** Caching product catalog data, session data, frequently accessed pricing/inventory data
* **Eviction Strategy:** TTL and LRU-based eviction policies configured per use case

## Message Queue / Event Streaming

* **Technology:** Amazon SQS, Amazon SNS, Amazon Kinesis, Apache Kafka (used in some internal/AWS-adjacent workloads)
* **Purpose:** Decoupling order processing workflows, inventory updates, notification delivery
* **Event Types:** Order placed/updated events, inventory change events, shipping/tracking events, recommendation signal events

## Search Infrastructure

* **Search Engine:** Amazon's internal search platform (A9, Amazon's search and advertising technology subsidiary) built on custom and open-source-derived search technology
* **Search Use Cases:** Product search, search-based advertising, category/faceted navigation

## Storage Systems

* **Object Storage:** Amazon S3 (used both internally and as the externalized AWS product)
* **File Storage:** S3 and internal blob storage systems for product images, documents
* **CDN:** Amazon CloudFront

## Real-Time Systems

* **Real-Time Components:** Real-time inventory updates, real-time order tracking, real-time pricing updates
* **Protocols:** HTTPS/REST for client-facing updates; internal event-driven protocols for backend propagation
* **Streaming Technologies:** Amazon Kinesis for real-time data streaming and analytics

## Authentication & Authorization

* **Authentication Method:** Username/password with optional multi-factor authentication; Amazon account federated across Amazon properties (Amazon.com, AWS, Prime Video, etc.)
* **Session Management:** Token/cookie-based session management
* **Security Features:** Multi-factor authentication, device/browser fingerprinting, fraud detection systems for transactions

## Scalability Strategy

* **Horizontal Scaling:** Independently scalable microservices, each owned by autonomous teams, scaled on AWS infrastructure
* **Load Balancing:** AWS Elastic Load Balancing (ELB/ALB) used extensively
* **Auto Scaling:** AWS Auto Scaling Groups and container-based scaling for services running on internal AWS infrastructure
* **Geographic Distribution:** Multi-region deployment across AWS's global regions and availability zones

## Reliability & Fault Tolerance

* **Redundancy:** Multi-AZ and multi-region redundancy for critical services and data stores
* **Failover Strategy:** Automated failover within AWS infrastructure (Route 53 health checks, multi-AZ failover for RDS/Aurora)
* **Disaster Recovery:** Cross-region backup and replication strategies; extensive use of AWS-native DR tooling

## Monitoring & Observability

* **Logging:** Centralized logging via internal tooling and AWS CloudWatch Logs
* **Monitoring:** AWS CloudWatch and internal monitoring dashboards
* **Tracing:** AWS X-Ray and internal distributed tracing tooling
* **Alerting:** CloudWatch Alarms integrated with internal on-call/incident response systems

## Security Measures

* **Encryption:** TLS in transit; encryption at rest via AWS KMS-managed keys for data stores
* **Access Control:** AWS IAM for service-to-service and operator access control
* **Threat Protection:** Fraud detection ML models, AWS Shield/WAF-class protections against DDoS and web attacks

## Engineering Challenges

* Decomposing an early monolith into thousands of independent services without breaking site reliability ("two-pizza team" reorganization)
* Building a database (DynamoDB) capable of handling Amazon's holiday-peak traffic spikes (e.g., Prime Day, Black Friday) reliably
* Maintaining strong consistency for orders/payments while using eventually-consistent systems elsewhere
* Managing massive product catalog search/ranking and personalization at scale

## Known Technologies Used

Java, C++, Perl (historically for early systems), DynamoDB, Aurora/RDS, S3, SQS, SNS, Kinesis, ElastiCache, CloudFront, AWS Lambda, AWS IAM, A9 search technology, React (in some modern surfaces)

## Architecture Patterns

* **Service-Oriented Architecture / Microservices** (Amazon is widely credited as an early large-scale pioneer of this pattern)
* **Event-Driven Architecture** for order/inventory workflows
* **CQRS** in some read-heavy catalog/search services
* **Saga pattern** for distributed order fulfillment workflows spanning payments, inventory, and shipping
* **Other Patterns:** API-first service contracts, eventual consistency for non-critical-path data

## System Design Lessons

Amazon's internal mandate that all teams expose functionality only through well-defined service APIs — rather than direct database access — is one of the most influential architectural decisions in the industry, directly enabling both internal scalability and, eventually, the creation of AWS itself. It demonstrates how organizational structure (autonomous "two-pizza teams") and architecture (service boundaries) can be designed together to support both technical scalability and engineering velocity at enormous scale.

END OF DOCUMENT
