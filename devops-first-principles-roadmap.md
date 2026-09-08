# DevOps From First Principles — A Roadmap to Employable Skill

**Philosophy of this roadmap:** For every tool (Docker, Kubernetes, Terraform, Jenkins...), you learn *the underlying problem and mechanism* before the tool. Tools change every 2-3 years; the problems they solve don't. An interviewer can tell in 5 minutes whether you know "kubectl commands" or whether you understand "why orchestration exists" — the second one gets hired.

Each phase has: **Core Idea → Why It Exists → What To Actually Learn → Tool(s) That Implement It → Project to Prove It**

---

## Phase 0: The Mental Model (1 week)

**Core idea:** DevOps is not a toolchain. It's the answer to one organizational problem: *the wall between "people who write code" and "people who run code in production" causes slow, unreliable releases.*

- Learn the history: pre-DevOps (Dev throws code "over the wall" to Ops) → CAMS model (Culture, Automation, Measurement, Sharing)
- Understand: every tool you'll learn exists to automate one manual, error-prone, human step in getting code from a laptop to a paying customer safely
- Read: "The Phoenix Project" (novel, explains the *why* better than any course)

**No tools yet. No project yet.** This phase just recalibrates how you'll think about everything below.

---

## Phase 1: Operating System & Linux Internals (3-4 weeks)

**Why this matters:** Every server you'll ever manage is (almost certainly) Linux. Docker, Kubernetes, cloud VMs — all just abstractions sitting on top of the Linux kernel. If you don't understand the kernel-level primitives, containers will always feel like magic instead of engineering.

**Core ideas to actually understand (not memorize commands):**
- Processes: PID, parent/child, process states, how a shell forks/execs a program
- Filesystem: everything-is-a-file philosophy, inodes, file permissions (owner/group/other, the actual bits behind `chmod 755`)
- Users & permissions: UID/GID, sudo, why root is dangerous
- Memory: virtual memory, how a process thinks it owns all RAM
- Networking basics: what a socket is, ports, how two processes on different machines talk
- **Namespaces & cgroups** — these two kernel features are literally what Docker is built from. Learn them *before* Docker.
- Systemd/init: how services start, stop, and restart on boot

**Tools:** Ubuntu/Debian server (install one in a VM), `ps`, `top`, `strace`, `lsof`, `systemctl`, `iptables` basics

**Project:** Set up a bare Linux VM (VirtualBox/cloud free tier), run a web server manually with no orchestration, break it on purpose (kill the process, fill the disk, exhaust memory) and fix it using only command-line diagnosis. Write up a "runbook" of what you did — this becomes a portfolio piece.

---

## Phase 2: Networking Fundamentals (2-3 weeks)

**Why this matters:** "It works on my machine" is 80% a networking misunderstanding. DevOps is largely the job of making machines talk to each other reliably.

**Core ideas:**
- OSI/TCP-IP model — but focus on layers 3, 4, 7 (IP, TCP/UDP, HTTP) since that's 95% of real work
- DNS: how a name becomes an IP, resolution chain, why DNS propagation delay exists
- HTTP/HTTPS: request/response cycle, status codes, headers, what TLS handshake actually does
- Load balancing: Layer 4 vs Layer 7, why load balancers exist, round robin vs least-connections
- Reverse proxies vs forward proxies (this trips up almost everyone)
- Firewalls & security groups: allow/deny rules, stateful vs stateless

**Tools:** `curl`, `dig`/`nslookup`, `nginx` (as a reverse proxy, config by hand), Wireshark (optional but great for building intuition)

**Project:** Configure Nginx as a reverse proxy in front of two backend app instances, manually load-balance between them, and enable HTTPS with a self-signed cert. Explain in writing what happens, packet by packet, when a browser hits your site.

---

## Phase 3: Programming & Scripting for Automation (3-4 weeks, can run parallel to Phase 1-2)

**Why this matters:** DevOps engineers are automation engineers. If you can't script, you can't automate — you're just a person who clicks buttons in a GUI slower than a machine could.

