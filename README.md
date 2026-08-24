# LibraSys — Microservices Platform Repository (Super Repository)

**Student Name:** Seenathul Ilma
**Student Number:** 241711005  
**Slack Handle:** Seenathul Ilma Musawwir  
**GCP Project ID:** librasys-eca 

---

## 📖 Project Description
This super repository contains all the core infrastructure platform services for the **LibraSys Library Management System**, organized using **Git Submodules** in a polyrepo architecture.

### Submodule Platform Services:
1. **[Config Server](file:///c:/IJSE/Enterprice%20Cloud%20Architecture/ECA-CW-LIBRARY-MANAGEMENT-SYSTEM/librasys-platform/config-server)** (`config-server`): Centralized application configuration.
2. **[Service Registry](file:///c:/IJSE/Enterprice%20Cloud%20Architecture/ECA-CW-LIBRARY-MANAGEMENT-SYSTEM/librasys-platform/service-registry)** (`service-registry`): Eureka Service Registry for service discovery.
3. **[API Gateway](file:///c:/IJSE/Enterprice%20Cloud%20Architecture/ECA-CW-LIBRARY-MANAGEMENT-SYSTEM/librasys-platform/api-gateway)** (`api-gateway`): Single entry point for routing client requests to backend microservices.

---

## 🛠️ Technology Stack
- **Java 25**
- **Spring Boot 4.1.1**
- **Spring Cloud 2025.1.2** (Config Server, Eureka Server, Spring Cloud Gateway)

---

## 🚀 Setup & Initialization with Submodules

```bash
# Clone parent repository with submodules
git clone --recursive <repository-url>

# Or update submodules if already cloned
git submodule update --init --recursive
```
