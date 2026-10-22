# AssessFlowV2 — Product Requirements Document (PRD)

**Version:** Draft 1 (documentation phase)
**Status:** Living document — unresolved items are marked **TBD**

---

## 1. Product Vision

AssessFlowV2 is an online teacher-focused assessment, grading, and results
platform designed to make creating, administering, grading, checking, analyzing,
and reporting tests and exams easier for teachers.

The system focuses specifically on:

1. Assessment creation
2. Printable OMR assessment sheets
3. Physical test administration
4. OMR scanning and grading
5. Teacher review of uncertain answers
6. Results and student progress
7. Classroom/student management related to assessments
8. Exporting results to Excel

## 2. Goals

- Make the end-to-end assessment workflow reliable for a real classroom.
- Ensure every sheet AssessFlow prints can be reliably read back by AssessFlow.
- Keep teachers in control: review uncertain results, correct them, finalize.
- Keep teachers free: exports are usable outside the platform.
- Enforce teacher-to-teacher data isolation on the server, not just in the UI.
- Protect student data (privacy and security) as a product requirement.
- Preserve proven V1 behavior where appropriate while improving the platform.

## 3. Non-Goals

AssessFlowV2 is **not** intended to become:

- A full school management system
- An ERP
- An LMS
- An attendance platform
- A general student portal
- A handwriting recognition (HTR) system
- A general document recognition system
- A gradebook replacing all school spreadsheet workflows
- Any unrelated school administration function

**Out of scope for initial V2:**

- Student accounts
- Student portal
- LMS
- Attendance management
- Full school ERP
- General school administration
- Handwriting recognition
- Full document recognition

## 4. Users and Roles

| Role | Description |
|---|---|
| **Admin** | Initially, the project owner. Exact permission set is **TBD**. |
| **Teacher** | Creates classrooms, students, assessments; reviews and exports results. |

- Teachers have their own accounts.
- **There are no student accounts in the initial version.**
- Future organization / school hierarchy is **TBD**.

### 4.1 Permissions and Data Isolation

Teacher A must **not** be able to see Teacher B's private:

- classrooms
- students
- assessments
- answer keys
- results
- assessment history

**Data isolation must be enforced by the backend/server**, not merely hidden in
the frontend.

Admin access to teacher data: **TBD** (not yet decided).

### 4.2 Student View

- A teacher can select a student and enter a **Student View** / progress display
  mode to show that student their progress **in person**.
- Students do **not** need their own login.
- The exact appearance and content of Student View: **TBD**.

## 5. Core Teacher Workflow

1. Teacher logs into AssessFlow.
2. First-time dashboard is empty: no classrooms, no students, no assessments, no
   results.
3. Teacher creates a classroom (e.g. `Grade 8.9 - EP`).
4. Teacher enters the classroom.
5. Teacher downloads a CSV student template containing fields such as:
   - Student Name
   - Student ID
   - Classroom Name
6. Teacher fills the CSV with students and uploads it.
   - **CSV import is the NORMAL student onboarding workflow.**
   - **Manual student creation is NOT the normal workflow.**
   - Manual creation is mainly for: adding a forgotten student, correcting
     student information, correcting a Student ID, maintaining individual
     records.
7. Teacher can later: edit classroom, rename classroom, update classroom
   information, delete classroom, add student manually, edit student, update
   student, remove student, re-upload CSV to correct/improve student records.
8. While inside a classroom, teacher creates an assessment/exam.
9. Teacher creates an assessment template for that assessment.
10. AssessFlow generates the printable OMR answer sheet dynamically.
11. Teacher prints the sheet.
12. Teacher provides an answer key.
    - The exact answer-key interface is **TBD**.
    - It may eventually support manual answer entry and/or upload.
13. Students take the physical test.
14. Teacher scans the completed answer sheets.
15. Teacher bulk uploads the scanned sheets.
16. AssessFlow processes the scans using OMR.
17. AssessFlow identifies students, detects answers, and grades them.
18. Teacher reviews uncertain/questionable cases.
19. Teacher can manually correct/override uncertain results.
20. Teacher finalizes the assessment results.
21. Results are stored in AssessFlow.
22. Teacher can view: class results, individual student results, student progress.
23. Teacher can export results to Excel.
24. Teacher can continue editing the exported spreadsheet locally for school
    reporting requirements. **The teacher should not be locked into the
    platform.**

## 6. Classroom Management

Classrooms support:

- create
- edit
- rename
- update
- delete

Requirements:

- Destructive actions should have appropriate confirmation/safety mechanisms.
- CRUD behavior should generally apply to teacher-created/manageable entities.
- **Technical/security settings should NOT be freely editable by normal
  teachers.**
- Classroom fields beyond name (e.g. section, grade level, subject): **TBD**.

