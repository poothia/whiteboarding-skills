# Red Hat Product Portfolio

Reference for mapping customer pains to Red Hat products. Read by the agent during Phase 2 (Story Structure).

---

## Pain-to-Product Map

Quick reference. Find the customer's pain, get the Red Hat answer.

| Customer Pain | Red Hat Product(s) | Board Shorthand |
|--------------|-------------------|-----------------|
| Manual deployments, slow release cycles | OpenShift Pipelines + GitOps | "Automated CI/CD" |
| Configuration drift, environment inconsistency | Ansible Automation Platform + GitOps | "Drift-free infra" |
| Container/image security vulnerabilities | Advanced Cluster Security (ACS) + Quay | "Shift-left security" |
| No visibility across clusters | Advanced Cluster Management (ACM) | "Single pane of glass" |
| Compliance violations, audit failures | ACS + GitOps + Ansible | "Continuous compliance" |
| VM-to-container modernization | OpenShift Virtualization + OpenShift | "Modernize in place" |
| Disconnected / air-gapped environments | OpenShift + Quay Mirror + ACM | "Disconnected-ready" |
| Microservices traffic management, observability | Service Mesh | "Traffic control" |
| Storage sprawl, no data portability | OpenShift Data Foundation | "Unified storage" |
| Scaling event-driven workloads | OpenShift Serverless | "Scale to zero" |
| Multi-cloud / hybrid cloud inconsistency | OpenShift + ACM + GitOps | "Run anywhere" |
| Skills gap, team productivity | OpenShift Dev Spaces + Developer Hub | "Developer self-service" |
| Legacy middleware, monolith decomposition | OpenShift + Application Migration Toolkit | "Strangler fig" |
| No platform self-service for dev teams | OpenShift + Developer Hub + Pipelines | "Internal developer platform" |

---

## Product Entries

### OpenShift Container Platform (OCP)
- **What**: Enterprise Kubernetes platform for building, deploying, and managing containerized applications
- **Solves**: Teams need a consistent, secure container platform that runs anywhere -- on-prem, cloud, edge, disconnected
- **Connects to**: Everything. OCP is the foundation. Pipelines, GitOps, ACS, ACM, Service Mesh, Serverless all run on top
- **Logo**: `https://cdn.jsdelivr.net/gh/devicons/devicon/icons/redhat/redhat-original.svg` (use Red Hat logo; for OpenShift-specific use `https://www.svgrepo.com/show/306575/redhatopenshift.svg`)
- **On the board**: Large central component in the solution architecture. Everything else connects to it

### Ansible Automation Platform (AAP)
- **What**: Enterprise automation for infrastructure, network, cloud, and security operations
- **Solves**: Manual config management, drift between environments, repetitive ops tasks, compliance enforcement at scale
- **Connects to**: Works alongside OpenShift (automates infra around it), feeds into GitOps (declarative automation), complements ACS (security remediation)
- **Logo**: `https://cdn.jsdelivr.net/gh/devicons/devicon/icons/ansible/ansible-original.svg`
- **On the board**: Sits alongside or below the cluster layer, automating the infrastructure foundation

### Red Hat Enterprise Linux (RHEL)
- **What**: Enterprise Linux operating system -- the foundation everything else runs on
- **Solves**: OS standardization, security patching, hardware compatibility, long-term support, compliance baseline
- **Connects to**: Foundation under OpenShift (CoreOS), under AAP, under everything. Image mode RHEL for immutable infrastructure
- **Logo**: `https://cdn.jsdelivr.net/gh/devicons/devicon/icons/redhat/redhat-original.svg`
- **On the board**: Base layer / foundation bar at the bottom of architecture diagrams

### Advanced Cluster Security (ACS / StackRox)
- **What**: Kubernetes-native security platform -- vulnerability scanning, runtime protection, compliance, network segmentation
- **Solves**: Container image vulnerabilities, runtime threats, compliance violations, insecure configurations, lack of security visibility
- **Connects to**: Scans images in Quay, enforces policy on OpenShift, integrates into Pipelines for shift-left, reports to ACM for multi-cluster
- **Logo**: StackRox shield icon or Red Hat ACS icon
- **On the board**: Security layer that wraps around or sits on top of the cluster, scanning everything

### Advanced Cluster Management (ACM)
- **What**: Multi-cluster lifecycle management -- deploy, observe, govern clusters across environments
- **Solves**: No visibility across clusters, inconsistent policies, manual cluster provisioning, multi-cloud governance
- **Connects to**: Manages OpenShift clusters, pushes GitOps configs, aggregates ACS findings, distributes Ansible policies
- **Logo**: Red Hat logo with "ACM" label
- **On the board**: Management plane sitting ABOVE the clusters, with lines down to each managed cluster

