# AssessFlowV2

**Online teacher-focused assessment, grading, and results platform.**

> **Current status: Documentation / planning phase.**
> No application code, database schemas, or dependencies exist yet.
> This repository currently contains product documentation only.

---

## What is AssessFlowV2?

AssessFlowV2 is an online platform that helps teachers create, administer, grade,
check, analyze, and report on tests and exams — with a focus on **OMR (Optical
Mark Recognition) answer sheets**.

It is a clean rebuild and evolution of AssessFlowV1, an existing working product
that has been used by a real teacher. V1 is treated as a reference and a source
of proven behavior; it remains untouched while V2 is developed.

## Who is it for?

- **Admin** — initially, the project owner.
- **Teacher** — creates classrooms, assessments, reviews results.

There are **no student accounts** in the initial version. A teacher can open a
**Student View** to show a student their progress in person.

## What it is NOT

AssessFlowV2 is **not** a school management system, ERP, LMS, attendance
platform, or general student portal. See `PRD.md` for the full non-goals list.

## Major capabilities

- Classroom and student management
- CSV student import (normal onboarding path) and export
- Assessment creation with a **structured template designer**
- Dynamically generated, printable **OMR answer sheets** (PNG)
- Physical test administration
- Bulk scanning upload (target: practical classroom batches, ~30–100 scans)
- OMR processing: sheet detection, alignment, answer detection, Student ID
  recognition, blank/multiple-mark handling, confidence scoring
- Teacher review and manual correction of uncertain results
- Grading with raw score, percentage, and numerical grade (Thai 1–5 system)
- Assessment versioning so historical results never silently change
- Results, assessment history, student progress, and Student View
- Excel export for school reporting and local editing
- Privacy, security, backup, and recovery requirements

## High-level workflow

```
Teacher logs in
  → creates a classroom
  → downloads CSV template, fills it, uploads students
  → creates an assessment inside the classroom
  → defines an assessment template (sections, question ranges, choices)
  → system generates a printable OMR sheet (PNG)
  → teacher prints the sheet
  → teacher provides an answer key
  → students take the physical test
  → teacher scans and bulk-uploads the completed sheets
  → system runs OMR: identifies students, detects answers, grades
  → teacher reviews uncertain/questionable cases and corrects results
  → teacher finalizes results
  → teacher views class results, individual results, and student progress
  → teacher exports results to Excel
```

## Documentation map

| File | Purpose |
|---|---|
| `README.md` | Project overview and documentation map (this file) |
| `PRD.md` | Product Requirements Document — the main requirements source |
| `ACCEPTANCE_TESTS.md` | Product-level acceptance criteria and scenarios |
| `PROJECT_PLAN.md` | Preliminary milestone roadmap with visible open decisions |
| `ARCHITECTURE.md` | Product/architecture requirements — no technology choices yet |

## Relationship to AssessFlowV1

AssessFlowV1 is an existing, working, non-failed product. It is a reference for
proven behavior. When comparing V1 and V2, features are categorized as:

**KEEP · IMPROVE · REDESIGN · NEW · REMOVE**

Not every V1 feature will be rewritten. Decisions are made during planning, not
by assumption.

## Working rules for this phase

1. Documentation only — no application code.
2. No package installation.
3. AssessFlowV1 is not modified or copied into this repository.
4. No technical stack is chosen yet.
5. Unresolved requirements are marked **TBD** and are not invented.
6. Nothing is committed or pushed to Git during this phase.

These documents are **living documents** and will be updated as decisions are
made.
