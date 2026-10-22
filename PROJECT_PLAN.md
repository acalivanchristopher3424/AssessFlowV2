# AssessFlowV2 — Project Plan

**Preliminary product roadmap.**
This plan is organized around **product milestones**, not implementation
details. Frameworks, languages, hosting providers, and database choices are
deliberately **not** specified here — those belong to Phase 1 and later.

Unresolved decisions are surfaced inline as **TBD** and collected in
§"Open Decisions".

---

## Guiding principles

- Work in phases; each phase ends with demonstrable product progress.
- Decisions are made explicitly and recorded in `PRD.md` / `ARCHITECTURE.md`.
- Proven V1 behavior is classified (KEEP / IMPROVE / REDESIGN / NEW / REMOVE)
  rather than assumed.
- AssessFlowV1 is never modified or copied.

---

## Phase 0 — Product documentation and decisions

**Goal:** A shared, unambiguous description of what we are building.

Activities:

- Author and maintain `README.md`, `PRD.md`, `ACCEPTANCE_TESTS.md`,
  `PROJECT_PLAN.md`, `ARCHITECTURE.md`
- Capture the core teacher workflow and scope boundaries
- Record all unresolved product questions as TBD
- Decide the V1 comparison process (KEEP/IMPROVE/REDESIGN/NEW/REMOVE)

Exit criteria:

- Documentation reflects the product vision and is approved by the owner
- Every known open question is listed, none silently invented

**TBD in this phase:** none blocking (documentation only).

---

## Phase 1 — Architecture and technology decisions

**Goal:** Choose how to build, based on researched evidence — not assumption.

Activities:

- Research realistic current costs: hosting, database, authentication,
  object/file storage, backups, bandwidth, OMR compute
- Define architecture requirements (see `ARCHITECTURE.md`) and evaluate
  options against them
- Decide the technology stack
- Decide deployment model and environment (dev / staging / production)
- Decide data location and backup approach at a high level

Exit criteria:

- Documented architecture decision record with rationale
- Documented cost estimate acceptable to the owner

**Open decisions (TBD):**

- Hosting provider and plan
- Database choice
- Authentication approach and its cost
- Object/file storage approach
- Backup provider/strategy
- Application framework and language
- Frontend approach
- OMR processing approach (synchronous vs. background jobs)
- Whether V1's local-first design carries any constraints into V2

---

## Phase 2 — Authentication and teacher accounts

**Goal:** Teachers can securely have their own accounts; isolation is enforced
server-side.

Activities:

- Admin account and role
- Teacher account creation/management
- Login/logout, session handling
- Server-side authorization framework for teacher data isolation
- Password policy, account recovery (TBD specifics)

Exit criteria:

- AC-1.1 through AC-1.8 pass

**Open decisions (TBD):**

- How teacher accounts are provisioned (self-signup vs. admin-created)
- Exact Admin permission set
- Admin visibility into teacher-private data
- Password policy / MFA / session lifetime
- Audit logging requirements

---

## Phase 3 — Classroom and student management

**Goal:** The normal onboarding path (CSV) works end to end.

Activities:

- Classroom CRUD with confirmation on destructive actions
- CSV template download
- CSV upload, validation, and error reporting
- CSV re-import/correction of existing records
- Manual student add/edit/remove (secondary path)

Exit criteria:

- AC-2.1 through AC-2.5 and AC-3.1 through AC-3.7 pass

**Open decisions (TBD):**

- Classroom fields beyond name
- CSV columns/encoding/delimiter
- Duplicate Student ID handling
- Import atomicity (all-or-nothing vs. row-by-row)
- Data behavior when a classroom/student is deleted

---

## Phase 4 — Assessment creation

**Goal:** A teacher can create and manage assessments inside a classroom.

Activities:

- Assessment CRUD scoped to a classroom and teacher
- Assessment lifecycle status tracking
- Assessment history view

Exit criteria:

- AC-4.1 through AC-4.4 pass

**Open decisions (TBD):**

- Exact status model (created → template generated → answer key available →
  scanned → graded → awaiting review → finalized/completed)
- Assessment metadata (subject, date, term, description)
- Naming/duplication rules

---

## Phase 5 — Template and OMR sheet generation

**Goal:** A teacher defines a structured template and receives a printable,
machine-readable sheet.

Activities:

- Structured template designer (sections, type, question range, question count,
  choice labels)
- Strict separation of question count vs. choice count
- Standard identity header (Student Name, Section/Classroom, Student ID)
- Printable PNG generation
- Sheet style/layout for readability

Exit criteria:

- AC-5.1 through AC-5.7 pass

**Open decisions (TBD):**

- True/False bubble encoding
- Pagination / multi-page sheets for long tests
- Sheet visual styling and branding
- Template reuse across assessments
- Max questions/sections per sheet
- How Student Name and Classroom are represented on the sheet
- Additional output formats (PDF?)

---

## Phase 6 — Answer key and grading

**Goal:** A teacher can supply a key and get scores on the 1–5 Thai scale.

Activities:

- Answer-key entry and storage (interface TBD)
- Answer-key versioning model
- Grading rules: correct/incorrect, blank, multiple marks
- Score computation: raw score, percentage, numerical grade
- Manual adjustment capability

Exit criteria:

- AC-6.1, AC-6.2, AC-9.1 through AC-9.3 pass
- AC-10.1 passes (historical results do not silently change)

**Open decisions (TBD):**

- **Percentage → 1–5 thresholds (owner will provide — do not invent)**
- Answer-key interface (manual entry / upload / both)
- Whether partial credit exists at all (currently: none)
- Key snapshot vs. version reference
- Regrade mechanism design
- Whether manual overrides survive regrade

