# AssessFlowV2 — Architecture Requirements

**This is a PRODUCT / ARCHITECTURE REQUIREMENTS document, not an
implementation document.**

It describes what the architecture must provide. It deliberately does **not**
select technologies. Every technical choice that has not yet been researched is
marked **TBD**.

> **Do not read any preference into omissions.** No framework, language,
> database, host, or provider has been chosen yet. Decisions will be made in
> Phase 1 of `PROJECT_PLAN.md` after reviewing these requirements and
> researching current hosting/authentication/database costs.

---

## 1. Application Form Factor

**Requirement:** AssessFlowV2 is an **online web application** accessible from
anywhere with a browser.

Implications that must be satisfied:

- Teachers use it from their own devices without installing a desktop client.
- A single deployed instance serves multiple teachers concurrently.
- The product must remain usable over typical school internet connections.

**TBD:** responsive/mobile support level, offline behavior (likely none
initially), supported browsers.

---

## 2. Multi-User Requirements

- The system serves **multiple teacher accounts concurrently**.
- Each teacher has an independent account and an independent data set.
- There are **no student accounts** in the initial version.
- The **Admin** role exists alongside **Teacher**.

**TBD:**

- Whether multiple admins exist
- Organization/school hierarchy (explicitly not in initial scope, but the data
  model should not foreclose it)
- Expected number of teachers, classrooms, students, assessments, and scans —
  needed for capacity planning

---

## 3. Teacher Data Isolation

**This is a hard architectural requirement.**

Teacher A must not be able to observe Teacher B's:

- classrooms, students, assessments, answer keys, results, assessment history

Requirements:

- Isolation is enforced **server-side** at the data-access layer for **every**
  read, write, list, search, export, and file-access path — not merely by
  hiding UI elements.
- Object identifiers must not be guessable/exploitable across tenants
  (authorization checks must accompany every lookup).
- File access (generated sheets, scans, exports) must carry the same isolation
  as database records.
- Exports, backups of individual tenants, and background jobs must not cross
  tenant boundaries.

**TBD:** whether Admin can view teacher data; how isolation is expressed in the
data model; any need for explicit tenant scoping vs. owner columns.

---

## 4. Admin Role

- Admin exists from the first release (initially, the project owner).
- Admin manages the system; Teachers manage their own instructional data.
- **Technical/security settings must not be freely editable by normal
  teachers.**

**TBD:**

- Exact admin capability set
- Whether admin can view or export teacher-private data (including student PII)
- Admin audit expectations

---

## 5. Authentication Requirements

The architecture must provide:

- Secure authentication for Admin and Teacher accounts
- Protected sessions (no ambient/unauthenticated access to data)
- Protection against brute force and credential stuffing to a reasonable degree
- Encrypted transmission of credentials
- A defined account recovery path

**TBD:**

- Authentication mechanism (self-hosted identity vs. managed provider vs.
  federated login) — **not chosen**
- Password policy, MFA, session lifetime, remember-me behavior
- Account provisioning (admin-created vs. self-signup)
- Cost of the chosen authentication approach (must be researched, not assumed)

---

## 6. Authorization Requirements

- Authorization is enforced **on the server for every request**.
- Roles: Admin, Teacher. Function-level permissions are defined per role.
- Object-level checks verify ownership on each access.
- Teachers manage CRUD over their own **teacher-created/manageable** entities
  (classrooms, students, assessments, results).
- Teachers must **not** be able to alter technical/security settings.
- Denials must be observable (for debugging and auditing).

**TBD:** fine-grained permission matrix; whether roles are fixed or configurable.

---

## 7. Database Requirements

The system must persist, at minimum:

- accounts, roles
- classrooms
- students (name, Student ID, classroom membership)
- assessments (metadata, status, ownership)
- assessment templates (sections, question ranges, question counts, choice
  labels)
- answer keys **and their versions**
- scan/processing records (temporary)
- detected answers with confidence information
- results (raw score, percentage, numerical grade, overrides)
- question-level results
- assessment history / lifecycle events
- audit-relevant events (**TBD**)

Constraints:

- **Relational integrity matters** (classroom → student → assessment →
  result relationships).
- **Answer-key versioning must be first-class**, so historical results never
  silently change.
- Schema evolution must be manageable (migration strategy **TBD**).
- Backup and restore must cover the database as a unit (§12).

**TBD:** database technology, migration tooling, whether the app is
single-region/single-instance or clustered.

---

