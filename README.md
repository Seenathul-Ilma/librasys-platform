# LibraSys Platform/Infrastructure - Enterprise Cloud Architecture 📚

**Student Name:** Seenathul Ilma  
**Student Number:** 241711005  
**Slack Handle:** Seenathul Ilma Musawwir  
**GCP Project ID:** `librasys-eca`

---

## 📖 Overview

`librasys-platform` is the **infrastructure / platform layer** of the LibraSys Library Management System — a polyrepo, cloud-native microservices application built for the Enterprise Cloud Architecture (ECA) coursework.

This super-repo bundles the three Spring Cloud platform components that every business microservice depends on to boot, discover each other, and be reached from the outside world:

| Submodule | Purpose | Port |
|---|---|---|
| [`config-server`](https://github.com/Seenathul-Ilma/librasys-config-server) | Centralized configuration for all services | `8888` |
| [`service-registry`](https://github.com/Seenathul-Ilma/librasys-service-registry) | Service discovery (Netflix Eureka) | `8761` |
| [`api-gateway`](https://github.com/Seenathul-Ilma/librasys-api-gateway) | Single entry point / dynamic routing | `8080` |

The business logic (users, books, loans) lives in the sibling repo **[`librasys-services`](https://github.com/Seenathul-Ilma/librasys-services)**, and the client UI lives in **[`librasys-frontend`](https://github.com/Seenathul-Ilma/librasys-frontend)**.

---

## 🏗️ Architecture Breakdown

LibraSys follows a **Spring Cloud microservices pattern**: every request from the browser hits the API Gateway first, which asks Eureka where each downstream service currently lives, then forwards the request. All services pull their runtime configuration (DB URLs, bucket names, ports) from the Config Server at boot, instead of hard-coding it.

```
                                Google Cloud Platform (librasys-eca)
                                            │
   Browser ──HTTPS──▶  Cloud Run (librasys-frontend)
                                            │
                                            ▼
                     Global External HTTP(S) Load Balancer
                                            │
                                            ▼
              Managed Instance Group (autoscaling, 2 zones, min 1 / max 3)
      ┌─────────────────────────────────────────────────────────────────┐
      │  Debian 12 VM — Nginx (80/443) ─▶ PM2 process manager            │
      │      ├─ config-server     (8888)  ◀── this repo                 │
      │      ├─ service-registry  (8761)  ◀── this repo                 │
      │      ├─ api-gateway       (8080)  ◀── this repo                 │
      │      ├─ user-service      (8081)  ── librasys-services          │
      │      ├─ book-service      (8082)  ── librasys-services          │
      │      └─ loan-service      (8083)  ── librasys-services          │
      │  MongoDB (27017, local to VM, used by book-service)              │
      └─────────────────────────────────────────────────────────────────┘
                     │                                   │
                     ▼                                   ▼
        Cloud SQL (MySQL 8.0)                 Cloud Storage + Firestore
        librasys-mysql-db                     librasys-book-covers
        (user/loan DBs)                       loan-audit-log
```

**Boot order matters:** `config-server` → `service-registry` → `api-gateway` → business services. PM2's `ecosystem.config.js` starts them in this order with `autorestart: true` so any crashed service self-heals.

---

## 🧰 Tech Stack

| Component | Type | Technology | Notes |
|---|---|---|---|
| Config Server | Platform | Spring Cloud Config, Java 25 | Native/Git-backed property source |
| Service Registry | Platform | Spring Cloud Netflix Eureka | Standalone, in-memory registry |
| API Gateway | Platform | Spring Cloud Gateway | Dynamic routing via Eureka |
| Build tool | — | Maven | `mvn clean package -DskipTests` |
| Process manager | — | PM2 | Runs all platform + business jars |
| Reverse proxy | — | Nginx | Terminates 80/443 on the VM |
| Compute | GCP | Managed Instance Group (Debian 12) | Autoscaling, 2 zones |
| Networking | GCP | Global HTTP(S) Load Balancer, Cloud DNS, Cloud NAT | |

---

## ⚙️ Setup & Local Run

### Clone with submodules
```bash
git clone --recurse-submodules https://github.com/Seenathul-Ilma/librasys-platform.git
cd librasys-platform
```
If already cloned without submodules:
```bash
git submodule update --init --recursive
```

### Build (in order)
```bash
cd config-server     && mvn clean package -DskipTests && cd ..
cd service-registry   && mvn clean package -DskipTests && cd ..
cd api-gateway        && mvn clean package -DskipTests && cd ..
```

### Run locally (in order, separate terminals)
```bash
java -jar config-server/target/config-server-*.jar     # http://localhost:8888
java -jar service-registry/target/service-registry-*.jar  # http://localhost:8761
java -jar api-gateway/target/api-gateway-*.jar          # http://localhost:8080
```

Verify:
- Config Server: `curl http://localhost:8888/user-service/default`
- Eureka Dashboard: `http://localhost:8761`
- Gateway health: `curl http://localhost:8080/actuator/health`

---

## ☁️ Live Deployment

| Item | Value |
|---|---|
| GCP Project | `librasys-eca` (`us-central1` / `us-central1-a`) |
| Backend entry point | `https://librasys-eca.duckdns.org` (Load Balancer → MIG) |
| Frontend (Cloud Run) | https://librasys-frontend-529440660281.us-central1.run.app |
| Process manager | PM2, 6 services, `pm2 list` |
| Autoscaling | Managed Instance Group, min 1 / max 3, 60% CPU target |

---

## 🔗 Related Repositories
- Business services: https://github.com/Seenathul-Ilma/librasys-services
- Frontend: https://github.com/Seenathul-Ilma/librasys-frontend