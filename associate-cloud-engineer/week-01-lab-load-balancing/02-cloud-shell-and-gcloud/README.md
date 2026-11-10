# Lab 2: Getting Started with Cloud Shell and gcloud

Practice gcloud configuration, listing and filtering resources, SSH, and firewall rules.

| Lab ID | Direct link |
|---|---|
| TBD | TBD |

> **ACE exam note:** `gcloud config` and configurations, `--filter` and `--format`, and the difference between `gcloud compute ssh` and IAP are all fair game.

## Task 1. Configure your environment

```bash
gcloud config set compute/region $REGION
gcloud config set compute/zone $ZONE
gcloud config get-value compute/zone
gcloud config get-value project

export PROJECT_ID=$(gcloud config get-value project)
gcloud compute project-info describe --project $PROJECT_ID
```

## Task 2. Create a VM with gcloud

```bash
gcloud compute instances create gcelab2 \
  --machine-type=e2-medium \
  --zone=$ZONE

gcloud compute instances create --help    # see all flags
```

## Task 3. Explore gcloud commands

```bash
gcloud -h
gcloud config --help
gcloud config list
gcloud config list --all
gcloud components list
```

## Task 4. Filter and format output

```bash
gcloud compute instances list
gcloud compute instances list --filter="name=('gcelab2')"
gcloud compute firewall-rules list
gcloud compute firewall-rules list --filter="network='default'"
gcloud compute firewall-rules list --filter="NETWORK:'default' AND ALLOW:'icmp'"
gcloud compute instances list --format="table(name,zone,status,networkInterfaces[0].accessConfigs[0].natIP)"
```

## Task 5. Connect to the VM

```bash
gcloud compute ssh gcelab2 --zone=$ZONE

# on the VM
sudo apt install -y nginx
exit
```

## Task 6. Update the firewall to allow HTTP

```bash
gcloud compute instances add-tags gcelab2 --tags=http-server,https-server

gcloud compute firewall-rules create default-allow-http \
  --direction=INGRESS --priority=1000 --network=default \
  --action=ALLOW --rules=tcp:80 \
  --source-ranges=0.0.0.0/0 --target-tags=http-server

gcloud compute firewall-rules list --filter=ALLOW:'80'

curl http://$(gcloud compute instances list --filter=name:gcelab2 \
  --format='value(EXTERNAL_IP)')
```

## Task 7. View system logs

```bash
gcloud logging logs list
gcloud logging logs list --filter="compute"
gcloud logging read "resource.type=gce_instance" --limit 5
gcloud logging read "resource.type=gce_instance AND labels.instance_name='gcelab2'" --limit 5
```

## Troubleshooting

| Symptom | Fix |
|---|---|
| `curl` hangs | Tag not added or firewall rule missing. Check `gcloud compute instances describe gcelab2 --format='get(tags.items)'`. |
| Filter returns nothing | Field names are case sensitive in some filters; quote values. |