## 8. Assessment and Result Data Requirements

- Results must reference the **specific answer-key version** used, so edits
  later never mutate historical results.
- A result must be traceable to: assessment, student, key version, scan (while
  it exists), and any manual override.
- Manual overrides must be stored as explicit values with attribution
  (**TBD:** who/when/why fields, audit trail).
- Regrade must be an **explicit, deliberate action** (mechanism **TBD**), never
  an automatic side effect of a key edit.
- Finalized results must be distinguishable from draft/under-review results.

**TBD:** whether results store a full key snapshot or a version reference;
override survival across regrade; retention of question-level detection data
after scans are discarded.

---

## 9. OMR Processing Requirements

The OMR capability must provide:

- sheet detection
- alignment / registration
- answer-region detection
- shaded bubble detection
- blank answer detection
- multiple-mark detection (flagged invalid; scored incorrect)
- Student ID detection
- per-answer **confidence information**
- structured answer results feeding grading

Architectural implications:

- The **template generator and the OMR engine form one connected system**: any
  sheet AssessFlow prints must be reliably readable by AssessFlow.
- OMR must return structured data, not just scores, so uncertain results can be
  reviewed and corrected.
- OMR must be **evaluated against a representative corpus of real scans** with
  an agreed accuracy threshold (**target TBD**).

**TBD:**

- Processing model (synchronous request vs. background job queue) — **not
  chosen**
- Where computation runs and what resource limits apply
- Whether OMR can run on the same host as the web app
- Accuracy threshold and the sheet quality/resolution requirements
- Handling of skewed, folded, cropped, or low-contrast scans

---

## 10. Bulk Processing Requirements

- A single batch should support practical classroom sizes — target
  **~30–100 scans** — with **no arbitrary small limit** such as five images.
- Limits that do exist must be derived from **measured** resource constraints
  (request size, memory, timeout, storage, compute), and documented.

> **Do not assume the cause of V1's five-image limitation.** When investigating
> V1, determine empirically whether it came from frontend, backend, request
> size, memory, timeout, processing architecture, storage, or something else.

- Per-sheet failures must not destroy an entire batch (behavior **TBD**).
- Long batches need progress feedback (**TBD**).
- The system must be able to process a batch without blocking unrelated users.

**TBD:** batch size ceilings by measurement, concurrency limits, queueing,
retry policy, partial-failure semantics.

---

## 11. Temporary Scan Processing and Storage Lifecycle

Preferred lifecycle:

```
Upload → temporary processing → OMR → grading → teacher review → results saved → scan files discarded
```

Requirements:

- Scan images are **not permanently stored**.
- They may be retained temporarily during processing and review.
- Teachers retain the physical exam papers.
- Discarding scans must **not** damage results.
- Storage must be private to the owning teacher while it exists.

**TBD:**

- Retention trigger (time-based vs. assessment-finalized-based) and duration
- Whether a scan can be re-inspected after discard
- Whether overrides retain enough evidence for later audit without the image
- Handling of orphaned/expired files

---

## 12. Backup and Recovery Requirements

Required properties:

- **Automatic** backups
- **Secure** backup storage
- A defined **retention policy**
- A documented **recovery procedure**
- A **tested restore process**

Restore verification must confirm recovery of:

- teacher accounts
- classrooms
- students
- assessments
- answer keys
- results

**TBD:**

- Backup technology/provider (not chosen)
- Backup frequency and retention windows
- Restore test cadence and who performs it
- Off-site/geo separation of backups
- Point-in-time recovery expectations

---

## 13. Security and Privacy Requirements

The system holds student **names, IDs, scores, grades, and assessment records**.

The architecture must address:

- **Encrypted communication** for all client traffic
- **Secure storage** of credentials and sensitive data at rest
- **Authorization** on every access path (see §3, §6)
- **Protection of student information** (least-privilege access, no exposure
  through logs, exports, or URLs)
- **Data retention** policy for student data and scan files
- **Auditability** of sensitive access (**TBD**)
- Resistance to common web vulnerabilities as a baseline expectation

**TBD:**

- Encryption-at-rest approach and key management
- Audit logging scope and retention
- Data residency / legal obligations for student data
- Vulnerability management and dependency update process
- Incident response expectations

---

## 14. Excel Export Requirements

