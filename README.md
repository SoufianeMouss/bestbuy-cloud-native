# Lab Project: Cloud-Native App for Best Buy

## Updated Architecture Diagram
```
flowchart LR
  %% External users
  U[Customer] -->|HTTP| SF_LB[Store-Front\nService: LoadBalancer]
  A[Admin/Employee] -->|HTTP| SA_LB[Store-Admin\nService: LoadBalancer]

  %% Frontends -> internal services
  SF_LB --> SF[Store-Front\nVue + Nginx]
  SA_LB --> SA[Store-Admin\nVue + Nginx]

  SF -->|/products| PS[Product-Service API]
  SF -->|/order| OS[Order-Service API]

  SA -->|/makeline/*| ML[Makeline-Service API]
  SA -->|/products| PS

  %% Async order processing
  OS -->|Publish orders| RMQ[RabbitMQ\nStatefulSet]
  ML -->|Consume orders| RMQ

  %% Persistence
  ML -->|Insert or Update orders| MDB[MongoDB\nStatefulSet]

  %% CI/CD
  subgraph CICD[CI/CD - GitHub Actions]
    GH[Push to GitHub] --> DH[Build and Push to Docker Hub\nTag: commit SHA and latest]
    DH --> AKS[Update AKS Deployment\nkubectl set image and rollout]
  end

```
Perfect — here’s **README-ready content** you can paste directly.
It’s **concise**, **clear**, and **aligned with what you actually built** (BestBuy microservices on AKS + CI/CD).

---

## Brief Application Explanation

This project is a **cloud-native microservices retail application** deployed on **Azure Kubernetes Service (AKS)**. It simulates a BestBuy-style online store with separate **customer** and **admin** interfaces. The **Store-Front** allows customers to browse products and place orders, while the **Store-Admin** enables employees to manage and process orders.

The backend is composed of independent microservices. The **Product Service** manages product data, the **Order Service** receives customer orders, and the **Makeline Service** processes orders asynchronously. Orders are published to **RabbitMQ**, enabling loose coupling and scalability, and are persisted in **MongoDB** for reliability and fault tolerance.

All services are containerized with **Docker** and orchestrated using **Kubernetes**. A **GitHub Actions CI/CD pipeline** automatically builds images, pushes them to **Docker Hub**, and deploys updates to AKS using immutable image tags, demonstrating modern cloud-native design and DevOps best practices.

---

## Deployment Instructions

### Prerequisites

* Azure account with an existing **AKS cluster**
* `kubectl` configured for the cluster
* Docker Hub account
* GitHub repository with Actions enabled

---

### Configure Kubernetes Access

```bash
az aks get-credentials \
  --resource-group <resource-group> \
  --name <aks-cluster-name>
```

---

### Deploy Infrastructure Components

```bash
kubectl create namespace bestbuy
kubectl apply -f k8s/rabbitmq.yaml -n bestbuy
kubectl apply -f k8s/mongodb.yaml -n bestbuy
```

---

### Deploy Application Services

```bash
kubectl apply -f k8s/product-service.yaml -n bestbuy
kubectl apply -f k8s/order-service.yaml -n bestbuy
kubectl apply -f k8s/makeline-service.yaml -n bestbuy
kubectl apply -f k8s/store-front.yaml -n bestbuy
kubectl apply -f k8s/store-admin.yaml -n bestbuy
```

---

### CI/CD Deployment (Recommended)

Once configured, **any push to the `main` branch** triggers:

* Docker image build
* Push to Docker Hub
* Automatic deployment to AKS

No manual redeployment is required.

---

### Verify Deployment

```bash
kubectl get pods -n bestbuy
kubectl get svc -n bestbuy
```

---

## 🔗 Links Table

| Service                            | GitHub Repository                                                                            | Docker Hub Image                                                                                                     |
| ---------------------------------- | -------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| Store-Front                        | [https://github.com/soufianemouss/store-front-l8](https://github.com/soufianemouss/store-front-l8)           | [https://hub.docker.com/r/soufianealgonquin/store-front-l8](https://hub.docker.com/r/soufianealgonquin/store-fron-final)           |
| Store-Admin                        | [https://github.com/soufianemouss/store-admin-l8](https://github.com/soufianemouss/store-admin-l8)           | [https://hub.docker.com/r/soufianealgonquin/store-admin-l8](https://hub.docker.com/r/soufianealgonquin/store-admin-final)           |
| Product Service                    | [https://github.com/soufianemouss/product-service-l8](https://github.com/soufianemouss/product-service-l8)   | [https://hub.docker.com/r/soufianealgonquin/product-service-l8](https://hub.docker.com/r/soufianealgonquin/product-service-final)   |
| Order Service                      | [https://github.com/soufianemouss/order-service-l8](https://github.com/soufianemouss/order-service-l8)       | [https://hub.docker.com/r/soufianealgonquin/order-service-l8](https://hub.docker.com/r/soufianealgonquin/order-service-final)       |
| Makeline Service                   | [https://github.com/soufianemouss/makeline-service-l8](https://github.com/soufianemouss/makeline-service-l8) | [https://hub.docker.com/r/soufianealgonquin/makeline-service-l8](https://hub.docker.com/r/soufianealgonquin/makeline-service-final) |

---

## Demo Video
[Demo Video](https://www.awesomescreenshot.com/video/47377343?key=7db93d385e257dcfbf2c0c7c8abc1e19)
