# **Production-Grade GitOps Driven Microservices Platform on AWS EKS**

<p align="center">
  <img src="https://img.shields.io/badge/AWS-Cloud-orange" alt="AWS" />
  <img src="https://img.shields.io/badge/Terraform-IaC-purple" alt="Terraform" />
  <img src="https://img.shields.io/badge/Kubernetes-EKS-blue" alt="Kubernetes" />
  <img src="https://img.shields.io/badge/GitOps-ArgoCD-red" alt="GitOps" />
  <img src="https://img.shields.io/badge/Monitoring-Prometheus-orange" alt="Prometheus" />
  <img src="https://img.shields.io/badge/Dashboard-Grafana-yellow" alt="Grafana" />
  <img src="https://img.shields.io/badge/DNS-Route53-green" alt="Route53" />
</p>

---

## **Project Overview**

This project demonstrates the deployment and operation of a **production-grade cloud-native microservices application on AWS using GitOps principles**.

The application is based on the **Online Boutique** microservices platform, which contains **11 independent services** communicating over **gRPC**. The focus of this project is not only application deployment, but also building a complete **DevOps platform** around it, including **Infrastructure as Code, Kubernetes, GitOps, DNS automation, TLS security, observability, monitoring, and cloud-native traffic management**.

The entire infrastructure was provisioned using **Terraform from a local WSL environment** and deployed on **Amazon EKS**. All operational work was performed from Linux command line environments to simulate a real DevOps workflow.

---

## **Online Boutique App**

Online Boutique is a microservices-based e-commerce demo application. It is designed to show how modern systems are split into small, independent services instead of one large monolithic application.

### **Microservices in the Application**

- **Frontend service** — serves the web application.
- **Cart service** — manages the shopping cart.
- **Product catalog service** — provides product data.
- **Currency service** — converts prices between currencies.
- **Payment service** — simulates payment processing.
- **Shipping service** — simulates shipping cost and delivery operations.
- **Email service** — sends order confirmation emails.
- **Checkout service** — orchestrates cart, payment, shipping, and email.
- **Recommendation service** — returns product recommendations.
- **Ad service** — serves contextual ads.
- **Load generator** — simulates user traffic.

### **How the Services Communicate**

The services communicate using:

- **gRPC**, for fast internal service-to-service communication,
- **HTTP**, for frontend traffic,
- and in a real-world design, services may also integrate with queues or APIs when needed.

---

## **Architecture**

<p align="center">
  <img src="docs/images/Architecture01.png" width="1000" alt="Architecture Overview" />
</p>

### **Service Breakdown**

| **Service** | **Language** | **Description** |
| --- | --- | --- |
| **frontend** | Go | Exposes an HTTP server to serve the website. It does not require signup/login and generates session IDs automatically. |
| **cartservice** | C# | Stores the items in the user's shopping cart in Redis and retrieves them. |
| **productcatalogservice** | Go | Provides the list of products from a JSON file, with search and product lookup support. |
| **currencyservice** | Node.js | Converts one money amount to another currency using live rates from the European Central Bank. |
| **paymentservice** | Node.js | Simulates charging a credit card and returns a transaction ID. |
| **shippingservice** | Go | Gives shipping cost estimates and simulates shipping operations. |
| **emailservice** | Python | Sends order confirmation emails in a mock workflow. |
| **checkoutservice** | Go | Retrieves the cart and orchestrates payment, shipping, and email notification flows. |
| **recommendationservice** | Python | Recommends products based on the cart contents. |
| **adservice** | Java | Provides contextual ads. |
| **loadgenerator** | Python/Locust | Continuously sends traffic to imitate realistic shopping flows. |

---

## **How Data Works in the Microservices**

Most services in this demo are stateless by design, which keeps the system simple to deploy and focus on platform engineering, observability, scaling, and networking.

### **Persistent Storage**

