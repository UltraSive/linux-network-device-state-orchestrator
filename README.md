# linux-network-device-state-orchestrator

A distributed control plane for managing DPDK/AF_XDP VPP routers and SONiC switches across multiple sites. This system enables scalable configuration management through a RESTful API, gRPC-based device communication, and on-device state caching.

## Overview

The Network State Orchestrator provides a centralized yet distributed architecture for orchestrating dynamic network state, including:

- IP address assignments
- Routing tables
- MAC address bindings
- ACLs and firewall rules
- Device metadata and configuration versioning

Each network device subscribes to only the relevant subset of state data. Devices receive updates over gRPC and cache state locally using SQLite for high availability and offline support.

## Architecture

```
+-------------+        REST         +----------------+         gRPC         +------------------+
|  Operator   | ------------------> | Control Plane  | ------------------> | Device Agent     |
|  (User/API) |                     | (Golang)       |                     | (VPP / SONiC)     |
+-------------+                     +----------------+                     +------------------+
        |                                 |                                          |
        |                                 |                                          |
        |                       PostgreSQL Database                        Local SQLite Cache
        |                                 |                                          |
        v                                 v                                          v
   Configuration                  Network State DB                           Cached State
     Requests
```

## Features

- Versioned state model for consistent reconciliation
- gRPC streaming for real-time, event-driven updates
- Support for VPP (via GoVPP) and SONiC (via gNMI)
- On-device SQLite caching for state persistence
- Modular data models for routes, interfaces, ACLs, and more
- Device metadata, filtering, and tagging support
- Secure transport using TLS/mTLS

## Technology Stack

- Go (REST API, orchestration logic)
- PostgreSQL (central configuration database)
- gRPC + Protobuf (for control messages)
- GoVPP (VPP device integration)
- gNMI (SONiC device integration)
- SQLite (on-device config cache)

## Database Schema

The PostgreSQL database models include:

- `devices`: Tracks all managed devices and their types
- `routes`, `ip_assignments`, `mac_bindings`, `acl_rules`: Core network state objects
- `device_tags`: Key-value metadata tags for flexible filtering and grouping

## Getting Started

### Prerequisites

- Go 1.21+
- Docker + Docker Compose
- PostgreSQL 15+
