# Netflix

## Overview

* **Domain:** Video Streaming, Entertainment
* **Product Type:** Subscription-based Video-on-Demand (SVOD) platform
* **Estimated Scale:** 260M+ subscribers globally (publicly reported figures vary by quarter); streams in 190+ countries
* **Primary Users:** Consumers streaming movies/TV shows across devices

## Core Features

* Video streaming with adaptive bitrate playback
* Personalized recommendations
* Multi-device support (TV, mobile, web, gaming consoles)
* Offline downloads
* Multiple profiles per account
* Content search and browsing
* A/B testing-driven UI personalization

## High-Level Architecture

Netflix runs almost entirely on AWS (compute, storage, databases) for everything except video delivery, which is handled by Netflix's own global CDN, Open Connect. The backend is one of the most well-documented large-scale microservices architectures in the industry, originally built around Java/Spring Boot services communicating via REST, with the API layer (Zuul gateway) routing client requests to hundreds of backend microservices. Netflix popularized many resilience patterns (circuit breakers via Hystrix, chaos engineering via Chaos Monkey/Simian Army) to operate reliably at massive scale on top of a cloud provider it does not control end-to-end.

## Frontend

* **Technologies:** React.js (Netflix rebuilt much of its web UI on React for performance), JavaScript/TypeScript
* **Client Applications:** Web app, iOS app, Android app, Smart TV apps, gaming console apps
* **Rendering Strategy:** Client-side rendering with server-side personalization APIs; React used for fast, low-memory UI on TVs
* **Mobile Stack:** Native iOS (Swift/Objective-C) and Android (Kotlin/Java) clients

## Backend

* **Architecture Style:** Microservices (hundreds of services)
* **Main Services:** Playback API, Recommendation Engine, Member/Account service, Content metadata service, Billing, Encoding pipeline
* **Service Communication:** REST/HTTP internally, with some gRPC adoption; async messaging for batch/streaming jobs
* **API Type:** REST (via Zuul API Gateway), GraphQL adoption in some newer services (Federated GraphQL Gateway)

## Databases

* **Primary Database:** Cassandra (for high-availability, high-write workloads)
* **Secondary Databases:** MySQL/Amazon Aurora (for relational data), EVCache (Memcached-based), CockroachDB (newer adoption for some services)
* **Data Model:** Wide-column (Cassandra) for viewing history/metadata; relational for billing/account data
* **Replication Strategy:** Multi-region Cassandra replication across AWS regions for high availability
* **Sharding Strategy:** Cassandra's consistent hashing-based partitioning across nodes/regions

## Caching Layer

* **Cache Technology:** EVCache (Netflix's distributed caching layer built on Memcached)
* **Cache Usage:** Caching personalization data, user session data, frequently accessed metadata
* **Eviction Strategy:** TTL-based with LRU-style eviction within Memcached nodes

## Message Queue / Event Streaming

* **Technology:** Apache Kafka, Apache Flink, Spark Streaming
* **Purpose:** Real-time event pipeline for telemetry, viewing activity, and operational metrics ("Keystone" pipeline)
* **Event Types:** Playback events, UI interaction events, A/B test events, device telemetry

## Search Infrastructure

* **Search Engine:** Elasticsearch (used for internal search tooling and some content discovery features)
* **Search Use Cases:** Title search, internal content catalog search, support tooling

## Storage Systems

* **Object Storage:** Amazon S3 (master copies of encoded video files, assets)
* **File Storage:** S3 for content storage and archival
* **CDN:** Open Connect — Netflix's own purpose-built CDN with appliances placed inside ISP networks worldwide

## Real-Time Systems

* **Real-Time Components:** Real-time telemetry pipeline (Keystone), adaptive streaming bitrate decisions
* **Protocols:** HTTPS/HTTP2 for API calls; adaptive streaming over HTTP (DASH/HLS-like proprietary client logic)
* **Streaming Technologies:** Kafka + Flink for real-time analytics; custom adaptive bitrate algorithms on the client

## Authentication & Authorization

* **Authentication Method:** Username/password with OAuth2-style token issuance; supports SSO for some partner integrations
* **Session Management:** Token-based sessions with device-level session tracking
* **Security Features:** Device authorization limits, account-sharing detection systems, DRM (Widevine, FairPlay, PlayReady) for content protection

## Scalability Strategy

* **Horizontal Scaling:** Auto-scaled stateless microservices on AWS EC2/containers
* **Load Balancing:** AWS ELB plus Netflix's own Zuul gateway for intelligent routing
* **Auto Scaling:** AWS Auto Scaling Groups, predictive scaling based on traffic patterns
* **Geographic Distribution:** Multi-region AWS deployment; Open Connect CDN nodes distributed globally inside ISPs

## Reliability & Fault Tolerance

* **Redundancy:** Multi-region active-active architecture for core services
* **Failover Strategy:** Hystrix circuit breakers (historically), regional failover automation
* **Disaster Recovery:** Chaos Engineering practices (Chaos Monkey, Chaos Kong) to proactively test region/AZ failure resilience

## Monitoring & Observability

* **Logging:** Centralized logging via internal tooling built on top of AWS and open-source stacks
* **Monitoring:** Atlas (Netflix's in-house time-series monitoring platform)
* **Tracing:** Distributed tracing tooling built internally; some open-source tracing adoption
* **Alerting:** Integrated with Atlas and on-call tooling for automated incident response

## Security Measures

* **Encryption:** TLS for data in transit; encryption at rest for sensitive data in S3/databases
* **Access Control:** Fine-grained IAM roles on AWS, internal service-to-service authentication
* **Threat Protection:** DRM and content protection, anti-piracy watermarking, account security monitoring

## Engineering Challenges

* Delivering extremely high-bitrate video reliably to hundreds of millions of devices globally
* Operating a massive microservices fleet reliably on top of third-party cloud infrastructure
* Personalization at scale (recommendations) without compromising latency
* Encoding pipeline scaling for huge content catalogs across many device/format permutations

## Known Technologies Used

Java, Spring Boot, React.js, Cassandra, MySQL/Aurora, EVCache, Memcached, Kafka, Apache Flink, Spark, Zuul, Eureka, Hystrix, Atlas, Open Connect, AWS (EC2, S3, RDS), Chaos Monkey/Simian Army, Python (for data science/recommendations)

## Architecture Patterns

* **Microservices**
* **Event-Driven Architecture** (Keystone real-time pipeline)
* **CQRS** in some recommendation/viewing-history services
* **Circuit Breaker pattern** (Hystrix)
* **Other Patterns:** API Gateway, Bulkhead isolation, Chaos Engineering as an operational pattern

## System Design Lessons

Netflix is the canonical example of building extreme resilience into a microservices architecture deployed on a cloud provider you don't fully control — through proactive failure injection (Chaos Monkey), circuit breakers, and graceful degradation. It also shows the value of building dedicated infrastructure (Open Connect CDN) when generic CDNs cannot meet a company's specific scale and cost requirements for content delivery.

END OF DOCUMENT