- **cartservice** uses **Redis** for cart persistence.
- Most other services are **stateless**.
- **checkoutservice** does not store orders in a real database.
- **productcatalogservice** reads product data from a JSON file.
- **paymentservice**, **shippingservice**, and **emailservice** are mock implementations.

### **Why This Design Matters**

This architecture makes the application ideal for DevOps and GitOps demonstrations because it clearly separates:

- service communication,
- deployment orchestration,
- traffic routing,
- storage,
- and observability.

---

## **Project Objectives**

- Provision AWS infrastructure using **Terraform**.
- Deploy a production-ready Kubernetes cluster using **Amazon EKS**.
- Implement GitOps deployment using **Argo CD**.
- Configure AWS Application Load Balancer using **Gateway API**.
- Automate DNS management using **ExternalDNS**.
- Secure application traffic using **ACM TLS certificates**.
- Implement centralized monitoring and alerting.
- Expose services using enterprise-grade traffic routing.
- Demonstrate cloud-native microservices operations.

---

## **High-Level Architecture**

```text
Developer
    │
    ▼
GitHub Repository
    │
    ▼
Terraform
    │
    ▼
AWS Infrastructure
    │
    ├── VPC
    ├── Public Subnets
    ├── Private Subnets
    ├── NAT Gateway
    └── Security Groups
    │
    ▼
Amazon EKS Cluster
    │
    ├── Worker Nodes
    ├── AWS Load Balancer Controller
    ├── Gateway API
    ├── ExternalDNS
    ├── Argo CD
    ├── Prometheus
    ├── Grafana
    └── Alertmanager
    │
    ▼
Online Boutique Microservices
    │
    ▼
AWS ALB
    │
    ▼
Route53
    │
    ▼
ACM Certificate
    │
    ▼
https://hudocafe.com
```

---

## **Infrastructure Provisioning**

Infrastructure was provisioned using **Terraform**.

Terraform was responsible for:

- VPC creation,
- Subnet creation,
- Internet Gateway,
- Route Tables,
- Security Groups,
- Bastion Host,
- EKS Cluster,
- Node Groups,
- IAM Roles.

### **Terraform Remote State**

Terraform remote state was stored in an **S3 backend bucket** with **versioning enabled**.

### **Why This Helps**

- State protection,
- Team collaboration,
- Disaster recovery,
- Infrastructure reproducibility.

---

## **Why Amazon EKS?**

Amazon EKS was chosen because it provides:

- Managed Kubernetes control plane,
- Automatic high availability,
- Native AWS integrations,
- IAM integration,
- Scalability,
- Enterprise-grade security.

Instead of managing Kubernetes masters manually, AWS manages the control plane while the worker nodes run the workloads.

---

## **Container Strategy**

Each microservice runs independently inside Kubernetes.

### **Benefits**

- Independent deployment,
- Independent scaling,
- Fault isolation,
- Technology flexibility,
- Better resource utilization.

The application contains services written in:

- Go,
- Python,
- Java,
- Node.js,
- C#.

All services communicate using **gRPC** for efficient internal communication.

---

## **GitOps Implementation**

GitOps was implemented using **Argo CD**.

### **Why GitOps?**

Traditional deployment flow:

```text
Developer → Jenkins → Kubernetes
```

GitOps deployment flow:

```text
Developer → Git Repository → Argo CD → Kubernetes
```

In GitOps, Git becomes the **single source of truth**.

### **Benefits**

- Version-controlled deployments,
- Rollback capability,
- Drift detection,
- Auditability,
- Reduced manual changes.

Whenever Kubernetes manifests are updated in GitHub, Argo CD automatically synchronizes the cluster.

---

## **Argo CD**

Argo CD continuously monitors Git repositories.

### **Responsibilities**

- Monitor manifests,
- Detect drift,
- Perform automatic synchronization,
- Support rollback,
- Provide deployment visibility.

### **Without Argo CD**

- Manual `kubectl apply`,
- Configuration drift,
- Less deployment visibility.

---

