# Kubernetes — Introduction

> **Source:** Day-30 | Kubernetes is Easy | Introduction to Kubernetes  
> **Goal:** Understand *why Kubernetes exists* before learning its individual components.

---
---

## 1. Why Do We Need Kubernetes?

Before learning Kubernetes, it is important to understand the problem it is trying to solve.

Modern applications are increasingly built using **microservices**.

Instead of having one large application:

```text
                         Application
                              |
                  +-----------+-----------+
                  |           |           |
                  ↓           ↓           ↓
                Users      Payments     Orders
```
we divide the application into multiple smaller services.

Each service can run inside a container.

For example:
E-Commerce Application

User Service       → Container
Payment Service    → Container
Order Service      → Container
Product Service    → Container
Notification       → Container

As the number of services increases, managing all these containers manually becomes difficult.

This is where Kubernetes comes into the picture.

---

## 2. Prerequisite: Containers and Docker

Before learning Kubernetes, you should understand the fundamentals of containers.

Docker is commonly used to work with containers.

However, knowing only Docker commands is not enough.

You should understand:

- What is a container?
- Why are containers lightweight?
- Containers vs Virtual Machines
- Container isolation
- Network isolation
- Namespace isolation
- Container lifecycle
- Container security
- Distroless images
- Multi-stage Docker builds

### Important idea
```text
**Docker**
↓  
Makes working with containers easier

**Kubernetes**
↓  
Manages containers at scale
```
So Docker and Kubernetes are **not competitors doing exactly the same thing**.

They solve **different problems**.

---

## 3. What is Docker?

Docker is a **container platform**.

It provides tools that make it easier to:

- Build container images
- Run containers
- Stop containers
- Remove containers
- Manage container lifecycle

For example:

```text
docker build
docker run
docker ps
docker stop
docker rm
```
---
## 4. What is Kubernetes?

The textbook definition is:

> **Kubernetes is a container orchestration platform.**

But this definition alone isn't enough.

### What does "orchestration" mean?

Suppose you have **hundreds or thousands of containers**.

Someone needs to manage them.

For example:

- Where should a container run?
- What happens if a container crashes?
- What happens if a machine fails?
- How many copies of the application should run?
- What happens when traffic increases?
- How do we distribute traffic?
- How do we replace failed containers?
- How do we manage applications across multiple machines?

Kubernetes provides mechanisms to handle these problems **automatically**.

Therefore:

```text
Docker
   ↓
Container Platform


Kubernetes
   ↓
Container Orchestration Platform
```
---
## 5. Problem With Running Containers Directly

To understand Kubernetes, imagine that we are using Docker directly.

Suppose we have one server:

```text
                    Server
                       |
                     Docker
                       |
             +---------+---------+
             |         |         |
             ↓         ↓         ↓
         Container  Container  Container
```
Initially this might work perfectly.

But as the application grows, problems start appearing.

---
## 6. Problem #1 — Single Host Dependency

Suppose all your containers are running on one machine.
```text
                    Server
                       |
             +---------+---------+
             |         |         |
             ↓         ↓         ↓
            C1        C2        C3
```
Now suppose the server fails.
```text
                    ❌ Server
                       |
             +---------+---------+
             |         |         |
             ↓         ↓         ↓
            C1        C2        C3
```
All containers are affected.

This creates a single point of failure.

---
## 7. How Kubernetes Solves the Single Host Problem

Kubernetes works as a **cluster**.

A cluster is a group of machines called **nodes**.

For example:

```text
                         Kubernetes Cluster
                                  |
                  +---------------+---------------+
                  |               |               |
                  ↓               ↓               ↓
                Node 1          Node 2          Node 3
                  |               |               |
                 Pods            Pods            Pods
```
Now workloads don't have to depend on a single machine.

If one node has a problem, Kubernetes can move/recreate workloads on other available nodes.

This provides better:
- Availability
- Fault tolerance
- Resource utilization

---
## 8. What is a Kubernetes Cluster?

A Kubernetes cluster is a group of **nodes** that work together to run applications.

Conceptually:

```text
                         Kubernetes Cluster
                                  |
                    +-------------+-------------+
                    |                           |
                    ↓                           ↓
              Control Plane                Worker Nodes
                                                 |
                                      +----------+----------+
                                      |          |          |
                                      ↓          ↓          ↓
                                     Pod        Pod        Pod
```
The detailed architecture and components will be covered separately.

For now remember:
```text
Cluster
   ↓
Group of Nodes
   ↓
Runs Applications
```
---
## 9. Problem #2 — Auto Healing

Containers are **ephemeral**.

