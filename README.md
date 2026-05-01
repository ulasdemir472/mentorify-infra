# Mentorify Infrastructure & GitOps 🚀

This repository contains the **Kubernetes Infrastructure**, **GitOps Workflows**, and **Security Configurations** for the Mentorify platform.

## 🏗️ System Architecture

The entire platform is built with a **Zero Trust** mindset, leveraging Cloudflare Tunnels and Kubernetes-native security patterns.

```mermaid
graph TD
    subgraph "Public Internet"
        User[("👤 User / Browser")]
    end

    subgraph "Cloudflare Edge (Zero Trust)"
        CF_Edge{"🛡️ Cloudflare Edge<br/>(WAF, DDoS Protection)"}
        CF_Tunnel["🚇 Cloudflare Tunnel"]
    end

    subgraph "K3s Cluster (Ubuntu VM)"
        direction TB
        
        subgraph "Ingress Layer"
            Traefik["🔌 Traefik Ingress Controller"]
        end

        subgraph "Mentorify Application"
            Frontend["🌐 Frontend Pod<br/>(Next.js Standalone)"]
            Backend["⚙️ Backend Pod<br/>(Express.js API)"]
            MongoDB[("💾 MongoDB Pod<br/>(StatefulSet)")]
            PVC[("📂 Persistent Volume<br/>(User Uploads)")]
        end

        subgraph "DevOps & Security"
            ArgoCD["🐙 ArgoCD (GitOps)"]
            SealedSecrets["🔐 Sealed Secrets"]
            NetPol["🛡️ Network Policies"]
        end
    end

    User -- HTTPS --> CF_Edge
    CF_Edge -- Encrypted Tunnel --> CF_Tunnel
    CF_Tunnel -- Host: mentorify.devlermedya.net --> Traefik
    
    Traefik -- "/" --> Frontend
    Traefik -- "/api/v1" --> Backend
    Traefik -- "/socket.io" --> Backend
    
    Frontend -- "API Proxy" --> Backend
    Backend -- "Internal DNS" --> MongoDB
    Backend -- "Volume Mount" --> PVC
    
    ArgoCD -- "Sync State" --> Mentorify
```

## 🔐 Security Features

### 1. Zero Trust Access
*   **No Exposed Ports:** The VM has **zero** public ports open. All traffic flows through an outbound Cloudflare Tunnel.
*   **Identity-Aware Access:** Admin panels (ArgoCD, Grafana) are protected by Cloudflare Access, requiring GitHub/Google authentication.

### 2. GitOps & Secrets Management
*   **ArgoCD:** Implements a declarative GitOps flow. The cluster state is automatically synced with this repository.
*   **Bitnami Sealed Secrets:** All sensitive data (DB passwords, API keys) are encrypted using asymmetric encryption. Only the cluster's private key can decrypt them. **It is safe to store these encrypted secrets in this public repository.**

### 3. Network Isolation
*   **Pod Segmentation:** Fine-grained **NetworkPolicies** ensure that only the Frontend can talk to the Backend, and only the Backend can talk to MongoDB.
*   **Namespace Isolation:** The application is logically separated from the system and monitoring components.

## 🛠️ Tech Stack
*   **Orchestration:** k3s (Lightweight Kubernetes)
*   **Ingress:** Traefik
*   **GitOps:** ArgoCD
*   **Secrets:** Sealed Secrets
*   **Monitoring:** Prometheus & Grafana
*   **Networking:** Cloudflare Zero Trust (Tunnel & Access)
*   **Service Mesh:** Istio (Optional/Partial)

---
*Built with ❤️ by [Your Name]*