**Core ideas:**
- Bash: variables, loops, conditionals, exit codes, piping, writing idempotent scripts
- Python (or Go later): file handling, API calls (requests library), JSON/YAML parsing — these are the two data formats you'll live in
- Regular expressions — enough to parse logs
- Idempotency as a concept: a script/action should be safely re-runnable without side effects (this concept reappears in every later phase — IaC, config management, CI/CD)

**Tools:** Bash, Python, `jq`, `yq`

**Project:** Write a Python script that hits a public API, parses the JSON, and generates a formatted report. Write a Bash script that health-checks a list of URLs and alerts (email/Slack webhook) if any are down. Make both idempotent and safe to cron.

---

## Phase 4: Version Control — Git Internals (1-2 weeks)

**Why this matters:** Everyone "knows git commands." Almost nobody understands git as a content-addressable graph database, which is why merge conflicts and detached HEADs terrify most junior engineers.

**Core ideas:**
- Git's internal object model: blobs, trees, commits — it's a DAG, not a timeline
- What a branch actually is (just a pointer to a commit)
- How merge vs rebase differ *mechanically*, not just "which command to run"
- Branching strategies: trunk-based development vs GitFlow, and *why* trunk-based is what most modern high-performing teams use (this is a real interview topic)

**Tools:** Git, GitHub/GitLab

**Project:** Deliberately create and resolve 3 different conflict scenarios (merge conflict, rebase conflict, detached HEAD recovery). Set up branch protection rules and a PR-based workflow on GitHub for a personal repo, as if a team were using it.

---

## Phase 5: Continuous Integration & Continuous Delivery — The Concept First (2-3 weeks)

**Why this matters:** CI/CD is not "using Jenkins/GitHub Actions." It's the automation of: build → test → package → deploy, so a human never manually does these repetitive, error-prone steps.

**Core ideas:**
- What "continuous integration" solves: merge hell from long-lived branches
- What "continuous delivery" vs "continuous deployment" actually differ on (this is a common interview trick question)
- Pipeline as a series of stages with fail-fast gates
- Artifact management: why you build once and promote the same artifact through environments (never rebuild per environment)
- Testing pyramid: unit vs integration vs e2e, and why pipelines run cheap tests first

**Tools:** GitHub Actions (easiest to start), then Jenkins (still heavily used in enterprises, worth knowing) or GitLab CI

**Project:** Take a small app (from Phase 3), write a full CI/CD pipeline: on push → lint → test → build artifact → build container image → push to registry → deploy to a test server. This single project is one of the strongest portfolio pieces you can show.

---

## Phase 6: Virtualization & Containers — From Kernel Primitives Up (3-4 weeks)

**Why this matters:** You already learned namespaces and cgroups in Phase 1. Containers are just a friendly UI over those two primitives plus a layered filesystem. Learning it in this order means Docker will never feel like a black box.

**Core ideas:**
- VMs vs containers: what's actually virtualized in each (hardware vs kernel)
- A container is just a process with restricted namespace + cgroup limits + a chroot-like filesystem — build intuition by manually creating a very limited "container" with `unshare`/`chroot` before touching Docker
- Image layers: union filesystem, why layer caching in builds matters for speed
- Container networking: bridge networks, how containers get IPs, port mapping

**Tools:** Docker, Docker Compose, container registries (Docker Hub / ECR / GCR)

**Project:** Containerize a multi-service app (e.g., a web app + database + cache) using Docker Compose. Write a multi-stage Dockerfile optimized for small image size and build cache efficiency. Push images to a registry as part of the Phase 5 pipeline.

---

## Phase 7: Orchestration — The Scheduling Problem First (4-6 weeks)

**Why this matters:** Once you have many containers across many machines, new problems appear: which machine runs which container, what happens when a machine dies, how do containers find each other. Kubernetes is one (complex) answer to these problems — but the problems exist independent of Kubernetes.

