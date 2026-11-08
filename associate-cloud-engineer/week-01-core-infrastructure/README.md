# Week 1: Google Cloud Fundamentals: Core Infrastructure

Course 60, "Google Cloud Fundamentals: Core Infrastructure". Introductory, about 5 hours of videos and hands-on labs. It introduces the main Google Cloud terms and compares the compute and storage services, along with resource and policy management tools. These are review notes, not a lab walkthrough.

| Course | Direct link |
|---|---|
| Course 60 | [Open in Google Skills](https://www.skills.google/course_templates/60) |

> **Note on the module list:** the course's module and lab list sits behind the Google Skills sign-in, so the modules below follow the standard outline for this course. If your course shows different modules or labs, update the tables to match. Lab IDs stay `TBD` until confirmed.

## Modules

| # | Module | Lab | Lab ID |
|---|---|---|---|
| 1 | Course introduction | — | — |
| 2 | Introducing Google Cloud | Getting Started with Cloud Marketplace | TBD |
| 3 | Resources and access in the cloud | — | — |
| 4 | Virtual machines and networks in the cloud | Getting Started with VPC Networking and Google Compute Engine | TBD |
| 5 | Storage in the cloud | Getting Started with Cloud Storage and Cloud SQL | TBD |
| 6 | Containers in the cloud | Getting Started with Google Kubernetes Engine | TBD |
| 7 | Applications in the cloud | Getting Started with Cloud Run (older versions: App Engine) | TBD |
| 8 | Prompt engineering | — | — |
| 9 | Course summary | — | — |

## Module 2: Introducing Google Cloud

Key points:

- **Cloud computing** has five traits (NIST): on-demand self-service, broad network access, resource pooling, rapid elasticity, and measured service (pay for what you use).
- **Service models:**
  - IaaS: you manage the OS and above. Compute Engine.
  - PaaS: you bring code, Google runs the platform. App Engine, Cloud Run.
  - Serverless / FaaS: Cloud Run functions, no servers to think about.
  - SaaS: finished apps such as Gmail and Workspace.
- **Global infrastructure:** locations > regions > zones. A zone is a deployment area inside a region and a single failure domain. Spread resources across zones for high availability and across regions for disaster recovery.
- **Resource scope:** zonal (VM, persistent disk), regional (subnet, regional MIG, Cloud SQL HA), multi-regional or global (Cloud Storage multi-region, global load balancer, VPC network).
- **Pricing:** per-second billing for Compute Engine, sustained use discounts applied automatically, committed use discounts for 1 or 3 years, Spot (preemptible) VMs for fault-tolerant batch work. Use the Google Cloud Pricing Calculator to estimate.
- **Sustainability and openness:** Google runs carbon-free energy goals, and open-source tools such as Kubernetes and TensorFlow help avoid vendor lock-in.

Lab takeaway (Cloud Marketplace): you can deploy a full software stack, such as a LAMP stack, from a template in a few clicks. Marketplace creates the VMs and firewall rules for you. Software license fees, if any, appear in the deploy page.

## Module 3: Resources and access in the cloud

Key points:

- **Resource hierarchy:** organization > folders > projects > resources. Policies are inherited downward, and a less restrictive parent policy overrides a more restrictive child policy.
- **Projects** have three identifiers:

  | Identifier | Unique | Who sets it | Can change |
  |---|---|---|---|
  | Project ID | Globally | You (or suggested) | No |
  | Project name | No | You | Yes |
  | Project number | Globally | Google | No |

- **IAM:** who (principal) can do what (role) on which resource.
  - Principals: Google account, Google group, service account, Cloud Identity / Workspace domain.
  - Role types: basic (Owner, Editor, Viewer, Billing Administrator), predefined (per service, such as `roles/compute.instanceAdmin`), custom (your own permission list; project or org level only, not folder).
  - Basic roles are broad; prefer predefined roles and least privilege.
  - Deny policies can block access even when an allow policy grants it.
- **Service accounts** are identities for apps and VMs, not people. They are both an identity (they get roles) and a resource (you control who can use or impersonate them).
- **Cloud Identity** lets an organization manage users and groups without Gmail accounts, and remove access centrally when someone leaves.
- **Ways to interact:** Google Cloud console, Cloud SDK and Cloud Shell (`gcloud`, `gsutil`/`gcloud storage`, `bq`), client libraries and APIs, Google Cloud app (mobile).

```bash
gcloud config list                                   # active account and project
gcloud projects get-iam-policy $PROJECT_ID           # who has what
gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member=user:someone@example.com --role=roles/viewer
```

## Module 4: Virtual machines and networks in the cloud

Key points:

- **VPC networks** are global. Subnets are regional and can span zones within a region. You can expand a subnet's IP range without downtime.
- **Auto mode** VPC creates one subnet per region automatically; **custom mode** gives full control and is the production choice. The `default` network is auto mode with permissive firewall rules.
- **Firewall rules** are applied at the VPC level and can target instances with network tags or service accounts. Implied rules: deny all ingress, allow all egress.
- **Routes:** every VPC has built-in routes between its subnets, so VMs in different regions on the same VPC talk over internal IPs.
- **Connecting VPCs:** VPC Network Peering (two VPCs, no transitive peering) and Shared VPC (one host project shares its network with service projects, central IAM control).
- **Compute Engine:**
  - Machine families: general purpose (E2, N2, N4), compute optimized (C2, C3), memory optimized (M-series), accelerator optimized (A2, G2 with GPUs). Custom machine types let you pick vCPU and memory.
  - Preemptible and Spot VMs: much cheaper, can be stopped at any time. Good for batch jobs.
  - Autoscaling with managed instance groups.
- **Cloud Load Balancing:** one global anycast IP, cross-region, no pre-warming. See [week-01-load-balancing](../week-01-load-balancing/) for the types and the full chain.
- **Cloud DNS** is a managed, programmable DNS service; **Cloud CDN** caches content at edge locations to cut latency and egress cost.
- **Hybrid connectivity:**

  | Option | Path | When |
  |---|---|---|
  | Cloud VPN (HA VPN) | IPsec over the internet | Low to moderate bandwidth, quick setup. Cloud Router + BGP for dynamic routes |
  | Direct Peering | Your network to a Google PoP | Google public services, no SLA |
  | Carrier Peering | Through a service provider | Same, when you cannot reach a PoP |
  | Dedicated Interconnect | Private physical link to Google | High bandwidth, up to 99.99% SLA |
  | Partner Interconnect | Private link via a partner | Cannot reach a colocation facility |
  | Cross-Cloud Interconnect | Google to another cloud | Multicloud |

Lab takeaway (VPC and Compute Engine): you delete the default network, create an auto mode network, add firewall rules for ICMP, SSH and RDP, then create VMs in two regions. VMs in different regions ping each other over internal IPs because the VPC is global. Without a firewall rule, ping and SSH fail.

```bash
gcloud compute networks create mynetwork --subnet-mode=auto
gcloud compute firewall-rules create mynetwork-allow-icmp-ssh \
  --network=mynetwork --allow=icmp,tcp:22 --source-ranges=0.0.0.0/0
gcloud compute instances create vm-a --zone=$ZONE --network=mynetwork --machine-type=e2-micro
```

## Module 5: Storage in the cloud

Key points:

- **Cloud Storage** is object storage. Objects live in buckets with a globally unique name. Objects are immutable; versioning keeps old copies.
- **Storage classes** (all have the same latency and durability; they differ in price and minimum storage duration):

  | Class | Minimum duration | Use for |
  |---|---|---|
  | Standard | None | Hot data, frequently accessed |
  | Nearline | 30 days | Accessed about once a month, backups |
  | Coldline | 90 days | Accessed about once a quarter |
  | Archive | 365 days | Accessed less than once a year, DR, compliance |

- **Autoclass** moves objects between classes automatically based on access. **Object Lifecycle Management** deletes or changes class on rules (age, versions).
- **Access control:** IAM at bucket level (uniform access, recommended), ACLs for fine-grained per-object access, signed URLs for time-limited access without a Google account.
- **Data transfer:** `gcloud storage cp` online, Storage Transfer Service for large or scheduled transfers from other clouds or HTTP, Transfer Appliance for petabytes offline.
- **Choosing a database:**

  | Service | Type | Pick it when |
  |---|---|---|
  | Cloud SQL | Managed MySQL, PostgreSQL, SQL Server | Relational, regional, up to tens of TB |
  | Spanner | Relational, horizontally scalable | Global scale, strong consistency, high availability |
  | Firestore | NoSQL document | Mobile, web and server apps, offline sync |
  | Bigtable | NoSQL wide-column | Very large, low-latency, high-throughput (IoT, time series, analytics) |
  | BigQuery | Data warehouse | Analytics with SQL over petabytes, not for transactions |

- Cloud SQL handles backups, patching and replication. It can fail over to a standby in another zone (HA) and add read replicas.

Lab takeaway (Cloud Storage and Cloud SQL): you create a bucket, upload an image, make it readable, create a Cloud SQL instance, and point a web server VM at it. The VM's external IP must be added to the Cloud SQL **authorized networks** before it can connect.

```bash
gcloud storage buckets create gs://$PROJECT_ID --location=$REGION
gcloud storage cp my-image.png gs://$PROJECT_ID/
gcloud storage objects update gs://$PROJECT_ID/my-image.png --add-acl-grant=entity=allUsers,role=READER
```

## Module 6: Containers in the cloud

Key points:

- **Containers** package code and dependencies and share the host OS kernel. They start fast and run the same everywhere. VMs virtualize hardware; containers virtualize the OS.
- **Kubernetes** orchestrates containers. Terms: cluster, node, pod (smallest deployable unit, one or more containers), deployment (keeps a set of identical pods running), service (stable IP and load balancing in front of pods). Kubernetes is declarative: you describe the desired state and it reconciles.
- **Google Kubernetes Engine (GKE)** is managed Kubernetes. Two modes:
  - **Autopilot:** Google manages nodes, scaling and security; you pay per pod. Recommended default.
  - **Standard:** you manage node pools and configuration; you pay per node.
- GKE gives auto-upgrade, auto-repair, cluster autoscaling and integration with Cloud Load Balancing and Cloud Logging.
- **Artifact Registry** stores container images; **Cloud Build** builds them.

Lab takeaway (GKE): you create a cluster, run an nginx deployment, expose it with a `LoadBalancer` service, then scale it.

```bash
gcloud container clusters create-auto my-cluster --region=$REGION
gcloud container clusters get-credentials my-cluster --region=$REGION
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80 --type=LoadBalancer
kubectl scale deployment nginx --replicas=3
kubectl get pods,services
```

## Module 7: Applications in the cloud

Key points:

- **Cloud Run** runs stateless containers, fully managed and serverless. Scales to zero, pay only while handling requests. Deploy from a container image or directly from source.
- **Cloud Run functions** (formerly Cloud Functions) run single-purpose code in response to events, such as a file upload to Cloud Storage or a Pub/Sub message, or an HTTP call.
- **App Engine** is the older PaaS: standard environment (sandboxed runtimes, scales to zero) and flexible environment (runs on VMs, does not scale to zero).
- Choose by control vs convenience: Compute Engine (most control) > GKE > Cloud Run > Cloud Run functions (least to manage).

```bash
gcloud run deploy hello --image=us-docker.pkg.dev/cloudrun/container/hello \
  --region=$REGION --allow-unauthenticated
```

## Module 8: Prompt engineering

Key points:

- Generative AI produces new content from a prompt. Large language models are trained on large datasets and then tuned for tasks.
- Good prompts give context, a clear task, the format you want, and examples (few-shot). Zero-shot means no examples.
- Watch for hallucinations: check facts, especially commands and resource names.
- **Gemini for Google Cloud** (Gemini Cloud Assist) helps in the console and Cloud Shell: explaining errors, writing `gcloud` commands and code, and summarizing logs.

## Variables

Each lab gives you its own project, region and zone. Set these first in Cloud Shell:

```bash
export PROJECT_ID=$(gcloud config get-value project)
export REGION=us-east1      # from your lab
export ZONE=us-east1-b      # from your lab
gcloud config set compute/region $REGION
gcloud config set compute/zone $ZONE
```

## Quick recap for the exam

- Hierarchy is org > folder > project > resource. Policies inherit downward, and a grant at a parent cannot be taken away at a child (unless a deny policy is used).
- Project ID is unique and permanent; the project name can change.
- Prefer predefined roles over basic roles. Service accounts are for workloads, not people.
- VPC is global, subnets are regional, VMs and disks are zonal.
- Storage class choice is about access frequency and minimum duration, not speed.
- Relational + regional = Cloud SQL. Relational + global = Spanner. Analytics = BigQuery. High-throughput NoSQL = Bigtable. App documents = Firestore.
- Autopilot GKE for less ops; Standard GKE for node-level control.
- Stateless container with HTTP and scale to zero = Cloud Run. Event-driven snippet = Cloud Run functions.
- Hybrid: HA VPN for quick and encrypted, Dedicated or Partner Interconnect for high bandwidth and an SLA.
