# Airbnb

## Overview

* **Domain:** Travel, Hospitality, Marketplace
* **Product Type:** Two-sided marketplace connecting hosts and guests for short-term lodging/experiences
* **Estimated Scale:** 5M+ hosts, 1.5B+ guest arrivals cumulatively reported by Airbnb over its history; operations in 220+ countries/regions
* **Primary Users:** Guests (travelers) and Hosts (property owners/experience providers)

## Core Features

* Listing search and discovery with filters/maps
* Booking and reservation management
* Messaging between hosts and guests
* Payments and payout processing
* Reviews and ratings
* Experiences and dynamic pricing tools for hosts

## High-Level Architecture

Airbnb began as a Ruby on Rails monolith and, as the company scaled, gradually decomposed parts of the system into services (Service-Oriented Architecture) while keeping a large, well-maintained core Rails monolith for much of its history rather than fully fragmenting into thousands of microservices. Airbnb is also notable for open-sourcing Apache Airflow, the workflow orchestration tool it built internally to manage complex data pipelines (ETL jobs, ML feature pipelines). The architecture combines this monolith-plus-services approach with extensive use of data infrastructure (Airflow, Druid, Spark) to power search ranking, pricing, and fraud detection.

## Frontend

* **Technologies:** React, React Native (Airbnb invested heavily in React Native before later scaling back parts of that strategy), Airbnb's internal design system (DLS - Design Language System)
* **Client Applications:** Web app, iOS app, Android app, Host app
* **Rendering Strategy:** Server-side rendering for SEO-critical listing/search pages; client-side rendering for interactive booking flows
* **Mobile Stack:** Native iOS (Swift) and Android (Kotlin), with React Native used selectively for shared feature development historically

## Backend

* **Architecture Style:** Originally a Ruby on Rails monolith, evolved into Service-Oriented Architecture with key domains extracted into services
* **Main Services:** Search/Discovery service, Pricing service (Smart Pricing), Booking/Reservation service, Messaging service, Payments service, Trust & Safety/fraud detection service
* **Service Communication:** Thrift-based RPC for service-to-service calls; REST/HTTP for some internal and external APIs
* **API Type:** REST (GraphQL adoption in some newer client-facing surfaces)

## Databases

* **Primary Database:** MySQL (core transactional data for bookings, listings, users)
* **Secondary Databases:** Apache Druid (real-time analytics), Elasticsearch (search), Redis (caching/session)
* **Data Model:** Relational (MySQL) for core booking/listing/user data; columnar/OLAP (Druid) for analytics dashboards
* **Replication Strategy:** Master-replica MySQL replication; multi-region read replicas for performance
* **Sharding Strategy:** Application-level/horizontal sharding of MySQL by entity (e.g., listings, users) as scale increased

## Caching Layer

* **Cache Technology:** Redis, Memcached
* **Cache Usage:** Caching search results, listing details, pricing calculations
* **Eviction Strategy:** TTL-based eviction tuned per cache use case

## Message Queue / Event Streaming

* **Technology:** Apache Kafka
* **Purpose:** Event pipeline for booking events, pricing updates, search indexing updates
* **Event Types:** Booking created/cancelled events, listing update events, message sent events, pricing recalculation events

## Search Infrastructure

* **Search Engine:** Elasticsearch
* **Search Use Cases:** Listing search with geospatial filters, ranking by relevance/personalization, Experiences search

## Storage Systems

* **Object Storage:** Amazon S3 (listing photos, documents)
* **File Storage:** S3-backed storage for media assets
* **CDN:** Third-party CDN for static assets and listing images (specific vendor not always publicly disclosed; historically used Akamai/Fastly-class providers)

## Real-Time Systems

* **Real-Time Components:** Real-time messaging between hosts and guests, real-time availability/calendar updates, real-time pricing adjustments
* **Protocols:** WebSockets for messaging; HTTPS for most API interactions
* **Streaming Technologies:** Kafka-based event streaming feeding into Spark/Druid for near-real-time analytics

## Authentication & Authorization

* **Authentication Method:** Username/password, OAuth-based social login (Google/Facebook/Apple), phone verification
* **Session Management:** Token/cookie-based session management
* **Security Features:** Identity verification (ID checks for hosts/guests), two-factor authentication, fraud detection ML models

## Scalability Strategy

* **Horizontal Scaling:** Horizontally scaled service tier behind load balancers; sharded MySQL for data tier
* **Load Balancing:** AWS ELB and internal load balancing layers
* **Auto Scaling:** AWS Auto Scaling Groups for compute workloads
* **Geographic Distribution:** Multi-region deployment on AWS, with localized data handling for regional compliance

## Reliability & Fault Tolerance

* **Redundancy:** Multi-AZ deployments for critical services and databases
* **Failover Strategy:** Automated failover for database replicas; circuit breakers in service layer
* **Disaster Recovery:** Cross-region backups and recovery runbooks

## Monitoring & Observability

* **Logging:** Centralized logging via internal tooling built on open-source components
* **Monitoring:** Internal dashboards integrated with Druid for real-time business metrics
* **Tracing:** Distributed tracing tooling for service-to-service calls
* **Alerting:** PagerDuty-class incident alerting integrated with monitoring stack

## Security Measures

* **Encryption:** TLS in transit; encryption at rest for sensitive data (payment info, ID documents)
* **Access Control:** Role-based access control internally; OAuth scopes for API access
* **Threat Protection:** ML-based fraud detection (for fake listings, payment fraud), account takeover protection

## Engineering Challenges

* Balancing monolith maintainability against the need to scale specific high-traffic domains (search, pricing) independently
* Building dynamic pricing algorithms (Smart Pricing) that account for seasonality, local events, and demand signals
* Geospatial search at scale with relevance ranking and personalization
* Trust & safety at scale across a marketplace with real-world physical stakes (verifying hosts/guests, preventing fraud)

## Known Technologies Used

Ruby on Rails, Java, React, React Native, MySQL, Apache Druid, Elasticsearch, Redis, Apache Kafka, Apache Airflow (created by Airbnb), Apache Spark, Thrift, AWS (EC2, S3, RDS)

## Architecture Patterns

* **Monolith with extracted services (SOA)**
* **Event-Driven Architecture** for booking/pricing pipelines
* **CQRS-like separation** between transactional (MySQL) and analytical (Druid) data paths
* **Other Patterns:** API Gateway, workflow orchestration pattern (Airflow DAGs) for data pipelines

## System Design Lessons

Airbnb shows that a well-maintained monolith can remain viable far longer than conventional wisdom suggests, with targeted extraction of high-traffic domains (search, pricing) into separate services rather than a wholesale microservices rewrite. Its creation and open-sourcing of Apache Airflow also demonstrates how solving an internal data-orchestration problem can produce a tool with broad industry impact beyond the company's own use case.

END OF DOCUMENT
