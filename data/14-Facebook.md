# Facebook

## Overview

* **Domain:** Social Media, Social Networking
* **Product Type:** Social networking platform (flagship product of Meta)
* **Estimated Scale:** 3B+ monthly active users (publicly reported by Meta across its family of apps; Facebook app itself also in the billions)
* **Primary Users:** Consumers, businesses/advertisers, Groups/community admins

## Core Features

* News Feed with algorithmic ranking
* Friends/social graph and connections
* Groups, Pages, Events
* Messenger (originally integrated, now a related but distinct app)
* Marketplace
* Live video and Stories
* Advertising platform

## High-Level Architecture

Facebook's backend is one of the most architecturally influential systems in the industry, historically built on PHP (later compiled via HipHop/HHVM, Facebook's custom PHP virtual machine and the Hack programming language it created) on top of a heavily customized MySQL/InnoDB storage layer. Facebook created TAO ("The Associations and Objects"), a distributed graph-aware data store built on top of MySQL and Memcached, specifically to serve the social graph (friends, likes, comments) at extremely low latency and massive read scale. Facebook also created and open-sourced major infrastructure projects including Cassandra (originally created at Facebook, later donated to Apache), Presto (distributed SQL query engine), Scribe (log aggregation), Thrift (cross-language RPC framework), and Haystack (its photo storage system designed to minimize per-photo storage/metadata overhead).

## Frontend

