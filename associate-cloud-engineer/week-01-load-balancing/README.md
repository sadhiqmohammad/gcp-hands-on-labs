# Implement Load Balancing on Compute Engine: Challenge Lab (step by step)

Skill badge course 648, "Implementing Cloud Load Balancing for Compute Engine". This is the **week 1 badge** in the ACE15 cohort and one of the 5 badges for the voucher.

> **What was assumed:** the lab page (lab 613023) sits behind the Google Skills sign-in, so its text could not be read. The course page confirms the course is the Compute Engine load balancing skill badge, and its only graded lab is the challenge lab (classically **GSP313**). This guide follows that lab's three tasks. **The lab gives you its own names, region and zone** on the left panel and in the task text. Set the variables in step 0 to those values before running anything. If a check fails, the first thing to compare is the resource name.

> **ACE exam note:** this lab is core ACE material. Know the difference between a **regional passthrough Network LB** (layer 4, target pool or backend service, regional IP) and a **global external Application LB** (layer 7, URL map, target HTTP proxy, global IP), and why the health-check firewall ranges `130.211.0.0/22` and `35.191.0.0/16` matter.

---

## 0. Open Cloud Shell and set variables

Click **Activate Cloud Shell** in the console top bar, then:

```bash
# Replace with the values shown in YOUR lab instructions
export REGION=us-east1
export ZONE=us-east1-b
export FW_RULE=www-firewall-network-lb     # firewall name the lab asks for

gcloud config set compute/region $REGION
gcloud config set compute/zone $ZONE
gcloud config list
```

`gcloud config list` should show the lab's project, region and zone.

---

## Task 1. Create multiple web server instances

Three VMs running Apache, each serving a page that shows its own name, all tagged `network-lb-tag`.

```bash
for i in 1 2 3; do
gcloud compute instances create web$i \
  --zone=$ZONE \
  --machine-type=e2-small \
  --tags=network-lb-tag \
  --image-family=debian-12 \
  --image-project=debian-cloud \
  --metadata=startup-script="#!/bin/bash
apt-get update
apt-get install apache2 -y
service apache2 restart
echo \"<h3>Web Server: web$i</h3>\" | tee /var/www/html/index.html"
done
```

Allow HTTP to those tagged VMs:

```bash
gcloud compute firewall-rules create $FW_RULE \
  --target-tags=network-lb-tag \
  --allow=tcp:80
```

Check that each VM serves its page (wait about a minute for the startup script):

```bash
gcloud compute instances list
curl http://$(gcloud compute instances describe web1 --zone=$ZONE \
  --format='get(networkInterfaces[0].accessConfigs[0].natIP)')
```

**Console path:** Compute Engine > VM instances > Create instance. Set name, zone, `e2-small`, Debian 12 boot disk, then Advanced options > Networking > Network tags `network-lb-tag`, and Management > Automation > Startup script. Firewall: VPC network > Firewall > Create firewall rule.

Click **Check my progress** for Task 1.

---

## Task 2. Configure the Network Load Balancer (layer 4, regional)

Static regional IP, legacy HTTP health check, target pool with the three VMs, and a forwarding rule on port 80.

```bash
# 1. Static external IP for the LB
gcloud compute addresses create network-lb-ip-1 --region=$REGION

# 2. Legacy HTTP health check (target pools need the legacy type)
gcloud compute http-health-checks create basic-check

# 3. Target pool using that health check
gcloud compute target-pools create www-pool \
  --region=$REGION \
  --http-health-check=basic-check

# 4. Add the instances
gcloud compute target-pools add-instances www-pool \
  --instances=web1,web2,web3 \
  --instances-zone=$ZONE

# 5. Forwarding rule: IP:80 -> target pool
gcloud compute forwarding-rules create www-rule \
  --region=$REGION \
  --ports=80 \
  --address=network-lb-ip-1 \
  --target-pool=www-pool
```

Test it. Responses should rotate between web1, web2 and web3:

```bash
IPADDRESS=$(gcloud compute forwarding-rules describe www-rule \
  --region=$REGION --format='value(IPAddress)')
echo $IPADDRESS
while true; do curl -m2 $IPADDRESS; sleep 1; done   # Ctrl+C to stop
```

Click **Check my progress** for Task 2.

> If your lab text says to use a **backend service** instead of a target pool for the network LB, the equivalent is: a regional health check (`gcloud compute health-checks create http ... --region=$REGION`), an unmanaged instance group holding web1-3, `gcloud compute backend-services create ... --load-balancing-scheme=EXTERNAL --protocol=TCP --region=$REGION`, then a forwarding rule with `--backend-service`. Follow whatever the lab text says.

