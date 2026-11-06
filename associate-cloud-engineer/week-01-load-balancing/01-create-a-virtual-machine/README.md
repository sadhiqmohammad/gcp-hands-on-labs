# Lab 1: Create a Virtual Machine

Create a VM in the console and another with gcloud, install NGINX, and reach it over HTTP.

> **ACE exam note:** know machine families (E2, N2, C3), regions vs zones, boot disk images, network tags, and `--tags=http-server` + the default-allow-http firewall rule.

## Task 1. Set region and zone

```bash
gcloud config set compute/region $REGION
gcloud config set compute/zone $ZONE
gcloud config list
```

## Task 2. Create a VM from the console

1. **Navigation menu > Compute Engine > VM instances > Create instance.**
2. Name `gcelab`, region/zone from the lab, machine type `e2-medium`.
3. **OS and storage:** Debian GNU/Linux 12 (bookworm), 10 GB balanced persistent disk.
4. **Networking:** tick **Allow HTTP traffic** (adds tag `http-server` and the firewall rule).
5. Click **Create**.

Same thing with gcloud:

```bash
gcloud compute instances create gcelab \
  --zone=$ZONE \
  --machine-type=e2-medium \
  --image-family=debian-12 \
  --image-project=debian-cloud \
  --tags=http-server

gcloud compute firewall-rules create default-allow-http \
  --network=default --allow=tcp:80 --target-tags=http-server
```

## Task 3. Install NGINX

SSH to the VM (console **SSH** button or below), then install:

```bash
gcloud compute ssh gcelab --zone=$ZONE

# on the VM
sudo apt-get update
sudo apt-get install -y nginx
ps auwx | grep nginx
exit
```

Open the VM's **External IP** in a browser, or:

```bash
curl http://$(gcloud compute instances describe gcelab --zone=$ZONE \
  --format='get(networkInterfaces[0].accessConfigs[0].natIP)')
```

You should see "Welcome to nginx!".

## Task 4. Create a second VM with gcloud

```bash
gcloud compute instances create gcelab2 --machine-type=e2-medium --zone=$ZONE
gcloud compute instances list
gcloud compute ssh gcelab2 --zone=$ZONE    # press Enter for no passphrase, then exit
```

## Troubleshooting

| Symptom | Fix |
|---|---|
| Browser can't reach NGINX | "Allow HTTP traffic" not ticked, or firewall rule missing. |
| `gcloud compute ssh` asks for a zone | Pass `--zone` or set `compute/zone`. |