* **Technologies:** React (created at Facebook specifically to address Facebook's own UI complexity, later open-sourced), GraphQL (also created at Facebook to solve client-driven data-fetching needs)
* **Client Applications:** Web app (facebook.com), iOS app, Android app, Facebook Lite (for low-bandwidth markets)
* **Rendering Strategy:** Client-side rendering (React) with server-side rendering for performance-critical initial loads; BigPipe (a Facebook-developed pipelining technique to progressively render page sections)
* **Mobile Stack:** Native iOS (Objective-C/Swift) and Android (Java/Kotlin), historically also explored React Native (created at Facebook)

## Backend

* **Architecture Style:** Originally a PHP monolith, evolved into a massive service-oriented architecture built on shared internal infrastructure
* **Main Services:** News Feed ranking service, Social graph service (TAO), Messaging service, Notifications service, Ads serving service, Search (Graph Search) service
* **Service Communication:** Thrift (created at Facebook) for cross-service RPC; internal service mesh and discovery systems
* **API Type:** GraphQL (created at Facebook) for client-facing data fetching; REST/internal RPC for service-to-service communication

## Databases

* **Primary Database:** MySQL/InnoDB (heavily customized and sharded), accessed primarily via TAO's graph abstraction layer rather than directly
* **Secondary Databases:** Cassandra (created at Facebook, used for specific high-write workloads such as Inbox search historically), RocksDB (created at Facebook, an embedded key-value store used as a building block for other systems), Memcached (heavily extended internally)
* **Data Model:** Graph model (via TAO, abstracting objects and associations) layered on top of relational MySQL storage; wide-column (Cassandra) for select use cases
* **Replication Strategy:** Multi-region MySQL replication with TAO's caching/replication layer providing read scalability across data centers
* **Sharding Strategy:** Application-level sharding of MySQL by user ID/object ID, abstracted away from application developers by TAO

## Caching Layer

* **Cache Technology:** Memcached (Facebook was historically one of the largest Memcached deployments in the world and published extensively on scaling it)
* **Cache Usage:** Caching the social graph (via TAO's caching tier), feed ranking results, session data
* **Eviction Strategy:** LRU-based eviction within Memcached pools, with custom invalidation logic (McSqueal/similar systems) to keep caches consistent with MySQL writes

## Message Queue / Event Streaming

* **Technology:** Scribe (created at Facebook for log aggregation), Apache Kafka (adopted more broadly across Meta infra over time)
* **Purpose:** Log/event aggregation pipeline, asynchronous processing for notifications and feed updates
* **Event Types:** Post creation events, like/comment events, friend request events, ad impression events

## Search Infrastructure

* **Search Engine:** Unicorn (Facebook's in-house "social graph search" engine, designed specifically to search graph-structured data rather than plain documents)
* **Search Use Cases:** Graph Search (people/posts/pages search), internal search tooling

## Storage Systems

* **Object Storage:** Haystack (Facebook's custom photo storage system, designed to reduce per-photo metadata overhead compared to general-purpose filesystems)
* **File Storage:** Internal blob storage infrastructure (f4 — Facebook's "warm storage" system for older, less frequently accessed photos/videos)
* **CDN:** Facebook's own global edge/CDN infrastructure for media delivery

## Real-Time Systems

* **Real-Time Components:** Real-time News Feed updates, real-time notifications, live video streaming
* **Protocols:** MQTT-derived protocols for mobile push/messaging (shared lineage with Messenger infra), RTMP/HLS-class protocols for live video
* **Streaming Technologies:** Internal real-time event pipelines for feed ranking signal ingestion

## Authentication & Authorization

* **Authentication Method:** Username/password, OAuth2 (Facebook Login, widely used as a third-party social login provider across the web)
* **Session Management:** Token-based session management shared across Meta's family of apps
* **Security Features:** Two-factor authentication, login anomaly/location-based detection, OAuth permission scopes for third-party apps

## Scalability Strategy

* **Horizontal Scaling:** Massive horizontal sharding of MySQL abstracted via TAO; horizontally scaled stateless service tiers
* **Load Balancing:** Internal L4/L7 load balancing infrastructure spanning Facebook's global data center footprint
* **Auto Scaling:** Managed via Facebook's internal fleet management and capacity planning systems
* **Geographic Distribution:** Globally distributed data centers with regional caching/replication to reduce latency

## Reliability & Fault Tolerance

* **Redundancy:** Multi-region replication for the social graph and critical data stores
* **Failover Strategy:** Automated failover within Facebook's internal infrastructure platform
* **Disaster Recovery:** Cross-region backups; Facebook has publicly discussed large-scale "data center drain" testing to validate failover readiness

## Monitoring & Observability

* **Logging:** Scribe-based centralized logging infrastructure
* **Monitoring:** ODS (Operational Data Store, Facebook's internal time-series monitoring system)
* **Tracing:** Internal distributed tracing tooling integrated with the service mesh
* **Alerting:** Integrated with internal incident response and on-call systems

## Security Measures

* **Encryption:** TLS in transit; encryption at rest for sensitive user data
* **Access Control:** Internal RBAC for engineering access; OAuth scopes for third-party app permissions
* **Threat Protection:** Fake account/bot detection at massive scale, content moderation ML pipelines, anti-scraping defenses

## Engineering Challenges

* Serving the social graph (friends, likes, comments) with extremely low latency at a scale of billions of users (motivating the creation of TAO)
* Scaling Memcached deployments to be among the largest in the world while maintaining cache consistency with MySQL
* Ranking News Feed content in real time across an enormous and constantly changing graph of content and relationships
* Storing and serving hundreds of billions of photos cost-effectively (motivating Haystack and f4)

## Known Technologies Used

PHP/Hack, HHVM, MySQL/InnoDB, TAO, Memcached, Cassandra (created here), RocksDB (created here), Thrift (created here), Scribe, Unicorn, Haystack, f4, React (created here), GraphQL (created here), React Native (created here), Java/Kotlin (Android), Objective-C/Swift (iOS), Presto (created here)

## Architecture Patterns

* **Service-Oriented Architecture on shared internal platform infrastructure**
* **Event-Driven Architecture** for feed/notification pipelines
* **Graph data model pattern** (TAO, Unicorn) layered over relational storage
* **Other Patterns:** Cache-aside pattern with custom invalidation (Memcached + TAO), BigPipe progressive page rendering pattern

## System Design Lessons

Facebook's TAO system is a widely studied example of building a purpose-specific data abstraction layer (graph reads/writes) on top of well-understood underlying storage (MySQL + Memcached) rather than replacing those systems outright — solving the specific latency/scale problem of the social graph without a full database rewrite. Facebook's history also demonstrates how solving internal infrastructure problems at extreme scale (React, GraphQL, Cassandra, Thrift) frequently produces technologies with industry-wide impact far beyond their original use case.

END OF DOCUMENT
