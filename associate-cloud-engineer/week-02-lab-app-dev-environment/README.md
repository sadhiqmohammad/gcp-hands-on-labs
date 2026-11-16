# Week 2: Set Up an App Dev Environment on Google Cloud

Skill badge [course 637](https://www.skills.google/course_templates/637), "Set Up an App Dev Environment on Google Cloud". Required for week 2. It covers Cloud Storage, Identity and Access Management (IAM), Cloud Run functions and Pub/Sub, and ends with a challenge lab.

> **Note on the lab list:** each lab is added here as I work through it, in course order. Guides follow the lab instructions on Google Skills.

## Modules

| # | Lab | Lab ID | What you build |
|---|---|---|---|
| 1 | [Manage Cloud Storage with the Google Cloud Console](01-cloud-storage-console/) | [GSP073](https://www.skills.google/course_templates/637/labs/592541) | A public bucket with an uploaded image, plus folders created and deleted |

## Variables used in every module

Each lab gives you its own region, zone and resource names. Set these first in Cloud Shell:

```bash
export REGION=us-east1      # from your lab
export ZONE=us-east1-b      # from your lab
gcloud config set compute/region $REGION
gcloud config set compute/zone $ZONE
```

## Quick recap for the exam

More is added as each lab is completed.

- **Bucket names** are global, unique across all of Google Cloud, lowercase, with no spaces.
- **Storage classes:** Standard, Nearline (30-day minimum), Coldline (90-day minimum), Archive (365-day minimum). The class is set per bucket as a default and can be changed per object.
- **Access control:** uniform bucket-level access uses IAM only. Fine-grained also allows per-object ACLs.
- **Public objects:** grant `allUsers` the `roles/storage.objectViewer` role. Public access prevention blocks this when enforced.
- **Folders** are name prefixes in a flat namespace. Deleting a folder deletes every object under that prefix.
