# CLAUDE.md

Step-by-step Google Cloud lab guides and notes from Sadhiq's GCP certification prep. Markdown only, no code to build or test.

## Layout

- `README.md`: certs overview table. Keep it in sync when a cert or week changes.
- `<cert>/` one top-level folder per certification, e.g. `associate-cloud-engineer/`, later `professional-cloud-architect/`.
  - `<cert>/README.md`: skill badge tracker (week, badge, guide link, status).
  - `week-NN-<topic>/README.md`: week index with a module table (`#`, Lab, Lab ID, What you build), shared variables and an exam recap.
  - `week-NN-<topic>/NN-<lab-name>/README.md`: one guide per lab, numbered in course order. The challenge lab is the last folder, `NN-challenge-lab/`.
  - `side-labs/`: labs outside the cohort plan. `notes/`: exam tips.
- `shared/`: material reused across certs. If a lab fits more than one cert, keep the full guide where it was first done and link to it.

## Lab guide format

Every lab README starts with:

```markdown
# Lab N: <Lab title>

<One-line summary of what you build.>

| Lab ID | Direct link |
|---|---|
| GSPxxx | [Open in Google Skills](https://www.skills.google/course_templates/<course>/labs/<lab>) |

> **ACE exam note:** <what the exam tests from this lab>
```

Then `## Task N. <name>` sections that mirror the lab's tasks, each with copy-paste `gcloud` commands in fenced `bash` blocks, an optional **Console path:** line, and a `## Troubleshooting` table at the end.

- Use `$REGION` and `$ZONE` variables; labs assign their own region, zone and resource names, so say so.
- Use `TBD` for any Lab ID or link not yet confirmed. Lab pages need a Google Skills sign-in, so never guess IDs or links. Ask Sadhiq.
- When adding or renaming a lab, update the week index table and the badge tracker.
- Plain, direct prose. No em dashes.

## Git

- Work on a branch and open a PR; `main` is protected and only squash merges are allowed.
- Commits are authored as Sadhiq. Don't add a "Require signed commits" rule: commits signed by a key not on the account would block merging.
- Never run lab `gcloud` commands; this repo documents them only. Be careful with heredocs: lab scripts contain `EOF` lines, so write files with an editor tool, not `cat <<EOF`.
