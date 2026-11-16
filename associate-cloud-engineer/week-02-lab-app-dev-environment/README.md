# Week 2: Set Up an App Dev Environment on Google Cloud

Skill badge [course 637](https://www.skills.google/course_templates/637), "Set Up an App Dev Environment on Google Cloud". Required for week 2. It covers Cloud Storage, Identity and Access Management (IAM), Cloud Run functions and Pub/Sub, and ends with a challenge lab.

> **Note on the lab list:** each lab is added here as I work through it, in course order. Guides follow the lab instructions on Google Skills.

## Modules

| # | Lab | Lab ID | What you build |
|---|---|---|---|
| 1 | [Manage Cloud Storage with the Google Cloud Console](01-cloud-storage-console/) | [GSP073](https://www.skills.google/course_templates/637/labs/592541) | A public bucket with an uploaded image, plus folders created and deleted |
| 2 | [Manage Cloud Storage with the Google Cloud CLI](02-cloud-storage-cli/) | [GSP074](https://www.skills.google/course_templates/637/labs/592542) | The same bucket tasks from Cloud Shell, with one object made public and private again by ACL |
| 3 | [Manage Access Control with IAM](03-iam-access-control/) | [GSP064](https://www.skills.google/course_templates/637/labs/592543) | A second user moved from project Viewer to only Storage Object Viewer |

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
- **One public object** (fine-grained only): `gcloud storage objects update gs://BUCKET/OBJ --add-acl-grant=entity=allUsers,role=READER`, and `--remove-acl-grant=allUsers` to undo it.
- **CLI verbs:** `gcloud storage buckets create`, `cp` (upload, download, or copy between paths), `ls` (`-l` for size and date, `-r` for everything), `rm` (`-r` for a folder).
- **Folders** are name prefixes in a flat namespace. Deleting a folder deletes every object under that prefix.
- **Basic roles:** Browser (browse hierarchy only), Viewer (read-only), Editor (Viewer plus changes), Owner (Editor plus IAM and billing). Managing IAM needs `resourcemanager.projects.setIamPolicy`.
- **Least privilege:** a predefined role such as `roles/storage.objectViewer` gives one service's access without project-wide Viewer.
- **IAM CLI:** `gcloud projects add-iam-policy-binding` and `remove-iam-policy-binding` with `--member=user:EMAIL --role=ROLE`; `get-iam-policy` to read. Changes can take up to 80 seconds to apply.
