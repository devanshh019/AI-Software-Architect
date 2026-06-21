# X (Twitter)

## Overview

* **Domain:** Social Media, Microblogging, Real-Time News/Discourse
* **Product Type:** Real-time public social networking and content broadcasting platform
* **Estimated Scale:** Several hundred million monthly active users (exact current figures not consistently publicly disclosed since the platform's transition to X)
* **Primary Users:** Consumers, public figures/creators, businesses/advertisers, developers (via API)

## Core Features

* Short-form posts ("Tweets"/posts) with text, images, video
* Timelines (Following and algorithmic "For You" feeds)
* Likes, reposts/retweets, replies, quote posts
* Direct messaging
* Live audio rooms (Spaces) and live video
* Trending topics and real-time search

## High-Level Architecture

Twitter's backend famously began as a Ruby on Rails monolith that struggled with scale (the well-known "fail whale" era), prompting a major migration to a JVM-based (Scala/Java) microservices architecture for core services, while keeping Rails for some front-end-adjacent functionality for a time. Twitter built and open-sourced several influential pieces of infrastructure: Finagle (an asynchronous RPC framework for the JVM), Mesos co-creation (cluster scheduling, alongside UC Berkeley), Manhattan (Twitter's distributed database, successor to Cassandra-based storage), and Gizzard/FlockDB (a sharded graph database originally used for the social graph). The system architecture centers on a fan-out/fan-in model for timeline delivery, with a hybrid push (fan-out-on-write for most users) and pull (fan-out-on-read for high-follower accounts) model to deliver tweets into followers' timelines efficiently.

## Frontend

* **Technologies:** React (web client, "Twitter Lite"/PWA architecture was a notable public case study), TypeScript
* **Client Applications:** Web app, iOS app, Android app, mobile web (PWA)
* **Rendering Strategy:** Twitter's PWA ("Twitter Lite") was a widely cited case study in progressive web app architecture for low-bandwidth markets; native rendering on mobile apps
* **Mobile Stack:** Native iOS (Swift/Objective-C) and Android (Kotlin/Java)

## Backend

