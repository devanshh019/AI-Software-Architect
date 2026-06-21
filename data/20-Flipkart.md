# Flipkart

## Overview

* **Domain:** E-Commerce, Retail
* **Product Type:** Online marketplace and retail platform (majority owned by Walmart)
* **Estimated Scale:** Hundreds of millions of registered users in India; one of India's largest e-commerce platforms, especially during major sale events (Big Billion Days)
* **Primary Users:** Consumers, third-party sellers/marketplace partners, logistics partners (Ekart)

## Core Features

* Product catalog browsing and search
* Shopping cart and checkout
* Order management and Ekart logistics/delivery tracking
* Personalized recommendations
* Flipkart Plus/loyalty programs
* Seller dashboard and marketplace tools

## High-Level Architecture

Flipkart is a widely cited case study in the Indian tech industry for its transition from a monolithic architecture to microservices, an effort internally referred to as "Project Voyager"/architecture overhaul work undertaken to handle massive traffic spikes during flash-sale events like Big Billion Days. The platform decomposed its original monolith into independently deployable microservices for catalog, search, cart, checkout, and order management, while building out its own logistics arm (Ekart) with dedicated systems for delivery tracking and route optimization. Flipkart has also publicly discussed building and using internally-developed infrastructure components, including contributions to and use of distributed data systems for high-throughput, high-availability transactional workloads.

## Frontend

* **Technologies:** React (web), native mobile development
* **Client Applications:** Web app, iOS app, Android app, Flipkart Lite (progressive web app for low-bandwidth markets)
* **Rendering Strategy:** Server-side rendering for SEO-critical product/category pages; client-side rendering for interactive cart/checkout flows
* **Mobile Stack:** Native Android (Kotlin/Java) and iOS (Swift/Objective-C); Flipkart has also publicly discussed PWA investment for accessibility on lower-end devices

## Backend

* **Architecture Style:** Microservices (migrated from an original monolithic architecture)
* **Main Services:** Catalog service, Search/discovery service, Cart service, Checkout/order management service, Payments service, Ekart logistics/delivery service, Seller platform service
* **Service Communication:** REST/HTTP and internal RPC frameworks for service-to-service communication; asynchronous messaging via Kafka for decoupled workflows
* **API Type:** REST primarily for internal and partner-facing APIs

## Databases

* **Primary Database:** MySQL (core transactional data: orders, catalog, users)
* **Secondary Databases:** HBase (for high-volume, write-heavy data such as event/clickstream data), Redis, Elasticsearch
* **Data Model:** Relational (MySQL) for core order/catalog data; wide-column (HBase) for large-scale analytics and activity data
* **Replication Strategy:** Master-replica MySQL replication; multi-data-center replication for critical transactional data, especially to handle flash-sale traffic spikes
* **Sharding Strategy:** Application-level sharding of MySQL by entity (e.g., orders, sellers, products) to distribute load across the database tier

## Caching Layer

* **Cache Technology:** Redis
* **Cache Usage:** Caching product catalog data, pricing/inventory data, frequently accessed search results
* **Eviction Strategy:** TTL-based eviction tuned for catalog freshness and flash-sale inventory accuracy

## Message Queue / Event Streaming

* **Technology:** Apache Kafka
* **Purpose:** Event-driven order/inventory workflows, clickstream/analytics event ingestion, logistics tracking updates
* **Event Types:** Order placed/confirmed/shipped/delivered events, inventory update events, price change events, delivery tracking events

## Search Infrastructure

* **Search Engine:** Elasticsearch (and internally developed search ranking/relevance systems layered on top)
* **Search Use Cases:** Product search, category/faceted navigation, search-based advertising

## Storage Systems

* **Object Storage:** Cloud object storage (AWS S3-class or equivalent) for product images and documents
* **File Storage:** Cloud-backed blob storage for catalog media assets
* **CDN:** Third-party CDN for image and static asset delivery, critical for performance during high-traffic sale events

## Real-Time Systems

* **Real-Time Components:** Real-time inventory updates (especially critical during flash sales), real-time order tracking via Ekart, real-time pricing updates
* **Protocols:** WebSockets/long-polling for live order tracking; HTTPS/REST for most client interactions
* **Streaming Technologies:** Kafka-based pipelines feeding real-time inventory and pricing systems, particularly tuned for extreme traffic spikes during sale events

## Authentication & Authorization

* **Authentication Method:** Phone number/OTP-based authentication, email/password, social login options
* **Session Management:** Token-based session management
* **Security Features:** Multi-factor authentication for account changes, fraud detection for payment and promo abuse

## Scalability Strategy

* **Horizontal Scaling:** Horizontally scaled stateless microservices, heavily load-tested and over-provisioned ahead of major sale events
* **Load Balancing:** Cloud-native load balancers and internal traffic management layers
* **Auto Scaling:** Auto-scaling combined with proactive capacity planning for predictable high-traffic events (Big Billion Days)
* **Geographic Distribution:** Multi-region/multi-data-center deployment within India, with CDN edge presence for performance

## Reliability & Fault Tolerance

* **Redundancy:** Multi-data-center redundancy for critical order/payment processing systems
* **Failover Strategy:** Automated failover for databases and service instances; circuit breakers for degraded service modes during extreme load
* **Disaster Recovery:** Backup and recovery strategies tested ahead of major high-traffic sale events

## Monitoring & Observability

* **Logging:** Centralized logging infrastructure (internal tooling on open-source-derived stacks)
* **Monitoring:** Internal dashboards tracking order throughput, inventory accuracy, and system health, especially during flash sales
* **Tracing:** Distributed tracing tooling for service-to-service call chains
* **Alerting:** Real-time alerting tuned for extreme-traffic events to detect bottlenecks quickly

## Security Measures

* **Encryption:** TLS in transit; encryption at rest for sensitive customer and payment data
* **Access Control:** Role-based access control internally; scoped API access for sellers/logistics partners
* **Threat Protection:** Fraud detection for fake orders/returns abuse, bot detection during flash sales, account takeover protection

## Engineering Challenges

* Handling extreme, predictable traffic spikes during flash-sale events (Big Billion Days) that can be orders of magnitude above normal traffic
* Maintaining real-time inventory accuracy across millions of SKUs and sellers during high-demand events to avoid overselling
* Scaling logistics (Ekart) to handle massive delivery volumes with route optimization across diverse Indian geographies
* Migrating a large-scale production monolith to microservices while continuing to operate the business without major downtime

## Known Technologies Used

Java, Python, React, MySQL, HBase, Redis, Elasticsearch, Apache Kafka, AWS/cloud infrastructure, Kotlin/Java (Android), Swift/Objective-C (iOS)

## Architecture Patterns

* **Microservices** (migrated from an original monolith)
* **Event-Driven Architecture** for order/inventory/logistics workflows
* **CQRS-like patterns** separating high-write inventory/event data from read-optimized catalog views
* **Other Patterns:** API Gateway, capacity-planning/load-shedding patterns for flash-sale traffic management

## System Design Lessons

Flipkart's experience with Big Billion Days flash sales is a widely referenced case study in designing e-commerce systems for extreme, predictable traffic spikes — emphasizing proactive load testing, capacity over-provisioning, and graceful degradation strategies rather than relying solely on reactive auto-scaling. Its monolith-to-microservices migration also reflects a common pattern among high-growth e-commerce platforms: decomposing services incrementally around critical domains (catalog, inventory, checkout) under the pressure of real-world scaling events.

END OF DOCUMENT