- The system must export results data to a spreadsheet format usable offline.
- Exported files must open and edit cleanly outside AssessFlow.
- Export must respect **teacher data isolation** (never include another
  teacher's data).
- Export must be reproducible and consistent with on-screen results.

**TBD:**

- File format (.xlsx vs .csv) and generation approach
- Exact columns and datasets covered
- Whether exports are generated on demand or scheduled
- Size limits for very large exports

---

## 15. Scalability Considerations

The architecture must be able to grow with (and must be sized against):

- number of teachers
- number of classrooms
- number of students
- number of assessments
- number of scans per batch
- OMR processing workload
- storage for generated sheets and temporary scans (bounded by lifecycle §11)
- export size

Requirements:

- Growth in **data volume** must not require a rewrite (schema and queries must
  support realistic history depth).
- Growth in **processing load** (many concurrent batches) must degrade
  gracefully rather than fail silently.
- Scaling decisions must be **driven by measured load**, not by assumed
  teacher-count formulas.

> **Note:** Do not assume that the number of teacher accounts directly
> determines cost. OMR compute, storage, and bandwidth may dominate.

**TBD:** expected scale figures, performance targets, horizontal vs. vertical
scaling strategy.

---

## 16. Audit, History, and Versioning Requirements

- **Assessment history** must remain available so teachers can understand what
  happened with previous assessments.
- **Assessment lifecycle status** must be distinguishable (exact model **TBD**).
- **Answer key versions** must be retained so historical grades remain
  explainable.
- **Manual overrides** should be attributable (**TBD**).
- **Audit trail** for sensitive actions (who viewed/changed what) is desired —
  scope **TBD**.

---

## 17. Deployment Considerations

- A deployed online instance accessible from anywhere
- Separate environments for development and production (**TBD:** whether a
  staging environment is needed)
- Controlled, repeatable deployment process
- Zero-downtime or low-downtime update expectations (**TBD**)
- Configuration/secrets management that does not leak into the repository
- Monitoring and logging sufficient to diagnose production issues (**TBD**
  specifics)

**TBD:** deployment provider, pipeline, environment count, rollback procedure.

---

## 18. Cost Considerations

Before architecture lock-in, realistic **current** costs must be researched for:

- application hosting
- database
- authentication
- object/file storage
- backups
- bandwidth
- processing requirements (OMR compute)
- expected teachers / classrooms / students / assessments / scan volume

Requirements:

- Cost must be estimated from current provider pricing, not guessed.
- Authentication/account costs must be investigated rather than assumed.
- The estimate must be acceptable to the owner before Phase 1 closes.
- Design choices should avoid premature over-provisioning while leaving room to
  scale.

**TBD:** the entire cost model and provider comparison.

---

## 19. Relationship to AssessFlowV1 (Architectural Reference)

- V1 is a **working, proven** local product and remains **untouched**.
- V1 is a reference for proven behavior; it is **not** a template whose
  architecture must be carried forward.
- Known V1 architectural traits to evaluate (not to assume): local production
  deployment, macOS launcher with duplicate-launch detection, apparent
  five-image batch limit.
- Each V1 trait will be classified **KEEP / IMPROVE / REDESIGN / NEW /
  REMOVE** during architecture decisions.

---

## 20. Consolidated TBD Register (Architecture)

| # | Topic | Open item |
|---|---|---|
| 1 | Form factor | Browser/mobile support level, offline behavior |
| 2 | Scale | Expected teachers/classrooms/students/assessments/scan volume |
| 3 | Tenancy | Isolation mechanism in the data model; admin visibility |
| 4 | Auth | Mechanism, policy, sessions, recovery, and its cost |
| 5 | Authz | Permission matrix; fixed vs. configurable roles |
| 6 | Data | Database technology and migration strategy |
| 7 | Data | Key snapshot vs. version reference; override survival on regrade |
| 8 | OMR | Processing model (sync vs. background), where compute runs |
| 9 | OMR | Accuracy threshold; scan quality requirements |
| 10 | Bulk | Measured batch limits; partial-failure and progress behavior |
| 11 | Scans | Retention trigger/duration; post-discard re-inspection |
| 12 | Backup | Provider, frequency, retention, restore cadence |
| 13 | Security | Encryption at rest, audit logging, data residency |
| 14 | Export | Format, columns, datasets, size limits |
| 15 | Scale | Performance targets, scaling strategy |
| 16 | Deploy | Provider, environments, rollback, monitoring |
| 17 | Cost | Full researched cost model |

---

*This is a living document. Technologies will be selected in Phase 1 and
recorded here with rationale once researched.*
