# Uber

## Overview

* **Domain:** Ride-hailing, Mobility, Delivery (Uber Eats), Freight
* **Product Type:** Two-sided real-time marketplace (riders/drivers, eaters/restaurants/couriers)
* **Estimated Scale:** 130M+ monthly active platform consumers; operations in 70+ countries; millions of trips per day (exact current figures not publicly disclosed in real time)
* **Primary Users:** Riders, Drivers/Couriers, Restaurants/Merchants, Freight shippers/carriers

## Core Features

* Real-time ride matching and dispatch
* Live GPS tracking and ETA computation
* Dynamic/surge pricing
* Payments and fare splitting
* Uber Eats food ordering and delivery dispatch
* Driver/rider rating and trust & safety systems
* Maps, routing, and geofencing
* Multi-product marketplace (UberX, Eats, Freight)

## High-Level Architecture

Uber evolved from a Python/Node.js monolith ("the Monolith") into one of the largest known microservices architectures in the industry, with thousands of independently deployed services. The architecture is organized around domain-oriented service boundaries (Marketplace, Maps, Payments, Eats, Trust & Safety), backed by a common platform layer (service mesh, schemaless storage, stream processing) that all domain teams build on. Uber's "Domain-Oriented Microservice Architecture" (DOMA) groups related microservices into domains and layers (gateway, business logic, infrastructure) to manage the complexity of thousands of services.

## Frontend

* **Technologies:** React, TypeScript, Uber's own design system (Base Web)
* **Client Applications:** Rider app, Driver app, Eats app, Uber Freight app, web dashboards
* **Rendering Strategy:** Server-driven UI for some flows, client-rendered SPA for web dashboards
* **Mobile Stack:** Native iOS (Swift/Objective-C) and Android (Kotlin/Java); cross-platform tooling explored but core apps remain largely native for performance

## Backend

* **Architecture Style:** Microservices (thousands of services), originally a monolith later decomposed
* **Main Services:** Dispatch (matching), Pricing, Maps/Routing, Payments, Trust & Safety, Eats Marketplace, Driver Incentives, Notifications
* **Service Communication:** gRPC and Thrift-based RPC over internal service mesh; async messaging via Kafka
* **API Type:** gRPC internally; REST/GraphQL at edge for mobile clients

## Databases

* **Primary Database:** Schemaless (Uber's custom datastore built on top of sharded MySQL)
* **Secondary Databases:** Cassandra, Redis, MySQL (Schemaless layer), Docstore (newer distributed DB)
* **Data Model:** Key-value with append-only schema-less columns (Schemaless); document-style for Docstore
* **Replication Strategy:** Multi-datacenter replication, async cross-region replication for resiliency
* **Sharding Strategy:** Application-level sharding via Schemaless's shard map; entity-based partitioning by city/region for some services

## Caching Layer

* **Cache Technology:** Redis, in-house caching layers
* **Cache Usage:** Hot trip state, driver location caches, pricing surge caches
* **Eviction Strategy:** TTL-based eviction; LRU for hot key caches (exact internal policies not publicly detailed)

## Message Queue / Event Streaming

* **Technology:** Apache Kafka (heavily used and extended internally)
* **Purpose:** Event-driven communication between microservices, analytics pipelines, real-time location ingestion
* **Event Types:** Trip lifecycle events, location pings, payment events, driver state changes

## Search Infrastructure

* **Search Engine:** Elasticsearch (used for internal tooling, restaurant/menu search in Eats)
* **Search Use Cases:** Restaurant and menu search, internal log search, support ticket search

## Storage Systems

* **Object Storage:** Internal blob storage built on top of cloud infra (details largely proprietary); also uses cloud object storage for some workloads
* **File Storage:** HDFS for big data/analytics (Uber's data lake)
* **CDN:** Third-party CDNs for static assets and Eats imagery (specific vendor not always disclosed)

## Real-Time Systems

* **Real-Time Components:** Live driver location streaming, real-time matching engine, real-time ETA computation
* **Protocols:** WebSockets/long-polling for client updates, gRPC streaming internally
* **Streaming Technologies:** Kafka, Apache Flink (for real-time stream processing, e.g., surge pricing, fraud detection)

## Authentication & Authorization

* **Authentication Method:** OAuth2-based authentication, phone number/OTP verification for riders/drivers
* **Session Management:** Token-based sessions with short-lived access tokens and refresh tokens
* **Security Features:** Device fingerprinting, anomaly detection for account takeover, trip verification (PIN/photo)

## Scalability Strategy

* **Horizontal Scaling:** Stateless microservices scaled horizontally behind load balancers
* **Load Balancing:** Internal L4/L7 load balancing (Uber uses HAProxy/Envoy-style proxies in its mesh)
* **Auto Scaling:** Container orchestration (Uber uses Mesos historically, migrating toward Kubernetes-based infra)
* **Geographic Distribution:** Multi-region/multi-datacenter deployment with city/region-based data partitioning

## Reliability & Fault Tolerance

* **Redundancy:** Multi-region active-active deployments for critical services
* **Failover Strategy:** Circuit breakers, automated failover for datastore replicas
* **Disaster Recovery:** Cross-region backups and replicated datastores

## Monitoring & Observability

* **Logging:** Centralized logging pipeline (internal tooling built atop open-source components)
* **Monitoring:** M3 (Uber's open-sourced metrics platform, M3DB) for time-series metrics at scale
* **Tracing:** Jaeger (originally created/open-sourced by Uber) for distributed tracing
* **Alerting:** Internal alerting integrated with M3/Jaeger pipelines

## Security Measures

* **Encryption:** TLS in transit; encryption at rest for sensitive datastores
* **Access Control:** Role-based access control internally; service-to-service auth via mTLS in mesh
* **Threat Protection:** Fraud detection ML models, anomaly detection for trip and payment fraud

## Engineering Challenges

* Real-time global-scale geospatial matching at extremely low latency
* Managing thousands of interdependent microservices and avoiding "microservices sprawl"
* Maintaining consistency for trip state across distributed services during failures
* Surge pricing computation requiring near real-time aggregation across regions

## Known Technologies Used

Go, Java, Python, Node.js, React, Kotlin, Swift, Schemaless, MySQL, Cassandra, Redis, Docstore, Kafka, Apache Flink, M3/M3DB, Jaeger, Mesos, Elasticsearch, HDFS, gRPC, Thrift, H3 (Uber's hexagonal geospatial indexing system), Piper/internal CI tooling

## Architecture Patterns

* **Microservices** (domain-oriented, DOMA)
* **Event-Driven Architecture** for cross-service communication via Kafka
* **CQRS-like patterns** in some read-heavy services (e.g., trip history reads vs. writes)
* **Saga-like compensating transactions** for distributed trip/payment workflows
* **Other Patterns:** Circuit breaker, API gateway, service mesh

## System Design Lessons

Uber demonstrates how a monolith can be decomposed into a large microservices ecosystem, but also illustrates the operational complexity that comes with that scale — leading Uber to invest heavily in platform tooling (service mesh, domain-oriented grouping, open-sourced observability tools like Jaeger and M3) specifically to tame the complexity it created. It also shows the value of building custom geospatial indexing (H3) and custom storage layers (Schemaless) when off-the-shelf solutions don't meet extreme-scale, low-latency geospatial and write-heavy requirements.

END OF DOCUMENT
