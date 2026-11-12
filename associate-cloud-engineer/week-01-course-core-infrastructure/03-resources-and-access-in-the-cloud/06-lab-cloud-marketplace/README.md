# Lab 3.6: Google Cloud Fundamentals: Getting Started with Cloud Marketplace

Deploy a LAMP stack on a Compute Engine instance in one click from Cloud Marketplace, then open the site it serves.

| Lab ID | Direct link |
|---|---|
| None | [Open in Google Skills](https://www.skills.google/course_templates/60/labs/689558) |

> **ACE exam note:** Cloud Marketplace deploys a ready-made solution, here a VM with its software already installed, without building it by hand. Know that a deployment needs the Compute Engine API and the Infrastructure Manager API, that the result is an ordinary Compute Engine VM, and how to reach it on its external IP.

This lab is part of module 3 of the Core Infrastructure course (Course 60). The lab assigns its own zone (`asia-east1-c` in this run) and names the deployment `lamp-1`, so use what your lab shows.

What the LAMP stack contains:

| Component | Role |
|---|---|
| Linux | Operating system |
| Apache HTTP Server | Web server |
| MySQL | Relational database |
| PHP | Web application framework |
| phpMyAdmin | PHP administration tool |

## Task 1. Sign in to the Google Cloud console

1. Click **Start Lab**, then **Open Google Cloud console**. In Chrome, you can right-click and open it in an Incognito window.
2. Sign in with the **Username** and **Password** from the **Lab setup and access** panel. If **Choose an account** appears, click **Use Another Account**.
3. Accept the terms. Don't add recovery options or two-factor authentication, and don't sign up for free trials.

> Use only the lab's temporary credentials, never your own Google Cloud account, which may incur charges.

Set the zone the lab gives you, for the gcloud commands later:

```bash
export ZONE=asia-east1-c   # use the zone your lab assigns
gcloud config set compute/zone $ZONE
```

## Task 2. Use Cloud Marketplace to deploy a LAMP stack

**Console path:** **Navigation menu > Marketplace**

1. In the search bar, type `LAMP` and press **Enter**.
2. Click **LAMP Stack Server on Ubuntu 26.04**, by **Cloud Infrastructure Services**. Another LAMP stack may not match the lab steps.
3. Click **GET STARTED**.
4. On the **Agreements** page, tick **Terms and agreements** and click **AGREE**.
5. In the **Successfully agreed to terms** pop-up, click **DEPLOY**.
6. If prompted, click **Enable** for the **Compute Engine API** and the **Infrastructure Manager API**.
7. Set:

   | Field | Value |
   |---|---|
   | Zone | `asia-east1-c` (or the lab's zone) |
   | Series | `E2` |
   | Machine type | `e2-medium` |

8. Leave the other settings as defaults and click **Deploy**. Warnings may appear during deployment; ignore them for this lab.

The status shows **lamp-1 is being deployed**, then **lamp-1 has been deployed**. After the software installs, a summary of the instance appears, including the site address.

If the APIs aren't enabled yet, you can also enable them from Cloud Shell before deploying:

```bash
gcloud services enable compute.googleapis.com config.googleapis.com
```

Click **Check my progress** for **Use Cloud Marketplace to deploy a LAMP stack**.

## Task 3. Verify your deployment

1. When the deployment is complete, click **lamp-1-vm > Details**.
2. Click the **Site Url** link. If the site doesn't respond, wait 30 seconds and try again. If a redirection notice appears, click its link.
3. A new tab shows a congratulations message, which confirms that the Apache HTTP Server is running.
4. To see the deployment welcome message, go to the **Network interfaces** section, copy the **External IP**, and paste it into a new browser tab.

Check the VM and its external IP from Cloud Shell:

```bash
gcloud compute instances list

gcloud compute instances describe lamp-1-vm --zone=$ZONE \
  --format='get(networkInterfaces[0].accessConfigs[0].natIP)'

curl -I http://$(gcloud compute instances describe lamp-1-vm --zone=$ZONE \
  --format='get(networkInterfaces[0].accessConfigs[0].natIP)')
```

## End your lab

Click **End Lab**. Google Skills removes the resources and cleans the account. Rate the lab if you want to, or close the dialog.

## Troubleshooting

| Symptom | Fix |
|---|---|
| Deploy asks to enable APIs | Click **Enable** for the Compute Engine API and the Infrastructure Manager API. |
| Lab steps don't match the deployment form | Pick **LAMP Stack Server on Ubuntu 26.04** by Cloud Infrastructure Services, not another LAMP listing. |
| Warnings during deployment | Expected for this lab; ignore them. |
| Site Url doesn't respond | Wait 30 seconds and retry. Follow any redirection notice link. |
| Page won't load on a corporate laptop | Leave the corporate VPN or network if you can, or open the external IP on another device such as a phone or tablet. |
| Check my progress fails | Make sure the status reads **lamp-1 has been deployed** and the zone and machine type match the lab. |
