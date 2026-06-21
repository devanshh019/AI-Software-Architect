# Swiggy

## Overview

* **Domain:** Food Delivery, Quick Commerce (Instamart)
* **Product Type:** On-demand food and grocery delivery marketplace
* **Estimated Scale:** Operations across 500+ Indian cities (figures vary over time); millions of orders processed; large fleet of delivery partners (exact current figures not consistently publicly disclosed)
* **Primary Users:** Consumers, restaurant partners, delivery partners ("Swiggy executives"), Instamart grocery partners

## Core Features

* Restaurant discovery and food ordering
* Real-time order tracking
* Delivery partner dispatch and route optimization
* Instamart quick-commerce grocery delivery
* Payments and wallet integration
* Ratings and reviews for restaurants/delivery experience

## High-Level Architecture

Swiggy operates a microservices-based architecture deployed primarily on AWS, supporting its core food delivery marketplace as well as Instamart (its quick-commerce vertical). The platform is built around real-time logistics: matching orders to restaurants, optimizing delivery partner allocation, and tracking deliveries geospatially in real time. Swiggy's engineering blog has publicly described use of event-driven systems and geospatial indexing for dispatch optimization, along with a strong investment in data platforms for demand forecasting and dynamic delivery-time estimation.

## Frontend

* **Technologies:** React/React Native for some client surfaces, native mobile development for core apps
* **Client Applications:** Consumer app (iOS/Android), Delivery partner app, Restaurant partner app, Instamart app
* **Rendering Strategy:** Native rendering for core consumer/delivery apps; hybrid approaches for some secondary surfaces
* **Mobile Stack:** Native Android (Kotlin/Java) and iOS (Swift/Objective-C)

## Backend

* **Architecture Style:** Microservices
* **Main Services:** Order management service, Restaurant catalog service, Dispatch/logistics service, Delivery partner allocation service, Payments service, Instamart inventory service
* **Service Communication:** REST/HTTP and gRPC-based internal communication; asynchronous messaging via Kafka
* **API Type:** REST primarily; gRPC for some performance-sensitive internal services

## Databases

* **Primary Database:** MySQL (core transactional data: orders, users, restaurants)
* **Secondary Databases:** Redis, Elasticsearch, Cassandra/NoSQL stores for high-write logistics/location data (specific current vendor choices not fully publicly detailed)
* **Data Model:** Relational for orders/catalog; key-value/geospatial models for real-time location tracking
* **Replication Strategy:** Master-replica replication for MySQL with read replicas for scaling reads
* **Sharding Strategy:** Application-level sharding by region/city for order and logistics data

## Caching Layer

* **Cache Technology:** Redis
* **Cache Usage:** Caching restaurant menus, delivery partner availability, frequently accessed catalog data
* **Eviction Strategy:** TTL-based eviction for menu/catalog caches

## Message Queue / Event Streaming

* **Technology:** Apache Kafka
* **Purpose:** Event-driven order lifecycle management, real-time dispatch updates
* **Event Types:** Order placed/accepted/prepared/picked-up/delivered events, delivery partner location update events

## Search Infrastructure

* **Search Engine:** Elasticsearch
* **Search Use Cases:** Restaurant search, dish/menu search, Instamart product search

## Storage Systems

* **Object Storage:** AWS S3 (menu images, restaurant photos, documents)
* **File Storage:** S3-backed storage for media assets
* **CDN:** Third-party CDN for image/static asset delivery

## Real-Time Systems

* **Real-Time Components:** Real-time order tracking, real-time delivery partner location updates, real-time dispatch/matching engine
* **Protocols:** WebSockets/MQTT-class protocols for location updates and live tracking
* **Streaming Technologies:** Kafka-based pipelines feeding real-time dispatch algorithms and ETA calculations

## Authentication & Authorization

* **Authentication Method:** Phone number/OTP-based authentication, social login options
* **Session Management:** Token-based session management
* **Security Features:** OTP verification for orders/delivery, fraud detection for payment and promo abuse

## Scalability Strategy

* **Horizontal Scaling:** Stateless microservices scaled horizontally on AWS
* **Load Balancing:** AWS ELB/ALB and internal load balancers
* **Auto Scaling:** AWS Auto Scaling Groups, container orchestration (Kubernetes-based deployments)
* **Geographic Distribution:** City/region-based partitioning of services and data to handle India's geographically diverse demand patterns

## Reliability & Fault Tolerance

* **Redundancy:** Multi-AZ deployments on AWS for critical services
* **Failover Strategy:** Automated failover for databases and stateless service instances
* **Disaster Recovery:** Backup and recovery strategies leveraging AWS-native tooling

## Monitoring & Observability

* **Logging:** Centralized logging infrastructure (internal tooling on open-source stacks)
* **Monitoring:** Internal dashboards and metrics pipelines for order/delivery SLAs
* **Tracing:** Distributed tracing for service call chains (specific tooling not fully publicly detailed)
* **Alerting:** Integrated alerting for SLA breaches (e.g., delivery time anomalies)

## Security Measures

* **Encryption:** TLS in transit; encryption at rest for sensitive customer/payment data
* **Access Control:** Role-based access control internally; OAuth/token scopes for partner APIs
* **Threat Protection:** Fraud detection systems for promo abuse, fake orders, and account takeover

## Engineering Challenges

* Real-time delivery partner allocation and route optimization across highly variable urban traffic conditions
* Scaling Instamart's quick-commerce model, which demands much tighter delivery-time SLAs (10-30 minutes) than traditional food delivery
* Demand forecasting for restaurant/dark-store inventory and delivery partner supply planning
* Handling extreme demand spikes during events (e.g., cricket matches, festivals)

## Known Technologies Used

Java, Kotlin, Python, MySQL, Redis, Elasticsearch, Apache Kafka, AWS (EC2, S3, RDS), Kubernetes, React Native (selectively)

## Architecture Patterns

* **Microservices**
* **Event-Driven Architecture** for order lifecycle and dispatch
* **CQRS-like patterns** in logistics services separating write-heavy location updates from read-heavy tracking views
* **Other Patterns:** Geospatial indexing/dispatch optimization patterns, API Gateway

## System Design Lessons

Swiggy demonstrates the architectural demands of real-time, geography-constrained logistics marketplaces, where the hardest engineering problems are less about raw data volume and more about low-latency dispatch optimization and tight SLA guarantees (especially for quick commerce). It also illustrates how a single platform can support multiple distinct marketplace verticals (food delivery and quick commerce) by sharing core logistics/dispatch infrastructure while specializing services per vertical.

END OF DOCUMENT