---

## Task 3. Create an HTTP(S) Application Load Balancer (layer 7, global)

Managed instance group from a template, health-check firewall, global IP, backend service, URL map, HTTP proxy, forwarding rule.

### 3.1 Instance template

```bash
gcloud compute instance-templates create lb-backend-template \
  --region=$REGION \
  --network=default \
  --subnet=default \
  --tags=allow-health-check \
  --machine-type=e2-medium \
  --image-family=debian-12 \
  --image-project=debian-cloud \
  --metadata=startup-script='#!/bin/bash
apt-get update
apt-get install apache2 -y
a2ensite default-ssl
a2enmod ssl
vm_hostname="$(curl -H "Metadata-Flavor:Google" \
  http://169.254.169.254/computeMetadata/v1/instance/name)"
echo "Page served from: $vm_hostname" | tee /var/www/html/index.html
systemctl restart apache2'
```

### 3.2 Managed instance group (2 VMs)

```bash
gcloud compute instance-groups managed create lb-backend-group \
  --template=lb-backend-template \
  --size=2 \
  --zone=$ZONE

# Name port 80 "http" so the backend service can use it
gcloud compute instance-groups set-named-ports lb-backend-group \
  --named-ports=http:80 \
  --zone=$ZONE
```

### 3.3 Firewall for Google health checkers

```bash
gcloud compute firewall-rules create fw-allow-health-check \
  --network=default \
  --action=allow \
  --direction=ingress \
  --source-ranges=130.211.0.0/22,35.191.0.0/16 \
  --target-tags=allow-health-check \
  --rules=tcp:80
```

### 3.4 Global static IP

```bash
gcloud compute addresses create lb-ipv4-1 \
  --ip-version=IPV4 \
  --global

gcloud compute addresses describe lb-ipv4-1 --global --format='get(address)'
```

### 3.5 Health check, backend service, URL map, proxy, forwarding rule

```bash
gcloud compute health-checks create http http-basic-check --port=80

gcloud compute backend-services create web-backend-service \
  --protocol=HTTP \
  --port-name=http \
  --health-checks=http-basic-check \
  --global

gcloud compute backend-services add-backend web-backend-service \
  --instance-group=lb-backend-group \
  --instance-group-zone=$ZONE \
  --global

gcloud compute url-maps create web-map-http \
  --default-service=web-backend-service

gcloud compute target-http-proxies create http-lb-proxy \
  --url-map=web-map-http

gcloud compute forwarding-rules create http-content-rule \
  --address=lb-ipv4-1 \
  --global \
  --target-http-proxy=http-lb-proxy \
  --ports=80
```

### 3.6 Test

Global LBs take **3 to 10 minutes** to start serving. Until then you get 404 or 502, which is normal.

```bash
gcloud compute backend-services get-health web-backend-service --global
LB_IP=$(gcloud compute addresses describe lb-ipv4-1 --global --format='get(address)')
curl http://$LB_IP      # "Page served from: lb-backend-group-xxxx"
```

**Console path:** Network services > Load balancing > Create load balancer > Application Load Balancer (HTTP/S) > Public facing > Global. Backend: `web-backend-service` with `lb-backend-group`, port 80, health check `http-basic-check`. Frontend: HTTP, IP `lb-ipv4-1`, port 80.

Click **Check my progress** for Task 3. If it fails right away, wait a few minutes and check again; the grader often needs the LB to be serving.

---

## Troubleshooting

| Symptom | Likely cause / fix |
|---|---|
| Check fails though resource exists | Name, region or zone differs from the lab text. `gcloud compute ... list` and compare. |
| `curl` to web VMs hangs | Firewall rule missing or VM not tagged `network-lb-tag`. |
| Backends `UNHEALTHY` | `fw-allow-health-check` missing, wrong tag on the template, or startup script still running. |
| 404/502 from HTTP LB | Still provisioning. Wait 5 to 10 minutes. |
| `target-pools create` rejects health check | Target pools need a **legacy** check (`http-health-checks`), not `health-checks`. |
| Template has wrong values | Templates are immutable. Create a new one or delete the MIG and template and redo. |

## Quick recap for the exam

- **Network LB (passthrough):** regional, layer 4, keeps client IP, target pool or regional backend service.
- **Application LB:** global, layer 7, chain is forwarding rule > target HTTP(S) proxy > URL map > backend service > instance group, with a health check on the backend service.
- **MIG + instance template** gives autohealing and autoscaling; named port maps the backend service's `port-name` to a real port.
- Health checks come from `130.211.0.0/22` and `35.191.0.0/16`, so the firewall must allow them.