---

## Phase 7 — OMR scanning and bulk processing

**Goal:** Real classroom batches of scanned sheets are processed reliably.

Activities:

- Bulk upload of many images with no arbitrary small limit
- Sheet detection, alignment/registration, answer-region detection
- Shaded/blank/multiple-mark detection
- Student ID detection and student matching
- Confidence scoring and tiering (accepted / invalid / needs review)
- Batch progress and per-sheet failure handling
- Temporary scan storage and post-processing discard

Exit criteria:

- AC-7.1 through AC-7.8 pass
- Batch target of ~30–100 scans demonstrated under real resource limits

**Open decisions (TBD):**

- Root cause of V1's five-image limit (investigate with evidence; do not
  assume frontend/backend/memory/timeout/storage without proof)
- Per-file and total batch size limits based on measured resources
- Processing model (immediate vs. background queue)
- Partial batch failure behavior
- Progress feedback UX
- Scan retention trigger/duration and whether re-inspection is possible after
  discard
- Input sources (file upload now; camera/scanner feed later?)

---

## Phase 8 — Results and student progress

**Goal:** Teachers can analyze what happened.

Activities:

- Results tables with required fields and sorting
- Individual student result view
- Student progress across assessments
- Teacher-controlled Student View / progress display
- Question-level results where appropriate

Exit criteria:

- AC-8.1 through AC-8.3 and AC-11.1 through AC-11.5 pass

**Open decisions (TBD):**

- Student View exact content/appearance
- Question-level analytics scope (e.g. item difficulty, common distractors)
- Progress visualization choices
- How finalized vs. draft results are distinguished in the UI

---

## Phase 9 — Excel export

**Goal:** Teachers can take their data and work with it offline.

Activities:

- Export results to a spreadsheet file
- Ensure the export opens and edits cleanly outside the platform
- Cover the data sets teachers need for school reporting

Exit criteria:

- AC-12.1 through AC-12.3 pass

**Open decisions (TBD):**

- Exact export columns
- File format (.xlsx vs .csv)
- Which datasets are exportable (results only, or also rosters/templates)
- One-click vs. configurable export

---

## Phase 10 — Security, backups, deployment

**Goal:** The online system is safe, recoverable, and actually deployed.

Activities:

- Encrypted communication
- Secure storage of student data
- Automatic backups with a retention policy
- Documented recovery procedure
- **Tested restore** verifying teacher accounts, classrooms, students,
  assessments, answer keys, and results
- Deployment pipeline to production
- Data retention policy (including scan lifecycle)

Exit criteria:

- AC-13.1 through AC-13.4 and AC-14.1 pass

**Open decisions (TBD):**

- Backup provider/strategy, retention, restore cadence
- Monitoring, alerting, and incident response
- Data residency/legal considerations for student data
- Retention policy specifics for student data and scan files

---

## Phase 11 — Testing and production readiness

**Goal:** Confidence that the product works for a real teacher in a real
classroom.

Activities:

- End-to-end testing against `ACCEPTANCE_TESTS.md`
- Accuracy evaluation of OMR against a representative sheet corpus
- Load/soak testing of bulk batches
- Usability pass with the actual teacher
- Documentation for teachers (how to print, scan, and import)
- V1 vs. V2 feature classification review (KEEP/IMPROVE/REDESIGN/NEW/REMOVE)
  to confirm nothing proven was lost

Exit criteria:

- All applicable acceptance scenarios pass
- OMR accuracy meets an agreed threshold (**target TBD**)
- Owner signs off for production use

**Open decisions (TBD):**

- OMR accuracy acceptance threshold
- Supported scan qualities/resolutions
- Performance targets for batch processing
- Beta/pilot scope and rollback plan

---

## Phase summary

| Phase | Focus | Primary exit |
|---|---|---|
| 0 | Product documentation & decisions | Approved docs, visible TBDs |
| 1 | Architecture & technology decisions | ADR + cost estimate |
| 2 | Authentication & teacher accounts | Isolation enforced server-side |
| 3 | Classroom & student management | CSV onboarding works |
| 4 | Assessment creation | Assessments + history |
| 5 | Template & OMR sheet generation | Printable, readable PNG |
| 6 | Answer key & grading | 1–5 grades, versioned keys |
| 7 | OMR scanning & bulk processing | ~30–100 scan batches |
| 8 | Results & student progress | Sorting, progress, Student View |
| 9 | Excel export | Usable offline spreadsheet |
| 10 | Security, backups, deployment | Tested restore, live system |
| 11 | Testing & production readiness | Owner sign-off |

**Suggested sequencing note:** Phases 5–7 form the technical core (sheet ↔
key ↔ OMR must stay consistent as one system) and should not be split apart
without an explicit dependency review.

---

## Open Decisions Register (cross-phase)

| Phase | Open decision |
|---|---|
| 1 | Hosting provider, database, auth approach, storage, backups, stack |
| 1 | Realistic cost model (hosting, DB, auth, storage, bandwidth, OMR compute) |
| 2 | Account provisioning, admin permission set, password/session policy |
| 3 | CSV format details, duplicate ID handling, import atomicity |
| 4 | Exact assessment status model |
| 5 | True/False encoding, pagination, sheet styling, template reuse |
| 6 | **Percentage → 1–5 thresholds**, answer-key interface, regrade design |
| 7 | V1 batch-limit root cause, batch limits, failure behavior, retention |
| 8 | Student View details, analytics scope |
| 9 | Export columns and formats |
| 10 | Backup strategy, restore cadence, data residency |
| 11 | OMR accuracy threshold, performance targets, pilot scope |

---

*This is a living document. Update it as phases complete and TBDs are resolved.*
