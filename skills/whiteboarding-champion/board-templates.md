# Board Templates

Two concrete board layouts the agent uses as reference when building whiteboards.

---

## Template 1: Pain Chain to Adoption (Default)

The standard board for any customer meeting. 4-act story flow, left-to-right.

### Scenario: Bank with GitOps Drift Detection Pain

**Customer**: Mid-tier bank, 200+ microservices, 3 failed SOX audits
**Audience**: VP of Engineering + CTO
**Pains**: Config drift undetected, manual audit prep, no unified cluster view
**Solution Pattern**: Security & Compliance (ACS + Quay + GitOps + ACM)

### Board Layout (Single Canvas ~5000x2800)

```
+------------------------------------------------------------------------+
| [Bank Logo]  "Acme Bank + Red Hat: Compliance-Ready Platform"  [RH Logo]|
+------------------------------------------------------------------------+
|                |                 |                    |                  |
| ACT 1: THEIR  | ACT 2: THE PAIN | ACT 3: SOLUTION   | ACT 4: EASY PATH|
| WORLD          |                 |                    |                  |
|                |                 |                    |                  |
| +----------+  | +----------+   | +----------+       | ADOPTION RIBBON  |
| | App A    |  | | App A    |   | | App A    |       | =====[P1]==[P2]= |
| +----------+  | +--[drift!]-+  | +----------+       |  =[P3]========>  |
|    |          |    |           |    |                |                  |
|    v          |    v           |    v                | P1: Pilot        |
| +----------+  | +----------+   | +----------+       | (circle) 2 apps  |
| | K8s      |  | | K8s      |   | |OpenShift |       | on OCP + GitOps  |
| | Cluster  |  | | [no      |   | |  [logo]  |       | 4 weeks          |
| |          |  | |  single  |   | +----------+       |                  |
| +----------+  | |  view!]  |   |    |               | P2: Expand       |
|    |          | +----------+   |    v               | (circle) 50 apps |
|    v          |    |           | +----------+       | + ACS + Quay     |
| +----------+  |    v           | | GitOps   |       | 3 months         |
| | Jenkins  |  | +----------+   | | [ArgoCD  |       |                  |
| | (legacy) |  | | Jenkins  |   | |  logo]   |<------| P3: Scale        |
| +----------+  | | [slow     |   | +----------+ pain | (circle) Full    |
|    |          | |  deploys!]|   |    |          to   | DevSecOps        |
|    v          | +----------+   |    v          prod  | 6 months         |
| +----------+  |    |           | +----------+       |                  |
| | Manual   |  |    v           | | ACS      |       | BEFORE / AFTER   |
| | Scripts  |  | [3 week        | | [shield  |       | [Act1 arch]      |
| +----------+  |  audit prep!]  | |  logo]   |       |  small, faded    |
|               |                | +----------+       |       |          |
| [Terraform]   | PAIN CHAIN:    |    |               |   transform      |
| [Ansible      | drift -> audit | +----------+       |    arrow          |
|  ad-hoc]      | fail -> $2M    | | ACM      |       |       |          |
|               | risk           | | [logo]   |       |       v          |
|               |                | +----------+       | [Act3 arch]      |
|               |                |                    |  prominent       |
|               |                | PROOF POINTS:      |                  |
|               |                | "BankCo: 94% drift |                  |
|               |                |  reduction, 90 days"|                 |
+------------------------------------------------------------------------+
| DISCUSSION ZONE                                                         |
| "Your Questions"      | "Next Steps"         | "Parking Lot"          |
|                       |                      |                         |
+------------------------------------------------------------------------+
```

### Elements Used
- **Act 1**: Architecture diagram with shaped rects (App, K8s, Jenkins, scripts), their tool logos inside, connectors showing flow
- **Act 2**: SAME architecture with red/pink sticky notes pinned to components: "drift!", "no single view!", "slow deploys!", "3 week audit prep!". Pain chain arrows: drift -> audit fail -> $2M risk
- **Act 3**: New architecture with Red Hat product logos (OpenShift, ArgoCD, ACS, ACM). Arrows from Act 2 pain stickies to the product that solves them. Proof point cards near products
- **Act 4**: Horizontal ribbon timeline with 3 phase milestones (circles). Before/After architecture comparison. Concrete deliverables at each phase
- **Discussion zone**: Deliberately empty, three labeled sections

---

## Template 2: Technical Deep-Dive

For deeply technical audiences (platform engineers, architects). More diagrams, fewer stickies, deeper solution architecture.

