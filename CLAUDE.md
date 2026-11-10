# CLAUDE.md

Step-by-step Google Cloud lab guides and notes from Sadhiq's GCP certification prep. Markdown only, no code to build or test.

## Layout

- `README.md`: certs overview table. Keep it in sync when a cert or week changes.
- `<cert>/` one top-level folder per certification, e.g. `associate-cloud-engineer/`, later `professional-cloud-architect/`.
  - `<cert>/README.md`: tracker (week, type, name, guide link, status). Type is Lab or Course.
  - Two kinds of week folder, named by type:
    - `week-NN-lab-<topic>/`: a skill badge made of labs. `README.md` is the week index with a lab table (`#`, Lab, Lab ID, What you build), shared variables and an exam recap. `NN-<lab-name>/README.md` is one guide per lab, numbered in course order. The challenge lab is the last folder, `NN-challenge-lab/`.
    - `week-NN-course-<topic>/`: a course made of modules. No Lab IDs. `README.md` is the course index with a module table (`#`, Module, Sections, Notes, Status). `NN-<module-name>/README.md` holds one module's notes, with one `## Section N.M <title>` per course section listing its key concepts.
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
- When adding or renaming a lab, update the week index table and the tracker.
- Plain, direct prose. No em dashes.

## Course notes format

Course module notes use only content from the official course. Never fill module titles, sections or concepts from memory or general knowledge; the course outline needs a Google Skills sign-in, so use `TBD` and ask Sadhiq to paste the course text. The template is in `associate-cloud-engineer/week-01-course-core-infrastructure/README.md`. When adding a module, update the course index table.

## Git

- Work on a branch and open a PR; `main` is protected and only squash merges are allowed.
- Commits are authored as Sadhiq. Don't add a "Require signed commits" rule: commits signed by a key not on the account would block merging.
- Never run lab `gcloud` commands; this repo documents them only. Be careful with heredocs: lab scripts contain `EOF` lines, so write files with an editor tool, not `cat <<EOF`.