### Quay
- **What**: Private container image registry with security scanning, geo-replication, and image mirroring
- **Solves**: Untrusted public images, no vulnerability scanning in registry, disconnected image distribution, registry sprawl
- **Connects to**: Feeds images to OpenShift, scanned by ACS, mirrored for disconnected environments, integrated with Pipelines
- **Logo**: Quay logo (whale/anchor icon)
- **On the board**: Registry component between the build pipeline and the cluster, often paired with ACS scanning

### OpenShift GitOps (ArgoCD)
- **What**: Declarative continuous delivery using Git as source of truth for cluster and application state
- **Solves**: Configuration drift, manual deployments, lack of audit trail, environment inconsistency, rollback complexity
- **Connects to**: Deploys to OpenShift, syncs with Quay images, enforces ACS policies, managed by ACM across clusters
- **Logo**: `https://cdn.jsdelivr.net/gh/devicons/devicon/icons/argocd/argocd-original.svg`
- **On the board**: Delivery component between Git repo and the cluster, with sync arrows showing declarative state

### OpenShift Pipelines (Tekton)
- **What**: Kubernetes-native CI/CD pipelines -- build, test, scan, deploy as code
- **Solves**: Manual build processes, no CI/CD standardization, non-cloud-native pipeline tools, slow feedback loops
- **Connects to**: Builds images for Quay, triggers ACS scans, feeds GitOps for deployment, runs on OpenShift
- **Logo**: Tekton logo (chain link icon)
- **On the board**: Build/CI component on the left side of the delivery pipeline, feeding into GitOps on the right

### OpenShift Serverless
- **What**: Event-driven serverless workloads on Kubernetes using Knative
- **Solves**: Over-provisioned resources for bursty workloads, event processing at scale, scale-to-zero requirements
- **Connects to**: Runs on OpenShift, triggered by events from Service Mesh or external sources
- **Logo**: Knative logo
- **On the board**: Specialized workload component within the cluster for event-driven services

### OpenShift Service Mesh (Istio)
- **What**: Traffic management, observability, and security for microservices communication
- **Solves**: No visibility into service-to-service traffic, manual mTLS, no canary/blue-green deployments, tracing gaps
- **Connects to**: Runs on OpenShift, complements ACS (network policy), works with Serverless for event routing
- **Logo**: `https://cdn.jsdelivr.net/gh/devicons/devicon/icons/istio/istio-original.svg`
- **On the board**: Mesh/network layer within the cluster, shown as a translucent overlay or sidecar pattern

### OpenShift Virtualization
- **What**: Run virtual machines alongside containers on the same OpenShift platform
- **Solves**: VM-to-container migration barriers, dual platform management (VMs and containers), legacy app modernization
- **Connects to**: Part of OpenShift, managed by ACM, automated by Ansible, secured by ACS
- **Logo**: VM icon or KubeVirt logo
- **On the board**: Component within the cluster showing VMs running next to containers -- bridging old and new

### OpenShift Data Foundation (ODF)
- **What**: Software-defined storage for containers -- block, file, and object storage on Kubernetes
- **Solves**: Storage sprawl, no data portability between clouds, stateful app challenges on K8s, backup/DR gaps
- **Connects to**: Provides persistent storage for OpenShift workloads, managed by ACM across clusters
- **Logo**: Ceph/ODF logo
- **On the board**: Storage layer at the bottom of the cluster, beneath the workload components

---

## Common Solution Patterns

When multiple pains cluster together, use these pre-built solution combos:

| Pattern Name | Products | When to Use |
|-------------|----------|------------|
| **Platform Modernization** | RHEL + OpenShift + Pipelines + GitOps | Customer running legacy VMs/middleware, wants to modernize |
| **Full DevSecOps** | Pipelines + GitOps + ACS + Quay | Customer wants shift-left security and automated delivery |
| **Security & Compliance** | ACS + Quay + GitOps + ACM | Regulated industry, audit failures, compliance mandate |
| **Multi-Cluster Governance** | ACM + GitOps + ACS | Multiple clusters across clouds/regions, no unified management |
| **Hybrid / Disconnected** | OpenShift + Quay Mirror + ACM + Ansible | Air-gapped, classified, or edge environments |
| **Internal Developer Platform** | OpenShift + Developer Hub + Pipelines + GitOps | Developer self-service, golden paths, platform engineering |
| **VM Modernization** | OpenShift Virtualization + OpenShift + Ansible | Migrate VMs without re-architecting, run VMs + containers together |

---

## Refreshing This File

When the user says "refresh the portfolio":
1. Fetch Red Hat product pages for any new products or naming changes
2. Update entries with current descriptions and capabilities
3. Add new products to the Pain-to-Product Map
4. Update the "Last refreshed" date below

**Last refreshed**: 2026-09-25
