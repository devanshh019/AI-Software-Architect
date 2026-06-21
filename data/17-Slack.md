# Slack

## Overview

* **Domain:** Business Communication, Collaboration
* **Product Type:** Real-time team messaging and collaboration platform (owned by Salesforce)
* **Estimated Scale:** 200,000+ paid organizations and tens of millions of daily active users (publicly reported figures vary by disclosure)
* **Primary Users:** Businesses, teams, developers (via Slack API and apps/bots)

## Core Features

* Channel-based messaging (public/private channels)
* Direct messaging and group DMs
* Voice/video calls (Slack Huddles)
* File sharing and search
* Workflow automation and bots/integrations
* Threaded conversations

## High-Level Architecture

Slack's backend was historically built primarily in PHP (using Hack/HHVM, similar in lineage to Facebook's PHP infrastructure approach) for its core web application logic, with MySQL as the primary datastore, scaled horizontally using Vitess (the YouTube-originated MySQL clustering system, also adopted by Slack) to handle sharding across a large number of workspaces. Real-time message delivery is handled through a dedicated WebSocket-based gateway layer that fans out messages to connected clients in real time. Slack runs primarily on AWS, with services for search (built on a customized Elasticsearch-based system called "the Search Index Service") and job queueing for asynchronous workflows (notification delivery, integrations, file processing).

## Frontend

* **Technologies:** React, Electron (desktop app), TypeScript
* **Client Applications:** Desktop app (Windows/Mac/Linux via Electron), web app, iOS app, Android app
* **Rendering Strategy:** Client-side rendered SPA via React for desktop/web
* **Mobile Stack:** Native iOS (Swift/Objective-C) and Android (Kotlin/Java) apps

## Backend

* **Architecture Style:** Originally a PHP/Hack monolith, evolved with service extraction for specific high-scale domains (search, messaging gateway, jobs)
* **Main Services:** Messaging/Gateway service (WebSocket-based real-time delivery), Channel/workspace service, Search service, Notification service, Workflow/automation service
* **Service Communication:** Internal RPC and HTTP-based service communication; WebSocket-based real-time channel for client connections
* **API Type:** REST (Slack's well-documented public Web API) plus the Events API (webhook-based) and a real-time messaging WebSocket API (RTM API/Socket Mode)

## Databases

* **Primary Database:** MySQL, scaled using Vitess for horizontal sharding across workspaces
* **Secondary Databases:** Memcached, Elasticsearch (for search indexing)
* **Data Model:** Relational (MySQL) for messages, channels, and user/workspace metadata
* **Replication Strategy:** Master-replica MySQL replication, managed/coordinated via Vitess across shards
* **Sharding Strategy:** Vitess-managed horizontal sharding, with workspace-based partitioning being a key sharding dimension given Slack's multi-tenant architecture

## Caching Layer

* **Cache Technology:** Memcached
* **Cache Usage:** Caching channel membership data, frequently accessed message history, user presence data
* **Eviction Strategy:** TTL and LRU-based eviction tuned per cache pool

## Message Queue / Event Streaming

* **Technology:** Internal job queue systems; Slack has publicly discussed using systems in the Kafka-class for some event pipelines
* **Purpose:** Asynchronous processing for notifications, integrations/bots, search indexing, file processing
* **Event Types:** Message sent events, channel join/leave events, integration/webhook trigger events

## Search Infrastructure

* **Search Engine:** A customized Elasticsearch-based search system (Slack has publicly described building dedicated search index infrastructure to handle message search at scale across workspaces)
* **Search Use Cases:** Message search, file search, channel/people search

## Storage Systems

* **Object Storage:** Amazon S3 (for shared files, images, attachments)
* **File Storage:** S3-backed storage for uploaded files and media
* **CDN:** Amazon CloudFront and/or other CDN providers for static asset and media delivery

## Real-Time Systems

* **Real-Time Components:** Real-time messaging delivery, real-time presence indicators, real-time typing indicators, Huddles (voice/video)
* **Protocols:** WebSocket-based real-time messaging gateway; WebRTC for Huddles voice/video
* **Streaming Technologies:** Custom WebSocket gateway infrastructure designed to fan out messages to all connected clients in a channel/workspace in real time

## Authentication & Authorization

* **Authentication Method:** Username/password, SSO (SAML/OAuth2 widely supported for enterprise customers), OAuth2 for third-party app/bot authorization
* **Session Management:** Token-based session management with long-lived authenticated WebSocket connections
* **Security Features:** Two-factor authentication, enterprise-grade SSO/SCIM provisioning, granular workspace admin controls

## Scalability Strategy

* **Horizontal Scaling:** Vitess-managed MySQL sharding; horizontally scaled stateless application/gateway services
* **Load Balancing:** AWS ELB/ALB and internal load balancing for WebSocket connection routing
* **Auto Scaling:** AWS Auto Scaling Groups for compute workloads
* **Geographic Distribution:** Multi-region AWS deployment for redundancy and latency optimization

## Reliability & Fault Tolerance

* **Redundancy:** Multi-AZ deployment for critical services and databases
* **Failover Strategy:** Automated database failover (Vitess-coordinated) and service-level circuit breaking
* **Disaster Recovery:** Cross-region backup and recovery strategies leveraging AWS-native tooling

## Monitoring & Observability

* **Logging:** Centralized logging infrastructure (internal tooling on open-source-derived stacks)
* **Monitoring:** Internal monitoring dashboards integrated with AWS CloudWatch and custom tooling
* **Tracing:** Distributed tracing for service-to-service call chains
* **Alerting:** PagerDuty-class incident alerting integrated with monitoring stack

## Security Measures

* **Encryption:** TLS in transit; encryption at rest for messages and files (Slack offers Enterprise Key Management for customer-controlled encryption keys)
* **Access Control:** Granular workspace/channel permissions, enterprise SSO/SCIM, OAuth scopes for apps/bots
* **Threat Protection:** Data loss prevention (DLP) integrations, malware scanning for shared files, anomaly detection for account compromise

## Engineering Challenges

* Scaling a multi-tenant MySQL architecture (via Vitess) to support hundreds of thousands of independent workspaces with vastly different sizes and usage patterns
* Real-time message delivery and fan-out at scale across large channels/workspaces with persistent WebSocket connections
* Building enterprise-grade search across years of message history while respecting workspace data isolation
* Supporting an extensive third-party app/bot ecosystem without compromising platform stability or security

## Known Technologies Used

PHP/Hack, HHVM, MySQL, Vitess, Memcached, Elasticsearch, AWS (EC2, S3, RDS, CloudFront), React, Electron, TypeScript, Kotlin/Java (Android), Swift/Objective-C (iOS)

## Architecture Patterns

* **Monolith with extracted services** (similar evolutionary pattern to other large PHP-based platforms)
* **Event-Driven Architecture** for notifications, integrations, and search indexing
* **Multi-tenant sharding pattern** (Vitess, workspace-based partitioning)
* **Other Patterns:** WebSocket gateway/fan-out pattern, API-first platform pattern (Slack's extensive public API/app ecosystem)

## System Design Lessons

Slack demonstrates how a PHP-based application can be scaled to enterprise-grade reliability by adopting purpose-built sharding tooling (Vitess) rather than rewriting the application layer in a different language, similar to YouTube's own use of Vitess. Its design also highlights the architectural importance of treating "workspace" as a first-class multi-tenancy boundary throughout the system — from database sharding to search indexing to permission models — which is a recurring lesson for any B2B SaaS platform operating at scale.

END OF DOCUMENT
