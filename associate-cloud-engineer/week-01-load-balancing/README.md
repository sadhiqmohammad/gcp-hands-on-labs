# Week 1: Implement Load Balancing on Compute Engine

Skill badge course 648, "Implementing Cloud Load Balancing for Compute Engine". It covers creating VMs in Compute Engine and putting network and application load balancers in front of them.

> **Note on the lab list:** the course's lab list sits behind the Google Skills sign-in, so the modules below follow the standard labs for this badge. If your course shows different labs, rename or add folders to match.

## Modules

| # | Lab | What you build |
|---|---|---|
| 1 | [Create a Virtual Machine](01-create-a-virtual-machine/) | A VM from the console and from gcloud, with NGINX |
| 2 | [Getting Started with Cloud Shell and gcloud](02-cloud-shell-and-gcloud/) | gcloud config, filtering, SSH, firewall basics |
| 3 | [Set Up a Network Load Balancer](03-network-load-balancer/) | 3 Apache VMs behind a regional layer 4 LB |
| 4 | [Set Up an Application Load Balancer](04-application-load-balancer/) | MIG + global layer 7 HTTP LB |
| 5 | [Challenge Lab](05-challenge-lab/) | To be added |

## Variables used in every module

Each lab gives you its own region, zone and resource names. Set these first in Cloud Shell:

```bash
export REGION=us-east1      # from your lab
export ZONE=us-east1-b      # from your lab
gcloud config set compute/region $REGION
gcloud config set compute/zone $ZONE
```

## Quick recap for the exam

- **Network LB (passthrough):** regional, layer 4, keeps client IP, target pool or regional backend service.
- **Application LB:** global, layer 7. Chain is forwarding rule > target HTTP(S) proxy > URL map > backend service > instance group, with a health check on the backend service.
- **MIG + instance template** gives autohealing and autoscaling. A named port maps the backend service's `port-name` to a real port.
- Health checks come from `130.211.0.0/22` and `35.191.0.0/16`, so a firewall rule must allow them.