Ephemeral means something that is **short-lived**.

A container can:

- Crash
- Stop
- Fail
- Become unhealthy

Suppose:

```text
Application
     |
 Container
     |
     ❌
```
With basic Docker usage, you may need to manually investigate and restart/recreate the container.

Kubernetes provides auto-healing.

---
## 10. Kubernetes Auto-Healing

Kubernetes continuously observes the **desired state** of your application.

Suppose you want:

```text
3 replicas
```
Kubernetes tries to maintain:
```text
Pod 1 → Running
Pod 2 → Running
Pod 3 → Running
```
If one fails:
```text
Pod 1 → Running
Pod 2 → ❌
Pod 3 → Running
```
Kubernetes detects that the actual state does not match the desired state.

It creates another replacement:
```text
Pod 1 → Running
Pod 2 → ❌
Pod 3 → Running
Pod 4 → Running
```
The important idea is:
```text
Desired State ≠ Actual State
        ↓
Kubernetes acts
        ↓
Desired State restored
```
This is one of the fundamental ideas behind Kubernetes.

---
## 11. Problem #3 — Auto Scaling

- Imagine your application normally receives:
- 10,000 requests
- But during a festival or sale:
- 100,000 requests
- Your application suddenly needs more capacity.
- With Docker alone, you would have to manually create additional containers.
- Kubernetes provides mechanisms for scaling.

---
## 12. Manual Scaling

Suppose your application currently has:

```text
1 replica
```
You can increase it:
```text
1 → 10 replicas
```
Conceptually:

**Before:**
```text
                  Application
                       |
                      Pod
```
**After:**

                  Application
                       |
              +--------+--------+
              |        |        |
              ↓        ↓        ↓
             Pod      Pod      Pod
              ↓
           ...
              ↓
           10 Pods

Kubernetes commonly represents this configuration using **YAML**.

For example:
```text
spec:
  replicas: 10
```
This tells Kubernetes that you want **ten replicas**.

---
## 13. Horizontal Pod Autoscaler (HPA)

Kubernetes also supports **Horizontal Pod Autoscaling**.

HPA can automatically adjust the number of Pods based on resource utilization or other supported metrics.

**Conceptually:**

```text
Low Traffic
     ↓
  2 Pods


Traffic increases
     ↓
  5 Pods


Traffic increases more
     ↓
  10 Pods
```
So instead of manually changing:
```
replicas: 1
```
to:
```
replicas: 10
```
an autoscaler can adjust the number of replicas according to configured conditions.

---
## 14. Problem #4 — Enterprise Requirements

Running applications in production requires more than simply starting containers.

Production environments commonly require things such as:
```
-> Load balancing
-> Networking
-> Security
-> High availability
-> Scaling
-> Fault tolerance
-> Monitoring
-> Integration with other infrastructure
```
A basic container platform doesn't automatically provide the complete orchestration layer needed for large-scale production environments.

Kubernetes was designed to provide an extensible platform for managing containerized workloads at scale.

---
## 15. Kubernetes and Enterprise Applications

Kubernetes provides a platform around which many other tools and systems can be integrated.

For example:
```
                    Kubernetes
                        |
       ┌────────────────┼────────────────┐
       ↓                ↓                ↓
   Networking        Security        Monitoring
       |                |                |
    Ingress            RBAC          Prometheus
```
This ecosystem is one of the major strengths of Kubernetes.

---
## 16. Where Did Kubernetes Come From?

Kubernetes originated at Google.

Google had experience managing huge numbers of workloads internally

One of the important systems associated with Google's infrastructure history was **Borg**

Kubernetes was influenced by the lessons learned from such large-scale cluster management systems.

The important takeaway is:
```
Google's large-scale infrastructure experience
                    ↓
              Kubernetes
                    ↓
       Open-source container orchestration
```
You don't need to memorize the internal implementation of Borg at this stage.

The important point is that Kubernetes came from experience managing workloads at very large scale.

---
## 17. Kubernetes and CNCF

Kubernetes became part of the Cloud Native Computing Foundation (CNCF) ecosystem.

CNCF provides a large open-source ecosystem around cloud-native technologies.

Examples of projects in the cloud-native ecosystem include:
```
-> Kubernetes
-> Prometheus
-> Container-related projects
-> Networking projects
-> Observability tools
-> Security tools
```
This ecosystem is important because Kubernetes itself does not try to provide every possible feature.

Instead, Kubernetes provides a platform that can be extended and integrated with other technologies.

---
## 18. Kubernetes is Extensible

One of the important ideas introduced in Kubernetes is extensibility.

