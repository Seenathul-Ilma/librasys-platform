# LibraSys Platform 🏗️

**Student Name:** Seenathul Ilma  
**Student Number:** 241711005  
**Slack Handle:** Seenathul Ilma Musawwir  
**GCP Project ID:** librasys-eca  

---

The foundation of the LibraSys microservices architecture. This repository acts as a "Super Repo" managing the core infrastructure services using Git Submodules.

## Architecture

This repository contains the following platform components:

- **Config Server:** Centralized Spring Cloud Config server that serves configuration properties for all microservices from local YAML files. (Port: `8888`)
- **Service Registry:** Netflix Eureka discovery server allowing microservices to register and find each other dynamically. (Port: `8761`)
- **API Gateway:** Spring Cloud Gateway acting as the single entry point to the backend, routing all client traffic to appropriate microservices. (Port: `8080`)

## Technologies Used
- Java 25
- Spring Boot 3.4.2
- Spring Cloud (Config Server, Netflix Eureka, Gateway)

## GCP Deployment Architecture
- **Compute Engine (VM):** The compiled JARs are deployed on a Debian Linux VM (`librasys-backend`) in `us-central1-a`.
- **Process Management:** All services are managed by **PM2**, which handles automatic restarts on failure and startup on VM reboot.
- **Reverse Proxy & SSL:** Nginx acts as a reverse proxy with a free SSL/TLS certificate via **Let's Encrypt** on domain `https://librasys-eca.duckdns.org` (DuckDNS free domain).
- **Auto Scaling:** Backend is deployed as a **Managed Instance Group** (`librasys-backend-group`) with autoscaling (min 1, max 3 instances).
- **Load Balancing:** GCP HTTP Load Balancer distributes traffic across the instance group.

## How to Run Locally
1. Start the **Config Server** first (Port `8888`).
2. Start the **Service Registry** second (Port `8761`).
3. Start the **API Gateway** third (Port `8080`).