## **AWS Load Balancer Controller**

The AWS Load Balancer Controller bridges Kubernetes and AWS networking.

### **Responsibilities**

- Creates ALB automatically,
- Updates listeners,
- Updates target groups,
- Integrates Gateway API.

### **Without This Controller**

Load balancers would need to be created manually.

---

## **Gateway API**

Gateway API is the next-generation traffic management model for Kubernetes.

### **Benefits**

- Better routing model,
- More flexibility than Ingress,
- Role-based separation,
- Advanced traffic control.

It was used in this project to expose applications through AWS Application Load Balancer.

---

## **Service Exposure Models**

### **ClusterIP**

- Internal only,
- Accessible only within the Kubernetes cluster,
- Used for service-to-service communication.

### **NodePort**

- Exposes services on worker node ports,
- Can be accessed externally,
- Not ideal for production.

### **LoadBalancer**

- Creates an external AWS Load Balancer,
- Good for public access,
- AWS-managed,
- But usually one load balancer per service.

### **Ingress**

- Allows multiple services behind one load balancer,
- Helps with cost optimization and centralized routing.

### **Gateway API**

- Modern replacement for Ingress,
- More flexible and scalable,
- Better separation of concerns.

This project uses **Gateway API** instead of traditional Ingress.

---

## **DNS Automation**

**ExternalDNS** was installed using **Helm**.

### **Responsibilities**

- Watches Kubernetes resources,
- Creates Route53 records automatically,
- Updates DNS automatically.

### **Without ExternalDNS**

DNS records would need to be created manually in Route53.

---

## **SSL/TLS Security**

**AWS Certificate Manager (ACM)** was used to secure the application.

### **Responsibilities**

- TLS certificates,
- Automatic renewal,
- HTTPS encryption.

### **Domain**

- **hudocafe.com**

### **Benefits**

- Secure communication,
- Browser trust,
- No manual certificate maintenance.

---

## **Monitoring Stack**

The monitoring stack includes:

- **Prometheus**,
- **Grafana**,
- **Alertmanager**.

### **Prometheus**

Prometheus collects metrics from:

- Nodes,
- Pods,
- Services,
- Kubernetes components.

Common metrics:

- CPU,
- Memory,
- Network,
- Requests,
- Latency.

### **Grafana**

Grafana is used to visualize metrics and create dashboards for:

- Cluster health,
- Node utilization,
- Application performance,
- Resource consumption.

### **Alertmanager**

Alertmanager delivers alerts when conditions such as the following occur:

- High CPU,
- High memory,
- Pod failures,
- Node failures.

Slack notifications were configured for alert delivery.

---

## **Security Implementation**

Implemented security controls include:

- IAM Roles,
- IAM Policies,
- ACM Certificates,
- Private Networking,
- Route53 DNS Control,
- Kubernetes RBAC,
- TLS Encryption.

---

## **Key DevOps Skills Demonstrated**

- AWS,
- Terraform,
- Amazon EKS,
- Kubernetes,
- GitOps,
- Argo CD,
- Helm,
- Route53,
- ACM,
- ExternalDNS,
- Gateway API,
- AWS Load Balancer Controller,
- Prometheus,
- Grafana,
- Alertmanager,
- Linux,
- GitHub.

---

## **Project Learnings**

This project helped me understand:

- Cloud infrastructure provisioning,
- Kubernetes operations,
- GitOps workflows,
- DNS automation,
- TLS-based traffic security,
- Load balancing in AWS,
- Monitoring and alerting,
- Production-grade deployment practices.

---

## **Outcomes**

Successfully built a production-grade GitOps platform capable of:

- Automated infrastructure provisioning,
- Automated DNS management,
- Automated certificate management,
- Git-driven deployments,
- Centralized monitoring,
- Slack alerting,
- Cloud-native traffic routing,
- Kubernetes-based application hosting.