* **Architecture Style:** Microservices (JVM-based, migrated from an original Ruby on Rails monolith)
* **Main Services:** Timeline service, Tweet service, Social graph service, Search/indexing service, Direct messaging service, Notification service
* **Service Communication:** Finagle (Twitter's open-sourced asynchronous RPC framework for Scala/Java services), Thrift for service interface definitions
* **API Type:** REST (public Twitter/X API for developers), internal RPC (Thrift/Finagle) between services

## Databases

* **Primary Database:** Manhattan (Twitter's distributed, sharded key-value database, successor to earlier Cassandra usage)
* **Secondary Databases:** MySQL (historically core relational data), Gizzard/FlockDB (sharded graph database for the social graph), Redis
* **Data Model:** Key-value (Manhattan) for tweets/timeline data; graph model (FlockDB) for follower/following relationships
* **Replication Strategy:** Multi-datacenter replication for Manhattan with tunable consistency models
* **Sharding Strategy:** Gizzard-based sharding framework distributing graph edges (follows) across many shards; consistent hashing in Manhattan

## Caching Layer

* **Cache Technology:** Memcached/Redis-class in-memory caching
* **Cache Usage:** Caching timelines (pre-computed fan-out timelines for most users), tweet metadata, user profile data
* **Eviction Strategy:** TTL-based and LRU eviction for timeline and metadata caches

## Message Queue / Event Streaming

* **Technology:** Apache Kafka (adopted over time for event streaming), internal pub/sub systems
* **Purpose:** Fan-out of tweets to follower timelines, real-time analytics, ad event tracking
* **Event Types:** Tweet creation events, like/retweet events, follow/unfollow events, DM events

## Search Infrastructure

* **Search Engine:** Lucene-based custom real-time search infrastructure (Twitter built a real-time inverted index system to support searching tweets within seconds of posting)
* **Search Use Cases:** Real-time tweet search, trending topics computation, hashtag search

## Storage Systems

* **Object Storage:** Internal/cloud blob storage for media (images, videos)
* **File Storage:** HDFS-based big data infrastructure for analytics
* **CDN:** Third-party CDN for media delivery

## Real-Time Systems

* **Real-Time Components:** Real-time timeline updates, real-time trending topics, real-time search indexing, Twitter/X Spaces (live audio)
* **Protocols:** WebSocket/streaming HTTP for real-time client updates; custom protocols for Spaces (audio streaming)
* **Streaming Technologies:** Real-time fan-out pipeline for tweet delivery; Storm (Twitter co-created/popularized real-time stream processing with Apache Storm) historically used for real-time analytics

## Authentication & Authorization

* **Authentication Method:** Username/password, OAuth1.0a/OAuth2 (Twitter's API has long supported OAuth for third-party developers)
* **Session Management:** Token-based session management
* **Security Features:** Two-factor authentication, account verification (historically the blue checkmark system), anomaly detection for bot/spam accounts

## Scalability Strategy

* **Horizontal Scaling:** Horizontally scaled JVM-based microservices; sharded data stores (Manhattan, Gizzard)
* **Load Balancing:** Internal L4/L7 load balancing integrated with Finagle's client-side load balancing capabilities
* **Auto Scaling:** Container/VM-based scaling on Twitter's infrastructure (historically a mix of owned data centers and cloud)
* **Geographic Distribution:** Multi-datacenter deployment for redundancy and latency

## Reliability & Fault Tolerance

* **Redundancy:** Multi-datacenter replication for Manhattan and core services
* **Failover Strategy:** Finagle's built-in retry/circuit-breaking and load-balancing-aware failover
* **Disaster Recovery:** Cross-datacenter backups and replication for critical data

## Monitoring & Observability

* **Logging:** Centralized logging infrastructure (internal tooling)
* **Monitoring:** Internal observability platform (Twitter has discussed Zipkin, which it created and open-sourced, for distributed tracing/observability)
* **Tracing:** Zipkin (created by Twitter, widely adopted industry-wide as an open-source distributed tracing system)
* **Alerting:** Integrated with internal incident response tooling

## Security Measures

* **Encryption:** TLS in transit; encryption at rest for sensitive account data
* **Access Control:** OAuth scopes for API consumers; internal RBAC for engineering access
* **Threat Protection:** Bot/spam detection systems, account takeover protection, content moderation pipelines

## Engineering Challenges

* Scaling timeline fan-out for users with extremely large follower counts (celebrities) without overwhelming write paths
* Achieving real-time search indexing of tweets within seconds of posting at massive scale
* Migrating from a Ruby on Rails monolith to a JVM-based microservices architecture without prolonged downtime
* Handling massive, unpredictable traffic spikes during live global events

## Known Technologies Used

Ruby on Rails (historical), Scala, Java, Finagle, Thrift, Manhattan, Gizzard/FlockDB, MySQL, Redis/Memcached, Apache Kafka, Apache Storm, Zipkin (created by Twitter), Mesos (co-created), React, Kotlin/Java (Android), Swift/Objective-C (iOS)

## Architecture Patterns

* **Microservices** (JVM-based, migrated from a Rails monolith)
* **Event-Driven Architecture** for fan-out and engagement pipelines
* **Fan-out-on-write / Fan-out-on-read hybrid pattern** for timeline delivery
* **Other Patterns:** Sharded graph database pattern (Gizzard/FlockDB), circuit breaker pattern (via Finagle), distributed tracing pattern (Zipkin)

## System Design Lessons

Twitter/X's "fail whale" era and subsequent migration is one of the most cited cautionary tales in the industry about the limits of a single monolithic Rails application under viral, unpredictable load — and a roadmap for migrating to a JVM-based microservices architecture under pressure. Its hybrid fan-out strategy (push for most users, pull for celebrity accounts) is also a widely taught system design pattern for solving the "celebrity problem" in social timeline architectures.

END OF DOCUMENT
