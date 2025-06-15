# Containers & ECS

## ECS Concepts

- Provides a platform to run Docker containers.
- Removes the need to manually manage container hosts.

### Container Host
- A machine that runs a container (e.g., Docker or containerd).
  - `containerd`: Software that runs a container.

### ECS handles:
- No need to:
  - Launch EC2 instances (container hosts)
  - Install Docker
  - Manage networking, scaling, etc.
- AWS manages all of the above.

### Modes
- **EC2 Mode**: Containers run on EC2 instances you manage.
- **Fargate Mode**: AWS manages the container hosts; you only define containers.

> 💡 *Fargate is ideal when you don’t want to manage infrastructure. It's serverless, meaning you pay only for the resources used.*

### Clusters
- Where your containers are run.
- Managed by ECS.
- You provide:
  - A container image
  - Instructions on how to run it.

### Container Images
- Stored in a container registry like Docker Hub or Amazon ECR (Elastic Container Registry).
- **ECR**:
  - AWS’s private registry
  - Integrated with IAM
  - Scalable and secure

---

## Task & Container Definitions

### Container Definition
- Defines a single container.
- Part of a task definition.
- Specifies:
  - Location of the container image
  - Port(s) to expose

### Task Definition
- Blueprint for how ECS should run containers.
- Includes:
  - One or more container definitions
  - Resources (CPU, memory)
  - Network mode
  - Compatibility (EC2 or Fargate)
  - Task Role (IAM role)

- Example tasks:
  - Single container (simple app)
  - Multiple containers (e.g., web app + database)

> 💡 *Think of a Task Definition as a recipe. ECS uses it to launch your containerized application.*

### Task Role
- IAM role assumed by each task.
- Provides temporary credentials to containers.
- Used to access other AWS services.

---

## ECS Tasks vs. Services

### ECS Task
- A single run of your app based on a task definition.
- Does **not** scale or self-recover.
- Ideal for demos or dev testing.

### ECS Service
- Long-running, scalable group of ECS tasks.
- Resilient and auto-healing.
- Deploys load balancers in front of tasks.
- Monitors health and restarts tasks automatically.

> ✅ *Use a service for production workloads; tasks alone for one-off jobs or testing.*

---

## ECS Cluster Modes

### EC2 Mode
- EC2 instances act as container hosts.
- ECS cluster is created inside your VPC.
- Architecture:
  - Benefits from multiple AZs.
  - Instances are managed with an Auto Scaling Group (ASG).
    - ASG provides horizontal scaling.
- You pay for EC2 instances regardless of container usage.
- Not serverless.
- Containers are pulled from registries.
- ECS runs tasks/services while you manage the host capacity.

> 🧠 *EC2 mode is more flexible but requires management. You can choose custom AMIs or GPU instances.*

### Fargate Mode
- **Serverless**:
  - AWS manages container host infrastructure.
  - You pay only for container resources used.

- Architecture:
  - Shared infrastructure (like EC2’s underlying hardware).
  - Tasks/services deployed into your VPC.
  - Each task gets an ENI (Elastic Network Interface) with an IP inside the VPC.
  - Can be accessed publicly if configured.

> ⚠️ *Default Fargate tasks are ephemeral. Storage must be explicitly configured (max 200 GiB).*

### When to Use
- **EC2**:
  - Existing container infrastructure
  - Cost-sensitive workloads (use spot/reserved instances)
- **Fargate**:
  - Minimal ops overhead
  - Burst or batch jobs

---

## Amazon Elastic Container Registry (ECR)

- Managed container image registry by AWS.
- Offers:
  - Public & Private registries
  - Enhanced scanning (via AWS Inspector)
  - CloudWatch metrics (auth attempts, push/pull)
  - CloudTrail logging of API actions
  - EventBridge support for event-driven workflows

> 💡 *Think of ECR as AWS’s version of Docker Hub, but with deeper security and IAM integration.*

---

## Kubernetes 101

- Open-source system for automating deployment, scaling, and management of containerized apps.
- **Cloud-agnostic**.

### Kubernetes Cluster
- A set of compute resources working as one unit.

#### Control Plane
- Manages cluster operations:
  - Scheduling
  - Deployment
  - Scaling
  - Health management

- Key components:
  - `kube-apiserver`: API entry point
  - `etcd`: Cluster state key-value store
  - `kube-scheduler`: Assigns pods to nodes
  - `cloud-controller-manager`: Cloud provider integration
  - `kube-controller-manager`: Node, job, and endpoint controllers

#### Pod
- Smallest deployable unit
- Contains 1+ containers
- Shared network/storage
- Ephemeral

#### Node
- Physical/virtual server running pods.
- Components:
  - `containerd`: Runs containers
  - `kubelet`: Talks to the control plane
  - `kube-proxy`: Manages networking

> 💡 *In most cases, one container = one pod, but pods can contain multiple containers.*

### Summary of Components

| Concept               | Description                                |
|-----------------------|--------------------------------------------|
| Cluster              | Complete Kubernetes deployment              |
| Control Plane        | Manages orchestration and scaling           |
| Node                 | Where containers actually run               |
| Pod                  | Smallest deployable unit                    |
| kubelet              | Talks to the control plane                  |
| kube-proxy           | Traffic routing within the cluster          |
| kube-apiserver       | Cluster's API interface                     |
| etcd                 | Stores cluster state                        |
| Job                  | One-off task until completion               |
| Service              | Abstracted access point to pods             |
| Ingress              | External traffic entry                      |
| Persistent Volume    | Long-term storage for pods                  |

---

## Amazon Elastic Kubernetes Service (EKS)

- AWS-managed Kubernetes service.
- Control plane is:
  - Fully managed
  - Highly available
  - Auto-scaling
  - Spread across AZs

- Integrates with:
  - ECR
  - ELB
  - IAM
  - VPC

### Node Options
- **Self-managed**: You provision EC2 manually.
- **Managed node groups**: AWS handles lifecycle.
- **Fargate**: Serverless, like ECS Fargate.

### Persistent Storage
- By default, pods are ephemeral.
- EKS supports persistent volumes via:
  - EBS
  - EFS
  - FSx

### Typical EKS Layout
- **AWS-managed VPC**: For the control plane
- **Customer VPC**: For worker nodes (EC2 or Fargate)
- **ENIs**: Connect control plane with worker nodes

> 🔍 *kubectl and aws-iam-authenticator (or AWS CLI v2) are required to interact with EKS clusters.*  
> ⚠️ *Fargate on EKS doesn’t support DaemonSets or privileged containers.*
