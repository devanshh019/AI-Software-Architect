# Discord

## Overview

* **Domain:** Communication, Community, Gaming-adjacent Social Platform
* **Product Type:** Real-time chat, voice, and video communication platform organized around servers/communities
* **Estimated Scale:** 150M+ monthly active users (publicly reported figures); billions of messages sent daily
* **Primary Users:** Gamers, online communities, creators, businesses using Discord for community engagement

## Core Features

* Text channels and direct messaging
* Real-time voice and video calls
* Servers with channels, roles, and permissions
* Screen sharing and "Go Live" streaming
* Bots and third-party integrations
* Push-to-talk and noise suppression for voice

## High-Level Architecture

Discord's backend is well documented through its engineering blog as a microservices architecture with a strong emphasis on real-time messaging infrastructure. The core chat/gateway layer was originally built in Elixir (running on the Erlang VM/BEAM) to take advantage of lightweight process-based concurrency for handling millions of simultaneous WebSocket connections. Discord has also publicly discussed migrating its primary message storage from MongoDB to Cassandra, and later from Cassandra to ScyllaDB (a C++ rewrite of Cassandra's data model) to handle trillions of messages with better latency and operational efficiency. Voice/video infrastructure is built on WebRTC with custom routing/media servers (some components written in Rust for performance).

## Frontend

* **Technologies:** React, Electron (desktop app), React Native (mobile, adopted for parts of the app)
* **Client Applications:** Desktop app (Windows/Mac/Linux via Electron), web app, iOS app, Android app
* **Rendering Strategy:** Client-side rendering (SPA) for desktop/web via React; native rendering supplemented by React Native on mobile
* **Mobile Stack:** Native and React Native hybrid approach on iOS/Android

## Backend

* **Architecture Style:** Microservices
* **Main Services:** Gateway/connection service (Elixir), Message persistence service, Voice/video media routing service, Presence service, Push notification service
* **Service Communication:** Internal RPC and message-passing (Elixir/Erlang's native messaging for gateway components); HTTP/REST for client-facing API
* **API Type:** REST (Discord's public Bot API and client API) plus WebSocket Gateway API for real-time events

## Databases

* **Primary Database:** ScyllaDB (migrated from Cassandra, which was itself migrated from MongoDB) for message storage at massive scale
* **Secondary Databases:** PostgreSQL/Cockroach-class relational stores for structured data (guild/server metadata, user accounts), Redis
* **Data Model:** Wide-column (ScyllaDB) for message history; relational for account/guild structured data
* **Replication Strategy:** Multi-datacenter replication for ScyllaDB clusters
* **Sharding Strategy:** ScyllaDB's consistent hashing-based partitioning by channel ID for message data

## Caching Layer

* **Cache Technology:** Redis
* **Cache Usage:** Caching presence/online status, guild member lists, session data
* **Eviction Strategy:** TTL-based eviction for ephemeral presence/session data

## Message Queue / Event Streaming

* **Technology:** Internal messaging within the Elixir/Erlang gateway cluster; additional queueing systems for cross-service event propagation (specific vendor choices not exhaustively publicly detailed)
* **Purpose:** Real-time event fan-out (messages, presence updates) to connected clients across a guild/server
* **Event Types:** Message create/update/delete events, presence updates, voice state updates, typing indicators

## Search Infrastructure

* **Search Engine:** Elasticsearch (used historically for message search; Discord has discussed challenges and trade-offs of search at scale on its engineering blog)
* **Search Use Cases:** In-server and DM message search

## Storage Systems

* **Object Storage:** Google Cloud Storage (for attachments/media, per Discord's public cloud infrastructure usage)
* **File Storage:** Cloud object storage for uploaded images/files/attachments
* **CDN:** Google Cloud CDN/third-party CDN for media and attachment delivery

## Real-Time Systems

* **Real-Time Components:** Real-time text messaging, voice/video calls, presence/typing indicators, screen sharing
* **Protocols:** WebSocket (Gateway API) for real-time events; WebRTC for voice/video; UDP-based custom protocols for low-latency voice routing
* **Streaming Technologies:** Custom-built voice server infrastructure (some components in Rust) for low-latency audio routing and mixing

## Authentication & Authorization

* **Authentication Method:** Username/password, OAuth2 (also exposed publicly for third-party app/bot authorization)
* **Session Management:** Token-based session management with persistent Gateway WebSocket connections
* **Security Features:** Two-factor authentication, OAuth2 scopes for bots/integrations, account verification

## Scalability Strategy

* **Horizontal Scaling:** Elixir/Erlang's process model allows individual gateway nodes to handle large numbers of concurrent connections; horizontally scaled stateless services elsewhere
* **Load Balancing:** Custom connection routing for Gateway sessions; standard load balancers for REST API traffic
* **Auto Scaling:** Cloud-based auto-scaling for stateless services (Discord runs primarily on Google Cloud Platform)
* **Geographic Distribution:** Globally distributed voice/media servers to minimize latency for real-time voice/video

## Reliability & Fault Tolerance

* **Redundancy:** Erlang/OTP supervisor-tree fault tolerance in gateway layer; multi-region database replication
* **Failover Strategy:** Automatic process restarts (Erlang supervision trees), database replica failover
* **Disaster Recovery:** Multi-region backups and replication for critical data stores

## Monitoring & Observability

* **Logging:** Centralized logging infrastructure built on cloud-native and open-source tooling
* **Monitoring:** Internal dashboards and metrics pipelines (Discord has publicly discussed Prometheus-class monitoring usage)
* **Tracing:** Distributed tracing tooling for cross-service request tracking
* **Alerting:** Integrated alerting for service health and SLA breaches

## Security Measures

* **Encryption:** TLS in transit; encryption at rest for stored messages/attachments
* **Access Control:** Role-based permission system within servers (channel/role permissions); OAuth2 scopes for bots
* **Threat Protection:** Anti-spam/abuse detection systems, automated moderation tooling (AutoMod), DDoS protection for voice infrastructure

## Engineering Challenges

* Storing and querying trillions of chat messages with low-latency read/write performance (driving the MongoDB → Cassandra → ScyllaDB migration path)
* Maintaining millions of persistent real-time WebSocket connections reliably (Gateway scaling)
* Building low-latency global voice/video infrastructure comparable to dedicated VoIP providers
* Scaling permission systems for large servers with complex role hierarchies

## Known Technologies Used

Elixir/Erlang (BEAM), Rust, Python, React, Electron, React Native, ScyllaDB, Cassandra (historical), MongoDB (historical), PostgreSQL, Redis, Elasticsearch, Google Cloud Platform, WebRTC, Protocol Buffers

## Architecture Patterns

* **Microservices**
* **Actor Model** (via Elixir/Erlang processes) for connection/session management
* **Event-Driven Architecture** for real-time message/presence fan-out
* **Other Patterns:** Pub/Sub fan-out for guild events, supervisor-tree fault recovery pattern

## System Design Lessons

Discord's well-documented database migration journey (MongoDB → Cassandra → ScyllaDB) is a widely cited case study in choosing storage engines based on evolving operational requirements (write amplification, tail latency, compaction overhead) rather than treating an initial technology choice as permanent. Its use of Erlang/Elixir for the real-time gateway layer also demonstrates how concurrency-model choice can be the dominant factor in achieving massive real-time connection scale.

END OF DOCUMENT
