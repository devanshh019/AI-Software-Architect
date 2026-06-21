# Zomato

## Overview

* **Domain:** Food Delivery, Restaurant Discovery, Quick Commerce (Blinkit, under Zomato/Eternal group)
* **Product Type:** Restaurant discovery and food delivery marketplace
* **Estimated Scale:** Operations across hundreds of Indian cities and several international markets historically; millions of monthly active users (exact current figures vary by disclosure period)
* **Primary Users:** Consumers, restaurant partners, delivery partners

## Core Features

* Restaurant discovery, reviews, and ratings
* Online food ordering and delivery
* Table reservations (Zomato dining)
* Real-time order tracking
* Delivery partner dispatch and logistics
* Zomato Gold/membership programs

## High-Level Architecture

Zomato's platform is built as a microservices architecture, having evolved from an earlier monolithic restaurant-discovery website into a full-stack food delivery marketplace requiring real-time logistics capabilities. The system separates the restaurant discovery/content side (reviews, menus, search) from the transactional order/delivery side, with dedicated services for dispatch and logistics optimization. Zomato has publicly discussed (via its engineering blog) using event-driven architectures and geospatial systems for delivery partner allocation, similar in spirit to other on-demand delivery platforms.

## Frontend

* **Technologies:** Native mobile apps; React for some web surfaces
* **Client Applications:** Consumer app (iOS/Android), Delivery partner app, Restaurant partner dashboard, Zomato web
* **Rendering Strategy:** Native rendering for mobile apps; server-rendered pages for SEO-critical restaurant listing/discovery pages on web
* **Mobile Stack:** Native Android (Kotlin/Java) and iOS (Swift/Objective-C)

## Backend

* **Architecture Style:** Microservices
* **Main Services:** Restaurant catalog/discovery service, Order management service, Dispatch/logistics service, Payments service, Reviews/ratings service
* **Service Communication:** REST/HTTP-based service communication; asynchronous messaging via message queues for order events
* **API Type:** REST primarily for internal and partner-facing APIs

## Databases

* **Primary Database:** MySQL (core transactional data)
* **Secondary Databases:** Elasticsearch (search/discovery), Redis (caching), NoSQL stores for high-throughput logistics data
* **Data Model:** Relational for restaurant/order/user data; document/key-value for catalog search indexing
* **Replication Strategy:** Master-replica MySQL replication with read replicas
* **Sharding Strategy:** Region/city-based sharding for order and logistics-related data

## Caching Layer

* **Cache Technology:** Redis
* **Cache Usage:** Caching restaurant menus, search results, frequently viewed catalog pages
* **Eviction Strategy:** TTL-based eviction tuned for catalog freshness requirements

## Message Queue / Event Streaming

* **Technology:** Apache Kafka (commonly used in this class of delivery platforms; specific internal usage details are not exhaustively publicly documented)
* **Purpose:** Order lifecycle event propagation, real-time dispatch coordination
* **Event Types:** Order placed/confirmed/dispatched/delivered events, delivery partner location events

## Search Infrastructure

* **Search Engine:** Elasticsearch
* **Search Use Cases:** Restaurant search, dish-level search, location-based discovery and filtering

## Storage Systems

* **Object Storage:** Cloud object storage (e.g., AWS S3-class) for restaurant/menu images
* **File Storage:** Cloud-backed blob storage for media assets
* **CDN:** Third-party CDN for image and static asset delivery

## Real-Time Systems

* **Real-Time Components:** Real-time order tracking, real-time delivery partner location updates
* **Protocols:** WebSockets/long-polling for live order tracking
* **Streaming Technologies:** Event-driven pipelines feeding dispatch and ETA computation systems

## Authentication & Authorization

* **Authentication Method:** Phone number/OTP-based login, social login options
* **Session Management:** Token-based session management
* **Security Features:** OTP verification, fraud detection for fake orders/promo abuse

## Scalability Strategy

* **Horizontal Scaling:** Stateless services scaled horizontally behind load balancers
* **Load Balancing:** Cloud-native load balancers (AWS ELB/ALB-class infrastructure)
* **Auto Scaling:** Cloud-based auto-scaling groups/container orchestration
* **Geographic Distribution:** City/region-based service and data partitioning to match India's and other markets' geographic demand patterns

## Reliability & Fault Tolerance

* **Redundancy:** Multi-AZ deployment for critical services
* **Failover Strategy:** Automated database and service failover
* **Disaster Recovery:** Cloud-native backup and recovery tooling

## Monitoring & Observability

* **Logging:** Centralized logging infrastructure (specific stack not exhaustively publicly disclosed)
* **Monitoring:** Internal dashboards tracking order/delivery SLAs and system health
* **Tracing:** Distributed tracing for service-to-service calls (specific tooling not fully publicly detailed)
* **Alerting:** SLA-based alerting for delivery delays and system anomalies

## Security Measures

* **Encryption:** TLS in transit; encryption at rest for sensitive customer and payment data
* **Access Control:** Role-based access control internally; scoped API access for restaurant/delivery partners
* **Threat Protection:** Fraud detection for promo abuse and fake reviews/orders

## Engineering Challenges

* Balancing the content-heavy discovery side (reviews, photos, ratings) with the transactional, latency-sensitive delivery side of the platform
* Real-time delivery partner allocation and route optimization across diverse urban geographies
* Maintaining data freshness for restaurant menus/pricing across a large, frequently changing partner base
* Scaling internationally while adapting to local market dynamics (Zomato has entered and exited various international markets over its history)

## Known Technologies Used

Java, Kotlin, Python, MySQL, Elasticsearch, Redis, Apache Kafka (industry-standard for this class of platform), AWS/Cloud infrastructure, React (web)

## Architecture Patterns

* **Microservices**
* **Event-Driven Architecture** for order and dispatch workflows
* **Other Patterns:** API Gateway, geospatial dispatch optimization patterns, caching-aside pattern for catalog data

## System Design Lessons

Zomato illustrates the architectural split that's common among discovery-plus-delivery platforms: a content/discovery subsystem optimized for read-heavy, SEO-sensitive workloads, paired with a transactional logistics subsystem optimized for low-latency, write-heavy real-time coordination. It also reflects the broader industry lesson that on-demand delivery platforms need geospatial and dispatch-specific infrastructure that general-purpose e-commerce architectures don't typically require.

END OF DOCUMENT