### Scenario: Telecom modernizing CI/CD pipeline

**Customer**: Large telecom, 500+ developers, legacy CI/CD on Jenkins
**Audience**: Platform engineering team lead + 3 senior engineers
**Pains**: Jenkins is brittle, no container-native CI, manual image management, no policy enforcement
**Solution Pattern**: Full DevSecOps (Pipelines + GitOps + ACS + Quay)

### Board Layout (Single Canvas ~6000x3200)

```
+------------------------------------------------------------------------+
| [Telco Logo]  "DevSecOps Pipeline Modernization"  [RH Logo]            |
+------------------------------------------------------------------------+
|                                |                                        |
| ACT 1+2: TODAY (left half)    | ACT 3+4: TOMORROW (right half)         |
| Current pipeline (broken)     | Red Hat pipeline (solved)              |
|                                |                                        |
| [Mermaid: sequenceDiagram      | [Mermaid: sequenceDiagram              |
|  showing the broken flow]      |  showing the clean flow]               |
|                                |                                        |
|  Developer ->> Jenkins: push   |  Developer ->> Git: push               |
|  Jenkins ->> Docker Hub: build |  Git ->> Pipelines: trigger            |
|  Jenkins ->> ???: where to     |  Pipelines ->> Quay: build+scan       |
|    deploy?                     |  Quay ->> ACS: vulnerability check     |
|  ???: manual deploy via SSH    |  ACS -->> GitOps: policy gate          |
|  [RED CALLOUTS on each         |  GitOps ->> OpenShift: sync deploy     |
|   broken step]                 |  [GREEN CALLOUTS on each               |
|                                |   improved step]                       |
|................................|........................................|
|                                |                                        |
| ARCHITECTURE (current)        | ARCHITECTURE (target)                   |
|                                |                                        |
| +------+   +--------+         | +------+   +----------+  +-------+     |
| | Dev  |-->| Jenkins|--???--> | | Dev  |-->| Pipelines|->| Quay  |     |
| +------+   +--------+         | +------+   | [Tekton] |  |[logo] |     |
|            [brittle, single   |            +----------+  +---+---+     |
|             point of failure] |                 |            |          |
|                               |                 v            v          |
| +----------+  +----------+   | +----------+  +-------+  +-------+     |
| | Docker   |  | Manual   |   | | GitOps   |  | ACS   |  | Image |     |
| | Hub      |  | SSH      |   | | [ArgoCD] |  |[shield|  | Scan  |     |
| | (public) |  | Deploy   |   | |          |  | logo] |  | Report|     |
| +----------+  +----------+   | +----------+  +-------+  +-------+     |
|                               |      |                                  |
| [pain stickies pinned to      |      v                                  |
|  broken components]           | +----------+                            |
|                               | |OpenShift |  "Deploy in minutes,      |
|                               | |[logo]    |   not days"               |
|                               | +----------+                            |
|                               |                                         |
|                               | ADOPTION STEPS (pipeline diagram):      |
|                               | Week 1: Install OCP + Pipelines         |
|                               | Week 2: Migrate 1 Jenkins job           |
|                               | Week 3: Add Quay + ACS scanning         |
|                               | Week 4: GitOps for 1 app, demo to team  |
|                               | Month 2: Migrate 10 jobs, sunset Jenkins|
+------------------------------------------------------------------------+
| DISCUSSION: "Your Jenkins jobs"  | "Migration priority" | "Questions"  |
+------------------------------------------------------------------------+
```

### Elements Used
- **Mermaid sequence diagrams**: Two side-by-side -- current broken flow (left, with red callouts) vs Red Hat clean flow (right, with green callouts). Native Mermaid widgets via `canvas_load_format_skill`
- **Architecture diagrams**: Shaped components with logos. Current (left, pain stickies pinned on broken parts) vs target (right, Red Hat product logos, clean connectors)
- **Adoption steps**: Pipeline-style step list, not a timeline ribbon. Technical audience wants concrete steps, not abstract phases
- **Discussion zone**: Includes a section for "Your Jenkins jobs" -- inviting them to map their specific jobs to the migration plan

### Key Differences from Template 1
- Acts 1+2 merged on the left, Acts 3+4 merged on the right (before/after side-by-side rather than 4-zone linear flow)
- Mermaid sequence diagrams for the workflow comparison (technical audiences read these naturally)
- Adoption steps as a concrete task list, not a phased timeline
- More detail on the "how" -- specific steps, specific weeks, specific migration activities