**Core ideas (learn these as *problems*, before K8s vocabulary):**
- Scheduling: given N containers and M machines with different capacities, how do you place workloads efficiently?
- Self-healing: how does a system detect a dead process/container and restart it automatically?
- Service discovery: if containers get new IPs every restart, how do other services keep finding them?
- Declarative state reconciliation: you declare "I want 3 replicas," and a controller *continuously* works to make reality match that — this reconciliation-loop idea is the single most important concept in Kubernetes and reappears everywhere in modern infra
- Rolling updates & rollbacks: how do you deploy a new version with zero downtime?

**Then map these problems onto Kubernetes concepts:**
- Pods, Deployments, Services, ConfigMaps/Secrets, Ingress
- kubelet, controller-manager, scheduler, etcd (just enough to know what each control-plane component does)

**Tools:** Kubernetes (minikube/kind for local practice), Helm (for packaging)

**Project:** Deploy your multi-service app (from Phase 6) onto a local Kubernetes cluster. Configure a Deployment with health checks, a Service for internal communication, and an Ingress for external access. Simulate a pod crash and observe self-healing. Perform a rolling update and a rollback.

---

## Phase 8: Infrastructure as Code (2-3 weeks)

**Why this matters:** Manually clicking through a cloud console to create servers is the exact "manual, error-prone step" DevOps exists to eliminate. IaC applies the *same idempotency and declarative-state ideas from Phase 7* to infrastructure itself.

