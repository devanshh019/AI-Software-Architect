# Reddit

## Overview

* **Domain:** Social Media, Online Community, Discussion Forums
* **Product Type:** Community-driven content aggregation and discussion platform organized around "subreddits"
* **Estimated Scale:** 100,000+ active communities; tens of millions of daily active users (publicly reported figures vary by disclosure period)
* **Primary Users:** Consumers/redditors, community moderators, advertisers

## Core Features

* Subreddit-based communities and posts (links, text, images, video)
* Upvote/downvote-based content ranking
* Comment threads (nested discussions)
* Moderation tools for community moderators
* Direct messaging and chat
* Awards and karma system

## High-Level Architecture

Reddit's backend was historically built primarily in Python (using Pylons/Pyramid frameworks) and is well documented as having evolved its data model around a flexible "Thing"/"Data" entity-attribute-value (EAV) pattern, allowing posts, comments, subreddits, and votes to share a common underlying storage abstraction rather than requiring rigid per-feature schemas. Over time, Reddit decomposed parts of its monolithic application into services, while continuing to rely heavily on PostgreSQL (its primary relational store) and Cassandra (for high-write data such as votes and certain feed-related data). Reddit has publicly discussed its caching architecture (Memcached) and queueing infrastructure (RabbitMQ historically, with later adoption of Kafka-class systems) as critical to handling its read-heavy, vote-driven ranking workload.

## Frontend

* **Technologies:** React (Reddit's modern web frontend, "Shreddit," is built on React), TypeScript
* **Client Applications:** Web app (new Reddit and legacy "old Reddit"), iOS app, Android app
* **Rendering Strategy:** Server-side rendering with client-side hydration for the modern React-based web frontend
* **Mobile Stack:** Native iOS (Swift/Objective-C) and Android (Kotlin/Java) apps

## Backend

* **Architecture Style:** Originally a Python monolith, evolved into a service-oriented architecture with extracted services for high-traffic domains
* **Main Services:** Post/listing service, Comment service, Voting/ranking service, Subreddit/community service, Moderation tooling service, Search service
* **Service Communication:** Internal RPC and HTTP/REST-based communication between services
* **API Type:** REST (Reddit's well-known public API, widely used by third-party apps/bots historically) and GraphQL adoption in newer internal services

## Databases

* **Primary Database:** PostgreSQL (core relational data store)
* **Secondary Databases:** Cassandra (for high-write data such as votes and certain activity feeds), Memcached
* **Data Model:** A generalized "Thing" (object) and "Data" (attribute) entity-attribute-value model historically underpinning posts, comments, and subreddits, layered on top of PostgreSQL
* **Replication Strategy:** Master-replica PostgreSQL replication with read replicas for scaling read-heavy traffic
* **Sharding Strategy:** Application-level sharding/partitioning for high-volume data such as comments and votes

## Caching Layer

* **Cache Technology:** Memcached
* **Cache Usage:** Caching post/comment listings, subreddit metadata, vote counts
* **Eviction Strategy:** LRU-based eviction with TTL tuning for ranking-sensitive data

## Message Queue / Event Streaming

* **Technology:** RabbitMQ (historically widely used at Reddit), with broader industry-standard adoption of Kafka-class systems for newer event pipelines
* **Purpose:** Asynchronous processing of votes, notifications, and comment/post propagation
* **Event Types:** Post created events, vote cast events, comment created events, moderation action events

## Search Infrastructure

* **Search Engine:** Elasticsearch (Reddit has discussed using Elasticsearch-based infrastructure for post/comment search)
* **Search Use Cases:** Post search, subreddit search, comment search

## Storage Systems

* **Object Storage:** Cloud object storage (e.g., AWS S3-class) for media (images, videos) uploaded directly to Reddit
* **File Storage:** Cloud-backed blob storage for media hosting
* **CDN:** Fastly (Reddit has publicly used Fastly as part of its CDN/edge strategy) and other CDN providers for content delivery

## Real-Time Systems

* **Real-Time Components:** Real-time vote count updates, real-time comment updates, live chat features
* **Protocols:** WebSocket-based real-time updates for live threads/chat
* **Streaming Technologies:** Event-driven pipelines feeding ranking/ "hot" algorithm recalculations in near-real-time

## Authentication & Authorization

* **Authentication Method:** Username/password, OAuth2 (Reddit's public API uses OAuth2 for third-party app authorization), social login options
* **Session Management:** Token/cookie-based session management
* **Security Features:** Two-factor authentication, account anomaly detection, moderator-specific permission tiers

## Scalability Strategy

* **Horizontal Scaling:** Horizontally scaled stateless application services; read replicas for PostgreSQL to handle read-heavy listing/ranking traffic
* **Load Balancing:** Cloud-native load balancers (Reddit runs primarily on AWS)
* **Auto Scaling:** AWS Auto Scaling Groups/container-based scaling for compute workloads
* **Geographic Distribution:** Multi-region AWS deployment combined with CDN edge presence for global content delivery

## Reliability & Fault Tolerance

* **Redundancy:** Multi-AZ deployment for critical services and databases
* **Failover Strategy:** Automated database failover for PostgreSQL/Cassandra clusters
* **Disaster Recovery:** Backup and recovery strategies leveraging AWS-native tooling

## Monitoring & Observability

* **Logging:** Centralized logging infrastructure (internal tooling on open-source-derived stacks)
* **Monitoring:** Internal dashboards and metrics pipelines for site health and ranking system performance
* **Tracing:** Distributed tracing tooling for service-to-service call chains
* **Alerting:** Integrated alerting for system health and abuse/spam spikes

## Security Measures

* **Encryption:** TLS in transit; encryption at rest for sensitive user data
* **Access Control:** Role-based moderator permission tiers within subreddits; OAuth scopes for third-party API access
* **Threat Protection:** Vote manipulation/brigading detection, spam/bot account detection, content moderation tooling (both automated and human moderator-driven)

## Engineering Challenges

* Computing and re-ranking "hot"/"best" post rankings in near real-time across hundreds of thousands of active communities
* Detecting and mitigating vote manipulation and coordinated brigading at scale
* Supporting a long-standing, heavily-used public API while balancing third-party app access against platform sustainability (a tension that became publicly contentious in 2023)
* Scaling a flexible but generic "Thing/Data" data model originally designed for a much smaller platform

## Known Technologies Used

Python, Pylons/Pyramid, PostgreSQL, Cassandra, Memcached, RabbitMQ, Elasticsearch, AWS (EC2, S3), Fastly, React, TypeScript, Kotlin/Java (Android), Swift/Objective-C (iOS)

## Architecture Patterns

* **Monolith with extracted services (evolutionary SOA)**
* **Event-Driven Architecture** for vote/comment propagation
* **Entity-Attribute-Value (EAV) data modeling pattern** ("Thing/Data" model)
* **Other Patterns:** Caching-aside pattern for ranking data, API-first platform pattern (public API and bot ecosystem)

## System Design Lessons

Reddit's "Thing/Data" generalized data model is a notable case study in designing a flexible schema that allows many different content types (posts, comments, subreddits, votes) to share common storage and indexing infrastructure, trading some query efficiency for development flexibility in the platform's early years. Reddit's ranking algorithm and vote-count architecture also illustrate the broader challenge of building real-time, abuse-resistant ranking systems for community-generated content at scale.

END OF DOCUMENT
