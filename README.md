# BookTicket :: Platform :: Eureka Server (Service Registry)

## Overview

The **Eureka Server** is the service registry for the BookTicket microservices ecosystem. It functions as a dynamic "phone book," allowing services to locate and communicate with each other without hardcoding network locations. This is a critical component for enabling resilience, scalability, and client-side load balancing in a distributed system.

This service is an implementation of **Spring Cloud Netflix Eureka Server**.

## Core Responsibilities

-   **Service Registration:** Listens for new microservice instances to come online and registers their network location (IP address and port).
-   **Service Discovery:** Provides an API for other services to query and discover the network locations of the services they need to communicate with.
-   **Health Monitoring:** Tracks the health of each registered service instance via periodic heartbeats. If an instance fails to send a heartbeat, it is removed from the registry.

## Architecture
<img width="1953" height="780" alt="Eureka-Server" src="https://github.com/user-attachments/assets/49fa5c72-78de-4a44-9545-40643154c361" />


### How It Works

1.  **Registration:** When a microservice (e.g., `user-service`) starts, its embedded Eureka client sends a registration request to the Eureka Server, providing its service name, IP address, and port.
2.  **Heartbeats:** The client then sends a heartbeat to the server every 30 seconds to confirm it is still alive and healthy. If the server doesn't receive a heartbeat for 90 seconds, it removes the instance from its registry.
3.  **Discovery:** When another service (e.g., the `api-gateway`) needs to communicate with the `user-service`, it asks the Eureka Server for a list of all healthy instances of `user-service`.
4.  **Client-Side Load Balancing:** The API Gateway's built-in load balancer receives this list of IP addresses and intelligently chooses one to forward the request to. If the request fails, it can try another instance from the list.
5.  **Resilience:** All clients maintain a local cache of the Eureka registry. If the Eureka Server becomes temporarily unavailable, clients can continue to function using their cached information.

## Configuration

The Eureka Server is configured via properties fetched from the **Config Server**. Key properties in its `eureka-server-prod.yaml` file include:

-   `eureka.client.register-with-eureka: false`: Prevents the server from trying to register with itself.
-   `eureka.client.fetch-registry: false`: Prevents the server from trying to fetch a registry from a peer. In a multi-node cluster, this would be `true`.
-   `server.port`: The port on which the Eureka Server UI and API are available (default: `8761`).

## Eureka Dashboard

The Eureka Server provides a web dashboard to visualize the status of all registered microservices. It is accessible at the root URL of the service:

-   **URL:** `https://service-registry-a48d.onrender.com/`
