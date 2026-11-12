# Week 1 course: Google Cloud Fundamentals: Core Infrastructure

Course notes for later review. This is a course, not a lab: it has modules instead of labs, and modules have no Lab ID. Each module has sections, and each section covers key Google Cloud concepts.

| Course | Direct link |
|---|---|
| Course 60 | [Open in Google Skills](https://www.skills.google/course_templates/60) |

> **Source rule:** notes here come only from the official course videos, published on the [Qwiklabs courses YouTube channel](https://www.youtube.com/@qwiklabs-courses2043). Each section's notes are summarised from that video's transcript and link to the video. Module and section titles stay `TBD` until a video is added.

## Modules

One heading per module, linked to its notes. Each section title links to its notes, and the video link shows its length.

### Module 1: [Course Introduction](01-course-introduction/README.md)

| Section | Video |
|---|---|
| 1.1 [Course Introduction](01-course-introduction/README.md#section-11-course-introduction) | [▶ 2:01](https://youtu.be/xVmam01f4Fw) |

### Module 2: [Introducing Google Cloud](02-introducing-google-cloud/README.md)

| Section | Video |
|---|---|
| 2.1 [Cloud computing overview](02-introducing-google-cloud/README.md#section-21-cloud-computing-overview) | [▶ 3:18](https://www.youtube.com/watch?v=ph5hjgOAf40) |
| 2.2 [IaaS and PaaS](02-introducing-google-cloud/README.md#section-22-iaas-and-paas) | [▶ 2:28](https://youtu.be/C7cb6kFhNmw) |
| 2.3 [The Google Cloud network](02-introducing-google-cloud/README.md#section-23-the-google-cloud-network) | [▶ 2:56](https://youtu.be/SDGJqKIE5yY) |
| 2.4 [Environmental impact](02-introducing-google-cloud/README.md#section-24-environmental-impact) | [▶ 1:30](https://youtu.be/MwZiq-DRGFo) |
| 2.5 [Security](02-introducing-google-cloud/README.md#section-25-security) | [▶ 5:41](https://youtu.be/BggWZl8qTzk) |
| 2.6 [Open source ecosystems](02-introducing-google-cloud/README.md#section-26-open-source-ecosystems) | [▶ 0:54](https://youtu.be/gYZGSrNffF8) |
| 2.7 [Pricing and billing](02-introducing-google-cloud/README.md#section-27-pricing-and-billing) | [▶ 3:16](https://youtu.be/OTD61ZfnO28) |
| 2.8 [Quiz](02-introducing-google-cloud/README.md#section-28-quiz) | No video |

### Module 3: [Resources and Access in the Cloud](03-resources-and-access-in-the-cloud/README.md)

| Section | Video |
|---|---|
| 3.1 [Google Cloud resource hierarchy](03-resources-and-access-in-the-cloud/README.md#section-31-google-cloud-resource-hierarchy) | [▶ 5:28](https://youtu.be/ecVv1L_TEo8) |
| 3.2 [Identity and Access Management (IAM)](03-resources-and-access-in-the-cloud/README.md#section-32-identity-and-access-management-iam) | [▶ 4:22](https://youtu.be/Di1T4RyO9yg) |
| 3.3 [Service accounts](03-resources-and-access-in-the-cloud/README.md#section-33-service-accounts) | [▶ 1:38](https://youtu.be/xoo5NfLqePY) |
| 3.4 [Cloud Identity](03-resources-and-access-in-the-cloud/README.md#section-34-cloud-identity) | [▶ 1:17](https://youtu.be/EZccX9nFaiI) |
| 3.5 [Interacting with Google Cloud](03-resources-and-access-in-the-cloud/README.md#section-35-interacting-with-google-cloud) | [▶ 3:40](https://youtu.be/lkchDTTMrZ8) |
| 3.6 [Lab: Getting Started with Cloud Marketplace](03-resources-and-access-in-the-cloud/06-lab-cloud-marketplace/README.md) | Lab, no video |
| 3.7 [Quiz](03-resources-and-access-in-the-cloud/README.md#section-37-quiz) | No video |

Add one row per section video, in course order. Start a new `### Module N` heading and table for each new module.

## Module folder layout

Each module gets its own folder, numbered in course order:

```text
week-01-course-core-infrastructure/
  README.md                  # this index
  01-<module-name>/README.md
  02-<module-name>/README.md
```

## Module notes template

```markdown
# Module N: <Module title>

<One-line summary of what the module covers, from the course.>

## Section N.1 <Section title>

Video: [<video title>](https://www.youtube.com/watch?v=<id>)

Key concepts:

- **<Concept>:** <what the course says about it>

## Section N.2 <Section title>

Video: [<video title>](https://www.youtube.com/watch?v=<id>)

Key concepts:

- **<Concept>:** <what the course says about it>

## Review questions

- <Quiz topics from the module, in your own words>
```