This project demonstrates real-world **DevOps**, **Platform Engineering**, **Kubernetes Operations**, **GitOps**, **Cloud Infrastructure Automation**, and **Production Monitoring** practices.

---

## **Screenshots**

### **AWS CLI and Access Configuration**

<p align="center">
  <img src="https://github.com/user-attachments/assets/4ad0f9af-6570-4b1f-901e-ef3e8c1e8671" width="48%" alt="AWS CLI access key config" />
  <img src="https://github.com/user-attachments/assets/66547052-9e57-441c-951e-38bd4b4aedac" width="48%" alt="kubectl nodes done" />
</p>

### **Bastion, Load Balancer Controller, and Gateway Setup**

<p align="center">
  <img src="https://github.com/user-attachments/assets/91298a70-e822-4cc9-8538-6bba63d7f64f" width="48%" alt="baston server policy" />
  <img src="https://github.com/user-attachments/assets/f751fd07-7e88-4d6e-bc8c-17a160b5fdc7" width="48%" alt="kubectl load balancer controller" />
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/a7e209dd-f8b1-4c3a-8eee-a3dae51b8fa1" width="48%" alt="controller lb installed" />
  <img src="https://github.com/user-attachments/assets/897c0c68-a3f4-469b-be62-791535991528" width="48%" alt="policy json created" />
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/4a68cd1e-7447-412d-9850-7ed801a1de2a" width="48%" alt="kubectl gateway class" />
  <img src="https://github.com/user-attachments/assets/1e055860-1510-4572-b332-4fa8389ed99e" width="48%" alt="hudocafe domain argocd" />
</p>

### **CI/CD and Argo CD Workflow**

<p align="center">
  <img src="https://github.com/user-attachments/assets/6c50382c-13a1-4dd8-b927-45455ed3dc84" width="48%" alt="src edit pipeline run action" />
  <img width="48%" alt="pipeline run succesfufl" src="https://github.com/user-attachments/assets/28bd68cd-ca10-46aa-bc03-d9899eb35727" />
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/a25aea0b-8231-45fd-9228-5c40bc8f2299" width="48%" alt="argocd with code yaml kubectl" />
  <img src="https://github.com/user-attachments/assets/d5707d42-6d31-491c-80da-79fd55ad9535" width="48%" alt="done trigger" />
</p>

### **Domain, Slack, Grafana, and Prometheus**

<p align="center">
  <img src="https://github.com/user-attachments/assets/db74581f-83fd-41ab-953c-74dd69a1d9e1" width="48%" alt="hudocafe domain deploy" />
  <img src="https://github.com/user-attachments/assets/4b94c77d-4df4-42a2-856e-c4b3ead99ac6" width="48%" alt="alert on slack" />
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/1b693055-b309-4c9d-a114-26317ba9c634" width="48%" alt="graphana" />
  <img src="https://github.com/user-attachments/assets/0e8c2c46-8f90-4404-978f-a0490ffa26b1" width="48%" alt="grafana login" />
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/5dd0706b-a893-4244-905e-74683a1abf84" width="48%" alt="slack screenshot" />
  <img src="https://github.com/user-attachments/assets/b2b58b24-e0a9-48b7-8604-1db196a2b90b" width="48%" alt="prometheus metrics" />
</p>

### **Argo CD and Final Architecture**

<p align="center">
  <img src="https://github.com/user-attachments/assets/974ac573-fc0d-4d26-b576-6d38cd56bb8a" width="48%" alt="argocd loc" />
  <img src="https://github.com/user-attachments/assets/60e9e9dc-6bf1-46e4-ae92-2ddbc11e23f7" width="48%" alt="argocd details" />
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/fb02862a-2518-4005-ae9c-20ed00f5a599" width="1000" alt="Gitops Project architecture" />
</p>

---

## **Final Note**

This repository represents a complete **real-world DevOps and GitOps journey**, from infrastructure provisioning to application delivery and observability. It is a strong showcase of cloud, Kubernetes, automation, and production operations skills.