Kubernetes provides concepts such as:
```
-> Custom Resources
-> Custom Resource Definitions (CRDs)
-> Controllers
```
These allow developers and organizations to extend Kubernetes.

**Conceptually:**

                 Kubernetes
                     |
            ┌────────┴────────┐
            ↓                 ↓
     Built-in Resources   Custom Resources
                              |
                              ↓
                       Custom Controllers

This allows external tools to integrate deeply with Kubernetes.

---
## 19. Example — Ingress Controllers

Kubernetes provides basic networking capabilities.

However, organizations may need more advanced functionality.

External projects can build controllers that integrate with Kubernetes.

This led to concepts such as Ingress Controllers.

**Conceptually:**

                 Internet
                    |
                    ↓
               Load Balancer
                    |
                    ↓
             Ingress Controller
                    |
             ┌──────┴──────┐
             ↓             ↓
          Service       Service
             ↓             ↓
            Pods          Pods

The important idea is that Kubernetes can be extended instead of having every feature built directly into its core.

---
## 20. Docker vs Kubernetes
| **Docker** | **Kubernetes** |
|---|---|
| Container platform | Container orchestration platform |
| Primarily works with containers | Manages containerized workloads across a cluster |
| Can run containers | Can manage many application replicas |
| Basic scaling is possible | Provides sophisticated scaling mechanisms |
| Single-machine usage is common | Designed around cluster-based operation |
| Manual management becomes difficult at scale | Automates many operational tasks |
| Container lifecycle | Application/workload orchestration |

**Easy way to remember**
```
Docker
   ↓
"How do I run my container?"
```
```
Kubernetes
   ↓
"How do I manage my application
running across many containers/nodes?"
```
---
## 21. Four Major Problems Kubernetes Solves

The video's main discussion can be remembered using these four problems:

**1. Single Host**
```
One machine
    ↓
Single point of failure

Kubernetes:

Multiple Nodes
    ↓
Cluster
```
**2. Auto Healing**
```
Container fails
      ↓
Manual intervention

Kubernetes:

Workload fails
      ↓
Kubernetes detects it
      ↓
Replacement workload
```
**3. Auto Scaling**
```
Traffic ↑
    ↓
Need more containers

Kubernetes:

Traffic ↑
    ↓
HPA
    ↓
More Pods
```
**4. Enterprise Capabilities**

Production applications require a broader ecosystem.

Kubernetes provides an extensible platform that can integrate with networking, security, monitoring, load balancing and other cloud-native technologies.

---
## 22. Important Terminology Introduced
**Container**

A lightweight isolated environment used to package and run an application.

**Node**

A machine that participates in a Kubernetes cluster.

**Cluster**

A group of nodes managed as a Kubernetes environment.

**1.Pod**

The basic workload unit Kubernetes uses to run containers.

Detailed explanation will come when studying Pods.

**2.Replica**

A copy of a workload running to provide availability and/or capacity.

**3.ReplicaSet**

A Kubernetes resource used to maintain a desired number of Pod replicas.

**4.HPA**

Horizontal Pod Autoscaler — automatically adjusts the number of Pod replicas based on configured metrics.

**5.API Server**

A central component through which Kubernetes API requests are handled.
The architecture and API Server will be covered in detail separately.

**6.YAML**

Kubernetes commonly uses YAML manifests to describe the desired state of resources.

---
## 23. The Most Important Kubernetes Concept — Desired State

A fundamental idea to remember is:
```
You tell Kubernetes:

"I want this state."

                ↓

Kubernetes continuously works toward:

"Actual state = Desired state"
```
For example:
```
spec:
  replicas: 3
```
means conceptually:
```
Desired state:

3 Pods should be running
```
If only two are running:
```
Actual state = 2
Desired state = 3
```
Kubernetes attempts to correct the difference.

This concept is extremely important because many Kubernetes features are built around declarative configuration and reconciliation.

---
## 24. Why Kubernetes is Important for DevOps

Modern applications increasingly use:
```
Microservices
     ↓
Containers
     ↓
Many containers
     ↓
Need orchestration
     ↓
Kubernetes
```
Therefore, Kubernetes has become an important skill in the DevOps/cloud-native ecosystem.

For someone learning DevOps, understanding Kubernetes is important because it connects many areas:
```
                    DevOps
                      |
       ┌──────────────┼──────────────┐
       ↓              ↓              ↓
      CI/CD        Containers     Kubernetes
                                     |
                    ┌────────────────┼─────────────┐
                    ↓                ↓             ↓
                Networking        Scaling       Security
                    ↓                ↓             ↓
                 Ingress           HPA            RBAC
```
---
