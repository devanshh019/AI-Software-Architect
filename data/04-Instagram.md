# Instagram

## Overview

* **Domain:** Social Media, Photo/Video Sharing
* **Product Type:** Social networking and content-sharing platform (owned by Meta)
* **Estimated Scale:** 2B+ monthly active users (publicly reported by Meta in various disclosures)
* **Primary Users:** Consumers sharing photos/videos/stories/reels; creators; businesses/advertisers

## Core Features

* Photo and video posting (feed, Stories, Reels)
* Direct messaging
* Explore/discovery feed with ML-driven recommendations
* Live video streaming
* Shopping/commerce features
* Likes, comments, follows, notifications

## High-Level Architecture

Instagram famously started as a small Python/Django monolith on top of PostgreSQL and grew to massive scale before and after the Facebook (Meta) acquisition. It is one of the most widely cited examples of scaling a Django application: sharding PostgreSQL across thousands of logical shards, heavy use of caching (Memcached/Redis), and migrating feed-serving infrastructure to Cassandra for write-heavy timeline data. Post-acquisition, Instagram increasingly leveraged Meta's shared infrastructure (TAO graph store, Haystack-derived photo storage, Meta's data centers and networking), while much of the original application logic remained Python/Django-based for a long time, with performance-critical paths optimized or rewritten over time.

## Frontend

* **Technologies:** React (web), Meta's internal frontend frameworks
* **Client Applications:** iOS app, Android app, Instagram web, Instagram Lite (for low-bandwidth regions)
* **Rendering Strategy:** Native rendering on mobile; client-side rendered SPA on web
* **Mobile Stack:** Native iOS (Objective-C/Swift) and Android (Java/Kotlin); some shared infrastructure with Meta's broader mobile platform tooling

## Backend

* **Architecture Style:** Originally a Python/Django monolith; evolved into a service-oriented architecture leveraging Meta's shared backend services
* **Main Services:** Feed generation service, Stories/Reels service, Direct messaging service, Notification service, Recommendation/ranking service
* **Service Communication:** Thrift/internal RPC frameworks (shared with Meta infra), HTTP internally for some services
* **API Type:** REST and GraphQL (Instagram historically used a private API consumed by official apps; GraphQL used in some Meta-wide infra)

## Databases

* **Primary Database:** PostgreSQL (heavily sharded; original core datastore)
* **Secondary Databases:** Cassandra (for feed/timeline and high-write data), TAO (Meta's distributed graph datastore for the social graph), Memcached, Redis
* **Data Model:** Relational (Postgres) for core entities; graph-like model via TAO for relationships (follows, likes); wide-column (Cassandra) for activity feeds
* **Replication Strategy:** Master-replica replication for PostgreSQL shards; multi-region replication for TAO/Cassandra-backed data
* **Sharding Strategy:** Logical sharding of PostgreSQL into thousands of shards distributed across physical servers, using a custom ID-generation scheme embedding shard information

## Caching Layer

* **Cache Technology:** Memcached, Redis
* **Cache Usage:** Caching feed data, user session data, frequently accessed media metadata
* **Eviction Strategy:** LRU-based eviction in Memcached pools; TTL-based expiry for time-sensitive data (e.g., Stories)

## Message Queue / Event Streaming

* **Technology:** Apache Kafka (used broadly across Meta infra), Meta's internal queueing systems
* **Purpose:** Asynchronous processing for notifications, feed fan-out, analytics events
* **Event Types:** Post creation events, like/comment events, follow events, Story view events

## Search Infrastructure

* **Search Engine:** Elasticsearch-class systems and Meta's internal search infra (Unicorn, used across Meta products) for user/hashtag/content search
* **Search Use Cases:** User search, hashtag search, Explore content discovery

## Storage Systems

* **Object Storage:** Haystack-derived photo/video storage system (shared lineage with Facebook's photo storage)
* **File Storage:** Meta's internal blob storage infrastructure
* **CDN:** Meta's global CDN/edge network for media delivery

## Real-Time Systems

* **Real-Time Components:** Live video streaming, real-time direct messaging, real-time notifications
* **Protocols:** WebSockets/MQTT-derived protocols for messaging (shared lineage with Facebook Messenger infra), RTMP/HLS-class protocols for live video
* **Streaming Technologies:** Meta's internal real-time infra for chat delivery; video transcoding pipelines for Reels/Stories

## Authentication & Authorization

* **Authentication Method:** Username/password, OAuth-based login, integration with Meta account system (Facebook login linkage)
* **Session Management:** Token-based sessions, device-bound session tracking
* **Security Features:** Two-factor authentication, login anomaly detection, account recovery flows

## Scalability Strategy

* **Horizontal Scaling:** Heavy sharding of PostgreSQL; stateless application servers scaled horizontally
* **Load Balancing:** Meta's internal L4/L7 load balancing infrastructure
* **Auto Scaling:** Capacity managed via Meta's internal fleet management and data center provisioning systems
* **Geographic Distribution:** Globally distributed via Meta's data centers and edge points of presence

## Reliability & Fault Tolerance

* **Redundancy:** Multi-region replication of core graph and content data
* **Failover Strategy:** Automated failover within Meta's shared infrastructure platform
* **Disaster Recovery:** Cross-data-center replication and backup strategies shared with Meta-wide DR practices

## Monitoring & Observability

* **Logging:** Meta's internal centralized logging infrastructure (Scribe-derived systems)
* **Monitoring:** Meta's internal monitoring stack (ODS-class systems)
* **Tracing:** Internal distributed tracing tooling shared across Meta products
* **Alerting:** Integrated with Meta's internal incident response tooling

## Security Measures

* **Encryption:** TLS in transit; encryption at rest for sensitive data
* **Access Control:** Role-based access internally; OAuth scopes for third-party API access
* **Threat Protection:** Spam/fake account detection, content moderation ML pipelines, rate limiting

## Engineering Challenges

* Scaling a Python/Django monolith's PostgreSQL layer to handle billions of users without a full rewrite
* Building a low-latency global feed ranking system personalized per user
* Handling massive media storage and delivery (photos/videos/Reels) cost-effectively at scale
* Integrating with Meta's shared infrastructure while preserving Instagram-specific product velocity

## Known Technologies Used

Python, Django, PostgreSQL, Cassandra, TAO, Memcached, Redis, Kafka, Thrift, Haystack (photo storage), React, Java/Kotlin (Android), Objective-C/Swift (iOS), Unicorn (search infra)

## Architecture Patterns

* **Monolith-to-Service-Oriented evolution**
* **Event-Driven Architecture** for notifications and feed fan-out
* **Sharding pattern** (custom logical sharding for PostgreSQL)
* **Graph data model pattern** via TAO for social relationships
* **Other Patterns:** Fan-out-on-write/fan-out-on-read hybrid for feed generation, caching-aside pattern

## System Design Lessons

Instagram's growth story is a strong case study in scaling a relational database (PostgreSQL) through careful application-level sharding rather than prematurely adopting NoSQL, while later complementing it with purpose-built systems (Cassandra, TAO) for specific access patterns like feeds and the social graph. It also illustrates how acquired companies can benefit enormously from integrating into a parent company's shared infrastructure (Meta's TAO, Haystack, CDN) rather than rebuilding equivalent systems from scratch.

END OF DOCUMENT
