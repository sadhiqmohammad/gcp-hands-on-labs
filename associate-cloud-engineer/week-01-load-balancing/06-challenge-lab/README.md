# Lab 6: Implement Load Balancing on Compute Engine: Challenge Lab

Three web VMs behind a regional Network Load Balancer, then a managed instance group behind a global Application Load Balancer, with no step-by-step instructions.

| Lab ID | Direct link |
|---|---|
| GSP313 | [Open in Google Skills](https://www.skills.google/course_templates/648/labs/613025) |

> **ACE exam note:** this lab tests both LB types side by side. Know which one is regional or global, layer 4 or layer 7, and which health check each needs: a **legacy** HTTP health check for a target pool, a regular health check for a backend service.

This lab combines [Lab 3](../03-network-load-balancer/) and [Lab 4](../04-application-load-balancer/). The challenge checker looks for **exact resource names**, so compare every name below with your lab's task list before you run anything. Your lab also gives you its own region and zone.

## Set variables

```bash
export REGION=us-east1      # from your lab
export ZONE=us-east1-b      # from your lab
gcloud config set compute/region $REGION
gcloud config set compute/zone $ZONE
```

## Task 1. Create multiple web server instances

Three VMs, `web1`, `web2` and `web3`, with tag `network-lb-tag`, each serving its own name over Apache.

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

gcloud compute firewall-rules create www-firewall-network-lb \
  --network=default \
  --target-tags=network-lb-tag \
  --allow=tcp:80

gcloud compute instances list
```

If your lab names a different image (for example `debian-11`), use that one.

Check one VM directly (wait a minute for the startup script):

```bash
curl http://$(gcloud compute instances describe web1 --zone=$ZONE \
  --format='get(networkInterfaces[0].accessConfigs[0].natIP)')
```

## Task 2. Configure the load balancing service

A regional passthrough Network Load Balancer: static IP, legacy health check, target pool, forwarding rule.

```bash
gcloud compute addresses create network-lb-ip-1 --region=$REGION

gcloud compute http-health-checks create basic-check

gcloud compute target-pools create www-pool \
  --region=$REGION \
  --http-health-check=basic-check

gcloud compute target-pools add-instances www-pool \
  --instances=web1,web2,web3 \
  --instances-zone=$ZONE

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
while true; do curl -m1 $IPADDRESS; sleep 1; done   # Ctrl+C to stop
```

Click **Check my progress** for Task 2 before moving on.

## Task 3. Create an HTTP load balancer

A managed instance group from a template, behind a global external Application Load Balancer.

### 3.1 Instance template and managed instance group

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

gcloud compute instance-groups managed create lb-backend-group \
  --template=lb-backend-template \
  --size=2 \
  --zone=$ZONE

gcloud compute instance-groups set-named-ports lb-backend-group \
  --named-ports=http:80 \
  --zone=$ZONE
```

### 3.2 Firewall for Google health checkers

```bash
gcloud compute firewall-rules create fw-allow-health-check \
  --network=default \
  --action=allow \
  --direction=ingress \
  --source-ranges=130.211.0.0/22,35.191.0.0/16 \
  --target-tags=allow-health-check \
  --rules=tcp:80
```

### 3.3 Global IP, health check, backend service

```bash
gcloud compute addresses create lb-ipv4-1 --ip-version=IPV4 --global

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
```

### 3.4 URL map, proxy, forwarding rule

```bash
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

**Console path:** Network services > Load balancing > Create load balancer > Application Load Balancer (HTTP/S) > Public facing > Global. Backend `web-backend-service` with `lb-backend-group`, port 80, health check `http-basic-check`. Frontend HTTP, IP `lb-ipv4-1`, port 80.

### 3.5 Test

Global LBs take **3 to 10 minutes** to start serving. A 404 or 502 until then is normal, and the Task 3 check can fail until the LB answers.

```bash
gcloud compute backend-services get-health web-backend-service --global
LB_IP=$(gcloud compute addresses describe lb-ipv4-1 --global --format='get(address)')
curl http://$LB_IP      # "Page served from: lb-backend-group-xxxx"
```

## Troubleshooting

| Symptom | Fix |
|---|---|
| Check my progress fails but the resources exist | A name doesn't match the lab text. Delete the resource and recreate it with the exact name. |
| `target-pools create` rejects the health check | Use `http-health-checks` (legacy), not `health-checks`. |
| Network LB returns nothing | Startup scripts still running, or `www-firewall-network-lb` missing or not targeting `network-lb-tag`. |
| HTTP LB backends `UNHEALTHY` | `fw-allow-health-check` missing, wrong tag on the template, or startup script still running. |
| 404/502 from the HTTP LB | Still provisioning. Wait 5 to 10 minutes, then check again. |
| Resources created in the wrong region or zone | Recreate them in the region and zone the lab assigned; the checker won't find them elsewhere. |