## 7. Student Management

Students support:

- import through CSV (primary path)
- add manually (secondary path — see §5.6)
- edit / update
- remove
- re-import / update through CSV

Requirements:

- Re-importing CSV must be able to **correct/improve existing records**.
  Exact matching/deduplication behavior (by Student ID?) is **TBD**.
- Validation errors on CSV upload must be reported to the teacher in a usable
  way. Exact behavior is **TBD**.

## 8. CSV Import

### 8.1 Template

The downloadable CSV template contains at least:

- Student Name
- Student ID
- Classroom Name

Additional columns, encodings, delimiter rules, and download format are **TBD**.

### 8.2 Behavior

- CSV import is the normal onboarding workflow.
- The system must validate the uploaded file and surface errors.
- Handling of duplicate Student IDs (within a class or across classes) is
  **TBD**.
- Handling of partial/failed imports (all-or-nothing vs. row-by-row) is
  **TBD**.

## 9. Assessment Workflow

Lifecycle (informal):

1. Teacher creates an assessment inside a classroom.
2. Teacher defines an assessment template (§10).
3. System generates a printable OMR answer sheet.
4. Teacher prints sheets.
5. Teacher provides an answer key.
6. Students take the physical test.
7. Teacher scans completed sheets.
8. Teacher bulk uploads scans.
9. System runs OMR → identifies students, detects answers, grades.
10. Teacher reviews uncertain/questionable cases and corrects as needed.
11. Teacher finalizes results.
12. Results are stored; history is retained.

### 9.1 Assessment History / Status

AssessFlow should track the assessment lifecycle. States to distinguish
include (informal list, exact model **TBD**):

- created
- template generated
- answer key available
- scanned
- graded
- awaiting review
- finalized/completed

**The exact status model is TBD.**

Assessment history should remain available so teachers can understand what
happened with previous assessments.

## 10. Assessment Types

Initial V2 assessment types:

1. Multiple Choice
2. Matching Type
3. Word Box
4. True/False

- Everything is **OMR**.
- There is **NO handwriting recognition / HTR in V2**.
- More assessment types may be added later.

## 11. Assessment Template Design

The assessment template designer is a **major feature**.

It should **NOT** be a freeform visual page editor. It is a **structured**
assessment/template designer.

The teacher defines **sections**. Each section can define:

- section name/type
- question range
- number of questions
- number/labels of choices

**Example:**

| Part | Type | Questions | Choices |
|---|---|---|---|
| 1 | Multiple Choice | 1–10 | 4 choices (A–D) |
| 2 | Matching | 11–20 | A–J |
| 3 | Word Box | 21–40 | A–T |
| 4 | True/False | 41–50 | **TBD** (see §11.2) |

This generates bubble blocks such as:

```
1  A B C D
2  A B C D
...
10 A B C D
```

### 11.1 Critical distinction

The system must **clearly distinguish**:

- **NUMBER OF QUESTIONS** — how many items exist in a section
- **NUMBER OF CHOICES PER QUESTION** — how many bubbles exist per item

These are separate concepts and must never be conflated in the model, the UI, or
the generated sheet.

### 11.2 Open design questions

- How True/False is represented as bubbles (e/F? T/F? Y/N? two bubbles?) is
  **TBD**.
- Section layout, sheet pagination (multi-page sheets for long tests), and
  visual styling are **TBD**.
- Whether a template can be reused across assessments is **TBD**.
- Maximum questions/sections per sheet is **TBD**.

### 11.3 Template ↔ OMR coupling

- The template determines the **OMR bubble layout**.
- The generated sheet and the OMR engine must be designed as **one connected
  system** so that any sheet generated by AssessFlow can be reliably read by
  AssessFlow.

### 11.4 Sheet identity/header area

Every generated sheet must have a standard identity/header area containing:

- Student Name
- Section/Classroom
- Student ID

The rest of the sheet is dynamically generated according to the assessment
template.

How the Student Name and Classroom appear (printed text vs. hand-written fields
vs. scannable encoding) is **TBD**, but the header area itself is required.

### 11.5 Output format

- The system must generate **printable PNG** output.
- Other output formats (e.g. PDF) can be considered later — **TBD**.

## 12. Answer Key

- The teacher provides an answer key before grading.
- The exact answer-key interface is **TBD**.
- It may eventually support manual answer entry and/or upload.
- Answer keys are **versioned** (see §14).
- Answer keys are **private to the owning teacher** (§4.1).

## 13. OMR Requirements

V2 uses **OMR only**. No handwriting recognition.

**Student identification uses the school-provided Student ID.**

The OMR engine needs to support:

- sheet detection
- alignment / registration
- answer-region detection
- shaded bubble detection
- blank answers
- multiple marked answers
- Student ID detection
- confidence information
- structured answer results