**Core ideas:**
- Declarative vs imperative infrastructure management
- State: how a tool knows what already exists vs what needs to change (Terraform's state file concept)
- Idempotency at the infra level: running the same IaC script twice should produce the same result, not duplicate resources
- Configuration management vs provisioning — Terraform provisions infra; Ansible/Chef/Puppet configure what's *inside* that infra (a common point of confusion)

**Tools:** Terraform (provisioning), Ansible (configuration management)

**Project:** Write Terraform code to provision a small cloud environment (VPC, subnet, security group, one VM) from scratch. Use Ansible to configure that VM (install Docker, deploy your app). Destroy and re-create the whole environment from code to prove reproducibility.

---

## Phase 9: Cloud Computing Fundamentals (3-4 weeks, layered on Phase 8)

**Why this matters:** Cloud providers didn't invent new concepts — they packaged compute, storage, and networking as on-demand services. Understanding the underlying abstraction means you can move between AWS/GCP/Azure without relearning everything.

**Core ideas:**
- Compute abstraction levels: bare VM → managed containers → serverless functions, and the trade-offs (control vs operational burden) at each level
- Storage types: block storage vs object storage vs file storage — and *when* each is the right choice
- Networking in the cloud: VPCs, subnets (public/private), NAT gateways, why private subnets exist
- IAM: the principle of least privilege, roles vs users vs policies
- Managed services vs self-hosted: why a team would pay for RDS instead of running their own database VM

**Tools:** Pick ONE cloud to go deep on for employability (AWS has the largest job market) — EC2, S3, VPC, IAM, RDS, ECS/EKS or equivalent

**Project:** Deploy your full application stack (from Phases 6-8) onto real cloud infrastructure: app in a private subnet, load balancer in a public subnet, managed database, proper IAM roles (not root credentials), all provisioned via your Terraform code from Phase 8.

---

## Phase 10: Observability (2-3 weeks)

**Why this matters:** "It's deployed" is not the finish line — you need to know if it's *healthy* without SSH-ing in and guessing. Observability is how you answer "why is it broken" in minutes instead of hours.

**Core ideas:**
- The three pillars: metrics (numbers over time), logs (discrete events), traces (a request's journey across services) — and what each is good/bad at
- The RED method (Rate, Errors, Duration) and USE method (Utilization, Saturation, Errors) for what to actually measure
- Alerting philosophy: alert on symptoms (user-facing impact) not causes (CPU is high) — alert fatigue is a real, career-relevant concept
- Centralized logging: why you can't just SSH into 50 servers to grep logs

**Tools:** Prometheus + Grafana (metrics), ELK/Loki (logs), basic OpenTelemetry awareness (tracing)

**Project:** Instrument your app with metrics, deploy Prometheus + Grafana to scrape and visualize them, set up a meaningful alert (e.g., error rate > 5%) that fires to Slack/email. Centralize your app's logs with Loki or the ELK stack.

---

## Phase 11: Security Fundamentals for DevOps (DevSecOps) (2 weeks)

**Why this matters:** Security bolted on at the end is why breaches happen. Increasingly, "DevOps engineer" job postings expect baseline security literacy.

**Core ideas:**
- Secrets management: why hardcoded credentials in code/config are a critical failure, and how a secrets manager solves it
- Principle of least privilege (reinforcing Phase 9's IAM concept)
- Image/dependency scanning: why you scan container images and dependencies for known vulnerabilities
- Network segmentation as a security control (reinforcing Phase 2/9)

**Tools:** HashiCorp Vault or cloud-native secret managers (AWS Secrets Manager), Trivy (image scanning), `git-secrets` or similar for preventing leaked credentials

**Project:** Remove all hardcoded secrets from your app/pipeline and move them into a secrets manager. Add a container image scan step to your CI/CD pipeline that fails the build on critical vulnerabilities.

---

## Phase 12: Capstone Project + Job Readiness (3-4 weeks)

This is where everything fuses into one project you can talk through fluently in an interview, end to end.

**Capstone requirements:**
1. A real (even if simple) multi-service application
2. Source in Git, trunk-based workflow, PR-gated
3. Full CI/CD pipeline: lint → test → build → scan → containerize → deploy
4. Infrastructure fully defined as code (Terraform) — nothing clicked manually in a console
5. Deployed on Kubernetes (managed, e.g., EKS) in the cloud
6. Observability: metrics dashboard + at least one working alert
7. Secrets properly managed, no hardcoded credentials anywhere
8. A written architecture doc + a README explaining *why* you made each decision (not just what you did) — this document is what differentiates you in interviews

**Job readiness:**
- Be ready to explain, for every layer above, the problem it solves *without naming the tool first* — this is the actual first-principles test interviewers use
- Practice system design questions: "design a CI/CD pipeline for X," "how would you scale Y," "debug this outage" scenarios
- Contribute to one open-source DevOps-adjacent project if possible — real collaboration signal
- Get a relevant certification only *after* the above (not before) if you want one for resume filtering: AWS Solutions Architect Associate or CKA (Certified Kubernetes Administrator) are the most respected

---

## Suggested Timeline

| Phase | Duration | Cumulative |
|---|---|---|
| 0. Mental model | 1 week | 1 week |
| 1. Linux internals | 3-4 weeks | ~5 weeks |
| 2. Networking | 2-3 weeks | ~8 weeks |
| 3. Scripting (parallel) | overlaps 1-2 | — |
| 4. Git internals | 1-2 weeks | ~10 weeks |
| 5. CI/CD concepts | 2-3 weeks | ~13 weeks |
| 6. Containers | 3-4 weeks | ~17 weeks |
| 7. Orchestration | 4-6 weeks | ~23 weeks |
| 8. IaC | 2-3 weeks | ~26 weeks |
| 9. Cloud fundamentals | 3-4 weeks | ~30 weeks |
| 10. Observability | 2-3 weeks | ~33 weeks |
| 11. Security | 2 weeks | ~35 weeks |
| 12. Capstone + job prep | 3-4 weeks | ~39 weeks |

**Roughly 8-10 months** of consistent, serious study (10-15 hrs/week) takes you from zero to employable junior/mid-level DevOps engineer — with real depth, not just tool familiarity.

---

## The Recurring Thread to Notice

Across all 12 phases, the same handful of ideas keep reappearing in new clothes:
- **Idempotency** (scripts → CI/CD → IaC → config management)
- **Declarative desired-state + reconciliation loop** (Kubernetes → Terraform)
- **Least privilege** (Linux users → cloud IAM → secrets management)
- **Abstraction layering** (namespaces/cgroups → containers → orchestration → serverless)

If you can explain these four ideas and point to where they show up at every layer of your stack, you're not "someone who knows DevOps tools" — you're someone who understands infrastructure, which is exactly what gets hired and promoted.
