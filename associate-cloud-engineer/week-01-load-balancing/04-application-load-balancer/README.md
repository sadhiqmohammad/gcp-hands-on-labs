# Lab 4: Set Up an Application Load Balancer

A managed instance group from a template, behind a global external Application (HTTP) Load Balancer.

> **ACE exam note:** know the chain: forwarding rule > target HTTP proxy > URL map > backend service > instance group, plus health check and the health-checker firewall ranges.

## Task 1. Instance template

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

## Task 2. Managed instance group

```bash
gcloud compute instance-groups managed create lb-backend-group \
  --template=lb-backend-template \
  --size=2 \
  --zone=$ZONE

gcloud compute instance-groups set-named-ports lb-backend-group \
  --named-ports=http:80 \
  --zone=$ZONE
```

## Task 3. Firewall for Google health checkers

```bash
gcloud compute firewall-rules create fw-allow-health-check \
  --network=default \
  --action=allow \
  --direction=ingress \
  --source-ranges=130.211.0.0/22,35.191.0.0/16 \
  --target-tags=allow-health-check \
  --rules=tcp:80
```

## Task 4. Global static IP

```bash
gcloud compute addresses create lb-ipv4-1 --ip-version=IPV4 --global
gcloud compute addresses describe lb-ipv4-1 --global --format='get(address)'
```

## Task 5. Health check, backend service, URL map, proxy, forwarding rule

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

**Console path:** Network services > Load balancing > Create load balancer > Application Load Balancer (HTTP/S) > Public facing > Global. Backend `web-backend-service` with `lb-backend-group`, port 80, health check `http-basic-check`. Frontend HTTP, IP `lb-ipv4-1`, port 80.

## Task 6. Test

Global LBs take **3 to 10 minutes** to start serving. A 404 or 502 until then is normal.

```bash
gcloud compute backend-services get-health web-backend-service --global
LB_IP=$(gcloud compute addresses describe lb-ipv4-1 --global --format='get(address)')
curl http://$LB_IP      # "Page served from: lb-backend-group-xxxx"
```

## Troubleshooting

| Symptom | Fix |
|---|---|
| Backends `UNHEALTHY` | `fw-allow-health-check` missing, wrong tag on the template, or startup script still running. |
| 404/502 from the LB | Still provisioning. Wait 5 to 10 minutes. |
| Need to change the template | Templates are immutable. Create a new one and update the MIG. |
