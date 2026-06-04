# Project Introduction

# Intro to Online Boutique App

This is a type of e-commerce platform, but unlike Amazon-type stores, it focuses on:

- **Niche or curated products**
- **Unique / limited collections**
- **Strong brand identity & style**

Think of it as a **digital version of a small, stylish fashion store**.

But from a **technical perspective**, modern boutique apps are **not built as a single application**.

They are built using **Microservices Architecture**.

># Online Boutique = Microservices in Action
>
>This online boutique app is made up of multiple services like:
>
>### 🧾 Product Catalog Service
>
>- Manages product list, categories, pricing
>
>### 🛒 Cart Service
>
>- Handles user cart (add/remove items)
>
>### 💳 Payment Service
>
>- Processes payments (UPI, cards)
>
>### 📦 Order Service
>
>- Manages order lifecycle
>
>### 👤 Frontend Service
>
>- Authentication & profiles
>
>### 🚚 Shipping Service
>
>- Delivery tracking & logistics
>
>### Etc..
>
>---
>
># How These Services Communicate
>
>- REST APIs (HTTP)
>- gRPC (faster internal communication)
>- Message queues (Kafka / RabbitMQ)
>
>👉 Example:
>
>- Cart service → calls Product service
>- Order service → calls Payment service


# **Architecture**

**Online Boutique** is composed of 11 microservices written in different languages that talk to each other over gRPC.

![image.png](docs/images/Architecture01.png)

