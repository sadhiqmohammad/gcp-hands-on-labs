# Week 2: Set Up an App Dev Environment on Google Cloud

Skill badge [course 637](https://www.skills.google/course_templates/637), "Set Up an App Dev Environment on Google Cloud". Required for week 2. It covers Cloud Storage, Identity and Access Management (IAM), Cloud Run functions and Pub/Sub, and ends with a challenge lab.

> **Note on the lab list:** the lab list is behind the Google Skills sign-in, so each lab is added here as I work through it, in course order.

## Modules

| # | Lab | Lab ID | What you build |
|---|---|---|---|

## Variables used in every module

Each lab gives you its own region, zone and resource names. Set these first in Cloud Shell:

```bash
export REGION=us-east1      # from your lab
export ZONE=us-east1-b      # from your lab
gcloud config set compute/region $REGION
gcloud config set compute/zone $ZONE
```

## Quick recap for the exam

Filled in as each lab is completed.