### 13.1 Multiple marks

If a student marks multiple answers for a question that expects one answer, the
answer is considered **incorrect**.

Example: **A + C shaded = invalid/multiple response = incorrect.**

(The result should still be flagged as invalid/multiple so the teacher can see
why it was marked wrong.)

### 13.2 Confidence tiers

The system must distinguish between:

1. **High-confidence accepted answer**
2. **Clearly invalid / multiple response**
3. **Low-confidence / questionable result requiring teacher review**

### 13.3 Teacher correction

- The teacher must be able to **manually correct/override** uncertain results.
- What happens to an override when the assessment is regraded is **TBD**.

### 13.4 Sheet ↔ engine guarantee

Any sheet generated by AssessFlow must be reliably readable by AssessFlow
(see §11.3).

## 14. Grading

Results should include:

- **raw score**
- **percentage**
- **numerical grade**

### 14.1 Grading scale

The grading system follows the **Thai numerical grading system: 1–5**.

- **NO letter grades**
- **NO weighted assessments**
- **NO multiple grading scales**

**The exact percentage → 1–5 thresholds will be provided later.**

> **TBD — DO NOT invent the thresholds.**

### 14.2 Manual adjustments

- Manual teacher adjustments are allowed when the system is uncertain or
  incorrect.
- The exact adjustment UI/behavior is **TBD**.

### 14.3 Scoring policies

- Partial credit: **TBD** (is it allowed at all?).
- Blank answers: score as incorrect — **confirm TBD**.
- Multiple marks: incorrect (§13.1).
- Question weighting: none (§14.1).

## 15. Assessment Versioning

**Assessment versioning is required.**

Once an assessment/answer key has been used to produce results, historical
results must **not silently change** when the answer key is edited later.

Example:

- **Version 1:** Question 5 = B → students are graded.
- **Version 2:** Question 5 = C → **old results remain associated with
  Version 1.**

There should eventually be an **explicit regrade mechanism** if the teacher
intentionally wants to recalculate old results.

- Exact implementation is **TBD**.
- Whether results store a full key snapshot vs. a key version reference is
  **TBD**.
- Whether regrading preserves or discards prior manual overrides is **TBD**.

## 16. Bulk Scanning

Bulk scanning is important.

- The teacher should be able to upload many completed answer sheets in one
  batch.
- The system must **NOT** have an arbitrary artificial limit such as five
  images.
- The target should support practical classroom batches, potentially around
  **30–100 scans in a batch**, subject to real server/resource limitations.

> **Note on V1's five-image limit:** Do **not** assume it has a known cause.
> When V1 is later investigated, determine whether the limitation came from:
> frontend, backend, request size, memory, timeout, processing architecture,
> storage, or another technical limitation. Do not assume the cause without
> evidence.

Related requirements:

- Per-file and total batch size limits based on real resource limits: **TBD**.
- Behavior when individual sheets in a batch fail processing: **TBD** (continue
  with the rest vs. fail the batch).
- Progress feedback for long batches: **TBD**.

## 17. Scanned Image Storage / Storage Lifecycle

Completed scanned answer-sheet images **do NOT need to be permanently stored**.

Preferred lifecycle:

```
Upload → temporary processing → OMR → grading → teacher review → results saved → scan files discarded
```

- Scans may remain temporarily while processing/review is happening.
- Teachers retain the physical exam papers.
- This reduces storage cost, privacy risk, and unnecessary data retention.

**The exact retention implementation is TBD.** Open questions:

- Retention duration / trigger (time-based, assessment-finalized-based).
- Whether a scan can be re-inspected after discard.
- Whether overrides record enough evidence to audit later without the image.

## 18. Results

Results should include information such as:

- Student ID
- Student Name
- Raw Score
- Percentage
- Numerical Grade
- Question-level results where appropriate

Requirements:

- Results tables should be **sortable** by at least: Student Name, Student ID,
  Score, Percentage, Grade.
- Teachers can view an **individual student's result**.
- Teachers can view **student progress** across assessments.
- There should be a teacher-controlled **Student View / progress display mode**.
- Students do **not** need their own login.
- Question-level analytics (which questions were missed most) — desired, exact
  scope **TBD**.

## 19. Excel Export

- Teachers must be able to export relevant results/data to **Excel**.
- The exported file must be usable for: school reporting, additional
  calculations, local editing, record keeping.
- AssessFlow should **not** attempt to replace every spreadsheet workflow used
  by the school.
- Exact export columns, file format (.xlsx vs .csv), and which data sets are
  exportable are **TBD**.

## 20. Privacy and Security

The system will contain student:

- names
- IDs
- scores
- grades
- assessment records

Therefore **privacy and security are important product requirements.**

The architecture must eventually address:

- secure authentication
- authorization
- teacher data isolation
- encrypted communication
- secure storage
- backups
- recovery
- appropriate data retention
- protection of student information

**No specific technology is chosen at this stage.**

Open security questions (**TBD**):

- Password policy, MFA, session management
- Account recovery for teachers
- Admin's access to other teachers' data
- Audit logging of who viewed/changed which student data
- Data residency / legal considerations for student data

## 21. Backup and Recovery

Because V2 is an online system, backups and recovery are required.

The eventual system should have:

- automatic backups
- secure backup storage
- retention policy
- recovery procedure
- **tested restore process**

Restore testing must verify that important data can be recovered, including:

- teacher accounts
- classrooms
- students
- assessments
- answer keys
- results

**The exact backup provider/strategy is TBD.**

## 22. Online Hosting Requirements and Cost

V2 is intended to be an **online web application accessible from anywhere**.

The owner is willing to pay for hosting if necessary. However, **before
choosing an architecture, realistic current costs must be understood.**

The eventual architecture decision must consider:

- application hosting
- database
- authentication
- object/file storage
- backups
- bandwidth
- processing requirements (OMR)
- number of teachers
- number of classrooms
- number of students
- number of assessments
- OMR processing workload

> **Note:** Do **not** assume that the number of teacher accounts directly
> determines cost.

- Realistic hosting/provider options will be researched later.
- Authentication/account costs must be investigated rather than guessed.
- **Technical hosting choices are NOT finalized yet — TBD.**

## 23. Relationship to AssessFlowV1

AssessFlowV1 is an existing working product.

- It has been used by the teacher.
- It is **not** a failed prototype.
- It contains proven functionality.
- It must remain **untouched** while V2 is developed.
- It must **not** be copied into this repository.

When comparing V1 and V2, use these categories:

**KEEP · IMPROVE · REDESIGN · NEW · REMOVE**

- Do not assume that every V1 feature should be rewritten.
- V2 should preserve proven behavior where appropriate while addressing
  limitations and adding the new online platform capabilities.

V1 features known to exist (for reference during comparison): OMR answer
detection, student ID detection, grading, individual results, assessment
history, results dashboard, web UI, classroom CSV import/export, results
export, local production version, macOS launcher with duplicate-launch
detection.

## 24. Future Extensibility

Designed-in room for later, without committing to it now:

- Additional assessment types beyond the initial four
- Additional sheet output formats (beyond PNG)
- Partial credit / weighted grading (explicitly out of scope now)
- Organization / school hierarchy and richer role models
- Question banks / template reuse
- Camera-based or feeder-based scanning input
- Question-level analytics and reporting
- Multi-language support

## 25. Summary of Unresolved (TBD) Decisions

| # | Area | Open question |
|---|---|---|
| 1 | Roles | Exact Admin permission set |
| 2 | Roles | Future organization/school hierarchy |
| 3 | Roles | Admin access to teacher-private data |
| 4 | Student View | Exact appearance/content |
| 5 | Classrooms | Fields beyond classroom name |
| 6 | CSV | Template columns, encoding, delimiter |
| 7 | CSV | Duplicate Student ID handling |
| 8 | CSV | All-or-nothing vs. row-by-row import errors |
| 9 | Status | Exact assessment status model |
| 10 | Template | True/False bubble representation |
| 11 | Template | Pagination, styling, multi-page sheets |
| 12 | Template | Template reuse across assessments |
| 13 | Template | Max questions/sections per sheet |
| 14 | Sheet | How Student Name/Classroom are encoded on the sheet |
| 15 | Sheet | Additional output formats (PDF?) |
| 16 | Answer key | Interface (manual entry / upload / both) |
| 17 | OMR | Override behavior on regrade |
| 18 | Grading | **Percentage → 1–5 thresholds (owner will provide)** |
| 19 | Grading | Partial credit allowed or not |
| 20 | Grading | Blank-answer scoring confirmation |
| 21 | Versioning | Key snapshot vs. version reference |
| 22 | Versioning | Regrade mechanism design |
| 23 | Versioning | Whether overrides survive regrade |
| 24 | Scanning | Per-file / batch size limits from real resources |
| 25 | Scanning | Partial batch failure behavior |
| 26 | Scanning | Progress feedback |
| 27 | Storage | Scan retention trigger/duration |
| 28 | Storage | Re-inspection after discard |
| 29 | Results | Question-level analytics scope |
| 30 | Export | Export columns and file formats |
| 31 | Security | Auth details (password, MFA, sessions, recovery) |
| 32 | Security | Audit logging |
| 33 | Security | Data residency/legal |
| 34 | Backup | Provider/strategy, retention, restore cadence |
| 35 | Hosting | Provider, cost model, scaling approach |

---

*This document is a living document. It will be updated as decisions are made.*