| **Service** | **Language** | **Description** |
| --- | --- | --- |
| [frontend](https://github.com/laxmikantagiri/Production-Grade_GitOps-Driven_Microservices-Demo/blob/main/src/frontend) | Go | Exposes an HTTP server to serve the website. Does not require signup/login and generates session IDs for all users automatically. |
| [cartservice](https://github.com/laxmikantagiri/Production-Grade_GitOps-Driven_Microservices-Demo/blob/main/src/cartservice) | C# | Stores the items in the user's shopping cart in Redis and retrieves it. |
| [productcatalogservice](https://github.com/laxmikantagiri/Production-Grade_GitOps-Driven_Microservices-Demo/blob/main/src/productcatalogservice) | Go | Provides the list of products from a JSON file and ability to search products and get individual products. |
| [currencyservice](https://github.com/laxmikantagiri/Production-Grade_GitOps-Driven_Microservices-Demo/blob/main/src/currencyservice) | Node.js | Converts one money amount to another currency. Uses real values fetched from European Central Bank. It's the highest QPS service. |
| [paymentservice](https://github.com/laxmikantagiri/Production-Grade_GitOps-Driven_Microservices-Demo/blob/main/src/paymentservice) | Node.js | Charges the given credit card info (mock) with the given amount and returns a transaction ID. |
| [shippingservice](https://github.com/laxmikantagiri/Production-Grade_GitOps-Driven_Microservices-Demo/blob/main/src/shippingservice) | Go | Gives shipping cost estimates based on the shopping cart. Ships items to the given address (mock) |
| [emailservice](https://github.com/laxmikantagiri/Production-Grade_GitOps-Driven_Microservices-Demo/blob/main/src/emailservice) | Python | Sends users an order confirmation email (mock). |
| [checkoutservice](https://github.com/laxmikantagiri/Production-Grade_GitOps-Driven_Microservices-Demo/blob/main/src/checkoutservice) | Go | Retrieves user cart, prepares order and orchestrates the payment, shipping and the email notification. |
| [recommendationservice](https://github.com/laxmikantagiri/Production-Grade_GitOps-Driven_Microservices-Demo/blob/main/src/recommendationservice) | Python | Recommends other products based on what's given in the cart. |
| [adservice](https://github.com/laxmikantagiri/Production-Grade_GitOps-Driven_Microservices-Demo/blob/main/src/adservice) | Java | Provides text ads based on given context words. |
| [loadgenerator](https://github.com/laxmikantagiri/Production-Grade_GitOps-Driven_Microservices-Demo/blob/main/src/loadgenerator) | Python/Locust | Continuously sends requests imitating realistic user shopping flows to the frontend. |

Screenshots: hudocafe.com
<img width="1195" height="678" alt="19 hudocafe domain deploy" src="https://github.com/user-attachments/assets/b612716d-52f3-4774-93b3-cfcb6ff24e40" />

---

**Most services are stateless**, and **only the cart uses persistence (Redis)**. Let’s break it down cleanly.

# How data works in `microservices'

This project is **designed** to:

- Demonstrate **microservice communication**
- Be **easy to deploy anywhere**
- Avoid complex database ops

So it uses **minimal persistence** on purpose.

---

## Service-by-Service Data Breakdown

### ✅ **cartservice** → ✔️ HAS persistence

**Storage used:**

- **Redis**

**What’s stored:**

- User cart items
- Quantity, product IDs

**Why Redis?**

- Fast
- Simple
- Easy to reset
- No schema complexity

📌 In Kubernetes:

- Redis runs as a pod (or StatefulSet)
- Cart data is lost if Redis is deleted (by default)

---

### ❌ **orders / checkout** → NO real database

There is **NO dedicated “orders database”**.

**checkoutservice:**

- Aggregates data from:
    - cartservice
    - paymentservice
    - shippingservice
    - emailservice
- Simulates order placement
- Does **not persist orders**

👉 This is **by design**, to keep the demo lightweight.

---

### ❌ **productcatalogservice**

**Storage:**

- Static JSON file
- Loaded into memory at startup

**No DB**

- Products reset on restart

---

### ❌ **recommendationservice**

**Storage:**

- Stateless
- Generates recommendations dynamically

---

### ❌ **paymentservice**

**Storage:**

- None
- Fake payment processor

---

## SUMMARY TABLE

| Service | Persistent Storage | Type |
| --- | --- | --- |
| cartservice | ✅ Yes | Redis |
| checkoutservice | ❌ No | Stateless |
| productcatalogservice | ❌ No | In-memory JSON |
| recommendationservice | ❌ No | Stateless |
| paymentservice | ❌ No | Fake |
| shippingservice | ❌ No | Fake |
| emailservice | ❌ No | Fake |
| adservice | ❌ No | In-memory |
| frontend | ❌ No | Stateless |
| currencyservice | ❌ No | In-memory |
| loadgenerator | ❌ No | Stateless |

---

> “The demo intentionally keeps most services stateless to simplify deployment and focus on platform concerns like CI/CD, observability, scaling, and networking.”
> 

---

# Project Architecture

![Gitops Project.gif](docs/images/Gitops_Project.gif)

![Gitops Project.drawio.png](docs/images/Gitops_Project.png)
---
---
---
---


Project Overview

This project demonstrates the deployment and operation of a production-grade cloud-native microservices application on AWS using GitOps principles.

The application is based on Google's Online Boutique microservices platform consisting of 11 independent services communicating through gRPC. The primary goal of this project was not application development, but building a complete DevOps platform around the application including Infrastructure as Code, Kubernetes, GitOps, DNS automation, TLS security, observability, and cloud-native traffic management.

The entire infrastructure was provisioned using Terraform from a local WSL environment and deployed onto Amazon EKS. All operational activities were performed from Linux command line environments to simulate real-world DevOps workflows.

Objectives
Provision AWS infrastructure using Terraform
Deploy a production-ready Kubernetes cluster using Amazon EKS
Implement GitOps deployment strategy using Argo CD
Configure AWS Application Load Balancer using Gateway API
Automate DNS management using ExternalDNS
Secure application traffic using ACM TLS certificates
Implement centralized monitoring and alerting
Expose services through enterprise-grade traffic routing
Demonstrate cloud-native microservices operations
High-Level Architecture

Developer
↓
GitHub Repository
↓
Terraform
↓
AWS Infrastructure

VPC
├── Public Subnets
├── Private Subnets
├── NAT Gateway
└── Security Groups

↓
Amazon EKS Cluster

├── Worker Nodes
├── AWS Load Balancer Controller
├── Gateway API
├── ExternalDNS
├── Argo CD
├── Prometheus
├── Grafana
└── Alertmanager

↓
Online Boutique Microservices

↓
AWS ALB

↓
Route53

↓
ACM Certificate

↓
hudocafe.com

Infrastructure Provisioning

Infrastructure was provisioned using Terraform.

Terraform was responsible for:

VPC creation
Subnets
Internet Gateway
Route Tables
Security Groups
Bastion Host
EKS Cluster
Node Groups
IAM Roles

Terraform remote state was stored inside an S3 backend bucket with versioning enabled.

Benefits:

State protection
Team collaboration
Disaster recovery
Infrastructure reproducibility
Why Amazon EKS?

Amazon EKS was selected because:

Managed Kubernetes Control Plane
Automatic high availability
Native AWS integrations
IAM integration
Scalability
Enterprise-grade security

Instead of manually managing Kubernetes masters, AWS manages the control plane while worker nodes run application workloads.

Container Strategy

Each microservice runs independently inside Kubernetes.

Benefits:

Independent deployment
Independent scaling
Fault isolation
Technology flexibility
Better resource utilization

The application contains services written in:

Go
Python
Java
Node.js
C#

All services communicate using gRPC for efficient internal service-to-service communication.

GitOps Implementation

GitOps was implemented using Argo CD.

Why GitOps?

Traditional deployment:

Developer → Jenkins → Kubernetes

GitOps deployment:

Developer → Git Repository → Argo CD → Kubernetes

Git becomes the single source of truth.

Benefits:

Version controlled deployments
Rollback capability
Drift detection
Auditability
Reduced manual changes

Whenever manifests are updated in GitHub, Argo CD automatically synchronizes the Kubernetes cluster.

Argo CD

Argo CD continuously monitors Git repositories.

Responsibilities:

Monitor manifests
Detect drift
Automatic synchronization
Rollback support
Deployment visibility

Without Argo CD:

Manual kubectl apply
Configuration drift
Less deployment visibility
AWS Load Balancer Controller

The AWS Load Balancer Controller bridges Kubernetes and AWS networking.

Responsibilities:

Creates ALB automatically
Updates listeners
Updates target groups
Integrates Gateway API

Without this controller:

Load Balancers must be created manually
Gateway API

Gateway API is the next-generation traffic management model for Kubernetes.

Benefits:

Better routing model
More flexibility than Ingress
Role-based separation
Advanced traffic control

Used in this project to expose applications through AWS Application Load Balancer.

Service Exposure Models
ClusterIP

Internal only.

Accessible only within Kubernetes cluster.

Use case:
Service-to-service communication.

NodePort

Exposes service on worker node ports.

Example:

NodeIP:30080

Limitations:

Not production friendly
Manual management
LoadBalancer

Creates external AWS Load Balancer.

Benefits:

Public access
AWS managed

Drawback:

One Load Balancer per service
Ingress

Allows multiple services behind one Load Balancer.

Benefits:

Cost optimization
Centralized routing
Gateway API

Modern replacement for Ingress.

Benefits:

More scalable
More flexible
Better separation of concerns

This project uses Gateway API instead of traditional Ingress.

DNS Automation

ExternalDNS was installed using Helm.

Responsibilities:

Watch Kubernetes resources
Create Route53 records automatically
Update DNS automatically

Without ExternalDNS:

Every DNS record must be manually created.

SSL/TLS Security

AWS Certificate Manager (ACM) was used.

Responsibilities:

TLS certificates
Automatic renewal
HTTPS encryption

Domain:

hudocafe.com

Benefits:

Secure communication
Browser trust
No certificate maintenance
Monitoring Stack

Prometheus
Grafana
Alertmanager

Prometheus

Collects metrics from:

Nodes
Pods
Services
Kubernetes components

Metrics:

CPU
Memory
Network
Requests
Latency
Grafana

Visualization platform.

Used to create dashboards for:

Cluster health
Node utilization
Application performance
Resource consumption
Alertmanager

Responsible for alert delivery.

Alerts are sent to Slack when:

High CPU
High Memory
Pod failures
Node failures
Security Implementation

Implemented security controls:

IAM Roles
IAM Policies
ACM Certificates
Private Networking
Route53 DNS Control
Kubernetes RBAC
TLS Encryption
Key DevOps Skills Demonstrated
AWS
Terraform
Amazon EKS
Kubernetes
GitOps
Argo CD
Helm
Route53
ACM
ExternalDNS
Gateway API
AWS Load Balancer Controller
Prometheus
Grafana
Alertmanager
Linux
GitHub
Outcomes

Successfully built a production-grade GitOps platform capable of:

Automated infrastructure provisioning
Automated DNS management
Automated certificate management
Git-driven deployments
Centralized monitoring
Slack alerting
Cloud-native traffic routing
Kubernetes-based application hosting

This project demonstrates real-world DevOps, Platform Engineering, Kubernetes Operations, GitOps, Cloud Infrastructure Automation, and Production Monitoring practices.










                                        End
---
---









<img width="1262" height="725" alt="aws cli access key config" src="https://github.com/user-attachments/assets/4ad0f9af-6570-4b1f-901e-ef3e8c1e8671" />


<img width="1171" height="298" alt="kubectl nodes done" src="https://github.com/user-attachments/assets/66547052-9e57-441c-951e-38bd4b4aedac" />




<img width="1259" height="586" alt="baston server policy" src="https://github.com/user-attachments/assets/91298a70-e822-4cc9-8538-6bba63d7f64f" />



<img width="1267" height="481" alt="kubectl load balancer controller" src="https://github.com/user-attachments/assets/f751fd07-7e88-4d6e-bc8c-17a160b5fdc7" />

<img width="1167" height="564" alt="controller lb instaled 5" src="https://github.com/user-attachments/assets/a7e209dd-f8b1-4c3a-8eee-a3dae51b8fa1" />



<img width="1274" height="384" alt="8policy json creat" src="https://github.com/user-attachments/assets/897c0c68-a3f4-469b-be62-791535991528" />
<img width="1264" height="285" alt="6 kubectl gateway clas" src="https://github.com/user-attachments/assets/4a68cd1e-7447-412d-9850-7ed801a1de2a" />



<img width="1196" height="673" alt="13 hudocafe domain argocd" src="https://github.com/user-attachments/assets/1e055860-1510-4572-b332-4fa8389ed99e" />


<img width="1231" height="663" alt="15 src edit pipeline run action" src="https://github.com/user-attachments/assets/6c50382c-13a1-4dd8-b927-45455ed3dc84" />


<img width="1249" height="605" alt="16 pipeline run succesfufl" src="https://github.com/user-attachments/assets/983ea175-866e-4de5-ad92-6aba739ef2c5" />




<img width="1234" height="674" alt="argocd with code yaml kubectl" src="https://github.com/user-attachments/assets/a25aea0b-8231-45fd-9228-5c40bc8f2299" />



<img width="1195" height="678" alt="19 hudocafe domain deploy" src="https://github.com/user-attachments/assets/db74581f-83fd-41ab-953c-74dd69a1d9e1" />



<img width="1190" height="687" alt="22 pic" src="https://github.com/user-attachments/assets/53fe3e8e-2aeb-4944-ad89-47138ca4b21c" />




<img width="1241" height="672" alt="done trigger" src="https://github.com/user-attachments/assets/d5707d42-6d31-491c-80da-79fd55ad9535" />




<img width="1271" height="635" alt="29 alert on slack" src="https://github.com/user-attachments/assets/4b94c77d-4df4-42a2-856e-c4b3ead99ac6" />
<img width="1265" height="609" alt="27 fdsf" src="https://github.com/user-attachments/assets/1bd785d7-67db-4097-aca3-bd0615fd1944" />




<img width="1277" height="575" alt="30 deply slac" src="https://github.com/user-attachments/assets/0d5f8d6b-3a81-430a-893e-06db1aff1f27" />



<img width="1268" height="611" alt="31 graphana" src="https://github.com/user-attachments/assets/1b693055-b309-4c9d-a114-26317ba9c634" />
<img width="1198" height="680" alt="30 grafan login" src="https://github.com/user-attachments/assets/0e8c2c46-8f90-4404-978f-a0490ffa26b1" />



<img width="1268" height="637" alt="slack ss" src="https://github.com/user-attachments/assets/5dd0706b-a893-4244-905e-74683a1abf84" />
<img width="1197" height="686" alt="35 promethus matrics" src="https://github.com/user-attachments/assets/b2b58b24-e0a9-48b7-8604-1db196a2b90b" />




<img width="1110" height="701" alt="gtrsph" src="https://github.com/user-attachments/assets/f6f008dc-8dd7-4a51-bd9a-ce5113d67bda" />











<img width="1219" height="663" alt="argocd loc" src="https://github.com/user-attachments/assets/974ac573-fc0d-4d26-b576-6d38cd56bb8a" />




<img width="1197" height="653" alt="detaisl argoc d" src="https://github.com/user-attachments/assets/60e9e9dc-6bf1-46e4-ae92-2ddbc11e23f7" />



<img width="2517" height="1505" alt="Gitops_Project architecture" src="https://github.com/user-attachments/assets/fb02862a-2518-4005-ae9c-20ed00f5a599" />


<img width="1250" height="681" alt="gith us" src="https://github.com/user-attachments/assets/436f483a-5c15-4d6f-80fb-604fbd3182ca" />

