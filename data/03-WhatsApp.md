# WhatsApp

## Overview

* **Domain:** Messaging, Communication
* **Product Type:** Cross-platform instant messaging and VoIP application
* **Estimated Scale:** 2B+ users globally; historically known for handling huge message volume with a famously small engineering/server team
* **Primary Users:** Consumers and businesses (WhatsApp Business) for messaging, voice/video calls

## Core Features

* One-to-one and group messaging
* End-to-end encrypted voice and video calls
* Media sharing (photos, video, documents)
* Status updates (stories-like feature)
* WhatsApp Business API/Cloud API for businesses
* Multi-device support

## High-Level Architecture

WhatsApp's backend was historically famous for achieving massive scale (well over a billion users) with a remarkably small server-side engineering team, built on Erlang/FreeBSD. The core messaging server used Erlang's actor-style concurrency model (via the ejabberd-derived/XMPP-inspired custom server) to handle millions of concurrent connections per server. After Facebook's acquisition, WhatsApp's infrastructure was gradually integrated with Meta's broader infrastructure (data centers, some shared services), while message transport continued to rely on a heavily customized, highly concurrent erlang-based system. End-to-end encryption (based on the Signal Protocol) is implemented client-side so the server only ever relays encrypted blobs.

## Frontend

* **Technologies:** Native mobile clients; WhatsApp Web uses JavaScript/React-like rendering via a browser-based client that mirrors phone state
* **Client Applications:** Android, iOS, WhatsApp Web/Desktop, WhatsApp Business app
* **Rendering Strategy:** Native rendering on mobile; WhatsApp Web operates as a companion client synced with the phone (historically) and later moved toward multi-device independent operation
* **Mobile Stack:** Native Android (Java/Kotlin) and iOS (Objective-C/Swift) applications

## Backend

* **Architecture Style:** Originally a tightly optimized monolith-like Erlang messaging server cluster; evolved with additional services post-acquisition
* **Main Services:** Message routing/relay servers, presence service, media relay service, push notification dispatch
* **Service Communication:** Custom binary protocol (originally XMPP-derived, later a proprietary binary protocol) between client and server
* **API Type:** Proprietary binary protocol for client-server messaging; REST/HTTP-based Cloud API for WhatsApp Business integrations

## Databases

* **Primary Database:** Mnesia (Erlang's built-in distributed database) historically used for some session/state data; FreeBSD-based custom storage
* **Secondary Databases:** Not Publicly Available (details of post-acquisition datastore changes are largely undisclosed)
* **Data Model:** Key-value/ephemeral message queueing model — WhatsApp historically did not persist messages long-term on servers once delivered
* **Replication Strategy:** Not Publicly Available
* **Sharding Strategy:** User-based partitioning across server clusters (historically by phone number hashing)

## Caching Layer

* **Cache Technology:** Not Publicly Available (likely Memcached-class systems shared with Meta infra post-acquisition)
* **Cache Usage:** Presence/online-status caching, session state caching
* **Eviction Strategy:** Not Publicly Available

## Message Queue / Event Streaming

* **Technology:** Custom Erlang-based message queueing within the messaging cluster; Meta's internal infra (e.g., Kafka-class systems) likely used for analytics post-acquisition
* **Purpose:** Reliable store-and-forward message delivery to offline devices
* **Event Types:** Message delivery, read receipts, presence updates, typing indicators

## Search Infrastructure

* **Search Engine:** Not Publicly Available (chat search is performed primarily client-side/on-device for privacy)
* **Search Use Cases:** Local on-device message search

## Storage Systems

* **Object Storage:** Media (photos/videos/docs) relayed and temporarily stored encrypted on servers until delivered, then deleted; long-term storage primarily on-device
* **File Storage:** Not Publicly Available in detail
* **CDN:** Meta's internal CDN/edge infrastructure used for media relay post-acquisition

## Real-Time Systems

* **Real-Time Components:** Real-time message delivery, presence ("last seen"/online status), typing indicators, real-time voice/video calls
* **Protocols:** Custom TCP-based binary protocol; WebRTC-based protocols for voice/video calls
* **Streaming Technologies:** Custom Erlang process-per-connection model enabling millions of concurrent persistent connections per server

## Authentication & Authorization

* **Authentication Method:** Phone number verification via SMS/voice OTP
* **Session Management:** Long-lived authenticated sessions tied to device and phone number registration
* **Security Features:** End-to-end encryption using the Signal Protocol for messages, calls, and status updates

## Scalability Strategy

* **Horizontal Scaling:** Erlang's lightweight process model allowed each server to handle millions of concurrent connections, reducing the number of servers needed
* **Load Balancing:** Custom connection routing across messaging server clusters
* **Auto Scaling:** Not Publicly Available in detail; historically scaled via careful capacity planning rather than dynamic cloud auto-scaling
* **Geographic Distribution:** Globally distributed data centers (increasingly integrated with Meta's global data center footprint)

## Reliability & Fault Tolerance

* **Redundancy:** Erlang/OTP's supervisor-tree model for process-level fault tolerance and automatic restarts
* **Failover Strategy:** Process supervision trees restart failed components without taking down the whole system
* **Disaster Recovery:** Not Publicly Available in detail

## Monitoring & Observability

* **Logging:** Not Publicly Available in detail
* **Monitoring:** Not Publicly Available in detail (likely integrated with Meta's internal observability stack post-acquisition)
* **Tracing:** Not Publicly Available
* **Alerting:** Not Publicly Available

## Security Measures

* **Encryption:** End-to-end encryption (Signal Protocol) for messages, voice, and video calls
* **Access Control:** Device-bound encryption keys, multi-device key management
* **Threat Protection:** Spam/abuse detection systems, account banning for automated abuse

## Engineering Challenges

* Achieving massive concurrent-connection scale with a historically very small engineering team
* Implementing end-to-end encryption across one-to-one, group, and multi-device contexts without breaking usability
* Reliable store-and-forward delivery for users who are frequently offline (especially in regions with poor connectivity)
* Scaling group messaging and media relay while preserving encryption guarantees

## Known Technologies Used

Erlang/OTP, FreeBSD, Mnesia, XMPP (early protocol basis), Signal Protocol (E2E encryption), WebRTC (calls), Java/Kotlin (Android), Objective-C/Swift (iOS), Meta internal infrastructure (post-acquisition)

## Architecture Patterns

* **Monolith-like high-performance messaging cluster** (intentionally simple, optimized rather than microservices-heavy)
* **Actor Model** (via Erlang processes) for per-connection isolation and fault tolerance
* **Store-and-forward messaging pattern**
* **Other Patterns:** Supervisor trees for fault recovery, end-to-end encryption pattern (Signal Protocol's double-ratchet algorithm)

## System Design Lessons

WhatsApp demonstrates that choosing the right concurrency primitive (Erlang's lightweight, isolated processes) can let a small team achieve massive scale without needing a sprawling microservices architecture. It also shows how end-to-end encryption can be implemented as a client-side concern layered on top of a relatively simple relay-based server architecture, keeping the server "dumb" by design for both performance and privacy reasons.

END OF DOCUMENT
