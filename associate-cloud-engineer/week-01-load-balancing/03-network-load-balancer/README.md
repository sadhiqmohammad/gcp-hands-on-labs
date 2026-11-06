# Lab 3: Set Up a Network Load Balancer

Three Apache VMs behind a regional passthrough (layer 4) Network Load Balancer.

> **ACE exam note:** a passthrough NLB is regional, works at layer 4, and keeps the client IP. Target pools need a **legacy** HTTP health check.

## Task 1. Create three web server VMs

```bash
for i in 1 2 3; do
gcloud compute instances create www$i \
  --zone=$ZONE \
  --machine-type=e2-small \
  --tags=network-lb-tag \
  --image-family=debian-12 \
  --image-project=debian-cloud \
  --metadata=startup-script="#!/bin/bash
apt-get update
apt-get install apache2 -y
service apache2 restart
echo \"<h3>Web Server: www$i</h3>\" | tee /var/www/html/index.html"
done

gcloud compute firewall-rules create www-firewall-network-lb \
  --target-tags=network-lb-tag \
  --allow=tcp:80

gcloud compute instances list
```

Check one VM directly (wait a minute for the startup script):

```bash
curl http://$(gcloud compute instances describe www1 --zone=$ZONE \
  --format='get(networkInterfaces[0].accessConfigs[0].natIP)')
```

**Console path:** Compute Engine > VM instances > Create instance. Set network tag `network-lb-tag` under Networking and paste the startup script under Management > Automation.

## Task 2. Configure the load balancer

```bash
# Static regional IP
gcloud compute addresses create network-lb-ip-1 --region=$REGION

# Legacy HTTP health check
gcloud compute http-health-checks create basic-check

# Target pool with the three VMs
gcloud compute target-pools create www-pool \
  --region=$REGION \
  --http-health-check=basic-check

gcloud compute target-pools add-instances www-pool \
  --instances=www1,www2,www3 \
  --instances-zone=$ZONE

# Forwarding rule: IP:80 -> target pool
gcloud compute forwarding-rules create www-rule \
  --region=$REGION \
  --ports=80 \
  --address=network-lb-ip-1 \
  --target-pool=www-pool
```

> Newer versions of this lab may use a **backend service** instead of a target pool: a regional health check, an unmanaged instance group with the VMs, `gcloud compute backend-services create ... --load-balancing-scheme=EXTERNAL --protocol=TCP --region=$REGION`, and a forwarding rule with `--backend-service`. Follow your lab text.

## Task 3. Send traffic

```bash
gcloud compute forwarding-rules describe www-rule --region=$REGION
IPADDRESS=$(gcloud compute forwarding-rules describe www-rule \
  --region=$REGION --format='value(IPAddress)')
while true; do curl -m1 $IPADDRESS; sleep 1; done   # Ctrl+C to stop
```

Responses should rotate between www1, www2 and www3.

## Troubleshooting

| Symptom | Fix |
|---|---|
| `target-pools create` rejects the health check | Use `http-health-checks` (legacy), not `health-checks`. |
| `curl` to VMs hangs | Firewall rule missing or VM not tagged `network-lb-tag`. |
| LB returns nothing | Startup script still running. Wait a minute. |
