---
name: dockerfile-labels
description: >-
  Enforce that every Dockerfile in the project contains the required LABEL
  directives: org (organisation name) and team (owning team). Use when the user
  asks to check, lint, or enforce Dockerfile labelling standards, or when
  reviewing a Dockerfile.
---

# Dockerfile Label Enforcement

All Dockerfiles **must** declare the following labels:

| Label  | Purpose                                         | Example                    |
|--------|-------------------------------------------------|----------------------------|
| `org`  | Organisation that owns the image                | `org=base2cloud`           |
| `team` | Team responsible for maintaining the image      | `team=platform-engineering` |

## How to enforce

1. Find every `Dockerfile*` in the project:
   ```bash
   find . -name 'Dockerfile*' -not -path '*/.git/*'
   ```

2. For each file, check that both labels are present:
   ```bash
   grep -E '^LABEL\s' <Dockerfile>
   ```
   A file **passes** when a `LABEL` line (or a multi-line `LABEL` block) includes
   both `org=` and `team=`.

3. Report any file that is missing either label as a violation.

## Required format

Labels must appear as a single `LABEL` instruction (preferred — minimises image
layers) or as separate `LABEL` instructions:

```dockerfile
# Preferred — single instruction
LABEL org="base2cloud" \
      team="platform-engineering"

# Also acceptable
LABEL org="base2cloud"
LABEL team="platform-engineering"
```

## What to report

For each violation, output:
- The file path relative to the repo root
- Which label(s) are missing (`org`, `team`, or both)
- A ready-to-paste remediation snippet to add to that file

If all Dockerfiles are compliant, confirm with a single line: "All Dockerfiles contain the required `org` and `team` labels."

## Security note

Labels are metadata only — they do not affect runtime behaviour or image size
meaningfully. However, they are essential for:
- Image provenance tracking in container registries
- Policy enforcement in admission controllers (e.g. OPA Gatekeeper, Kyverno)
- Cost attribution and audit trails in CI/CD pipelines
