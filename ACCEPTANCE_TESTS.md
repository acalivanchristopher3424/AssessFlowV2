# AssessFlowV2 — Product Acceptance Criteria

**These are PRODUCT-LEVEL acceptance scenarios, not automated unit tests.**

They describe what must be true for the product to be considered successful.
They are written so a developer, tester, or product owner can evaluate a build
against them.

Notation: `Given / When / Then` where useful.

> **Note:** Items marked **TBD** depend on unresolved product decisions and
> cannot be finalized yet. They are listed as scenarios with pending specifics.

---

## 1. Accounts, Roles, and Data Isolation

### AC-1.1 Teacher can log in
- **Given** a teacher account exists
- **When** the teacher logs in with valid credentials
- **Then** the teacher is authenticated and sees their own dashboard

### AC-1.2 Failed login is rejected
- **Given** a teacher account exists
- **When** invalid credentials are submitted
- **Then** login is refused and no session is created

### AC-1.3 First-time dashboard is empty
- **Given** a newly registered teacher with no data
- **When** the teacher opens the dashboard
- **Then** there are no classrooms, no students, no assessments, and no results
  shown

### AC-1.4 Teacher A cannot access Teacher B's classroom
- **Given** Teacher B owns classroom `C-B`
- **When** Teacher A attempts to open `C-B` (by URL, API, or any direct
  request)
- **Then** access is denied by the server

### AC-1.5 Teacher A cannot see Teacher B's private data
- **Given** Teacher B owns students, assessments, answer keys, results, and
  assessment history
- **When** Teacher A queries any listing or search that could include them
- **Then** none of Teacher B's private records are returned

> This must hold at the **backend**, not only through hidden UI elements.

### AC-1.6 Isolation covers answer keys
- **Given** two teachers each have an assessment
- **When** either teacher requests the other's answer key
- **Then** the request is denied server-side

### AC-1.7 Admin role exists
- **Given** the Admin account
- **When** the Admin performs admin-level actions
- **Then** admin functions are available per the (TBD) admin permission set

### AC-1.8 No student accounts exist
- **Given** the initial product
- **When** account creation is attempted
- **Then** only Admin and Teacher accounts can exist; students have no login

**TBD:** exact Admin permission set; Admin's visibility into teacher data;
password policy; session behavior; account recovery.

---

## 2. Classroom Management

### AC-2.1 Teacher can create a classroom
- **Given** a logged-in teacher
- **When** the teacher creates a classroom (e.g. `Grade 8.9 - EP`)
- **Then** the classroom appears in the classroom list and can be entered

### AC-2.2 Teacher can rename a classroom
- **Given** an existing classroom
- **When** the teacher renames it
- **Then** the new name is persisted and displayed everywhere it appears

### AC-2.3 Teacher can edit/update classroom information
- **Given** an existing classroom
- **When** the teacher edits its fields
- **Then** changes are saved

### AC-2.4 Teacher can delete a classroom
- **Given** an existing classroom
- **When** the teacher deletes it with confirmation
- **Then** the classroom is removed and its dependent data follows the defined
  policy (see **TBD** on delete cascade)

### AC-2.5 Destructive actions require confirmation
- **Given** a destructive action (delete classroom, remove student)
- **When** the teacher initiates it
- **Then** a confirmation step prevents accidental execution

**TBD:** classroom fields beyond name; behavior of dependent data on delete.

---

## 3. Student Management and CSV Import

### AC-3.1 Teacher can download a CSV template
- **Given** a teacher inside a classroom
- **When** the teacher requests the student CSV template
- **Then** a downloadable CSV is provided containing at least: Student Name,
  Student ID, Classroom Name

### AC-3.2 Teacher can import students from CSV (normal path)
- **Given** a teacher with a filled CSV template
- **When** the teacher uploads the file
- **Then** students are created in the classroom and appear in the student list

### AC-3.3 CSV import reports errors clearly
- **Given** an uploaded CSV with invalid or missing data
- **When** the file is processed
- **Then** the teacher receives an understandable report of what failed

### AC-3.4 Re-import corrects existing student records
- **Given** a classroom already populated via CSV
- **When** the teacher uploads a corrected CSV
- **Then** existing records are corrected/improved rather than blindly
  duplicated (matching rule: **TBD**)

### AC-3.5 Manual student creation works (secondary path)
- **Given** a teacher who forgot a student
- **When** the teacher adds the student manually
- **Then** the student record is created with a Student ID

### AC-3.6 Teacher can edit a student
- **Given** an existing student
- **When** the teacher edits name/ID
- **Then** changes persist and are reflected in results views where applicable

### AC-3.7 Teacher can remove a student
- **Given** an existing student
- **When** the teacher removes the student with confirmation
- **Then** the student is removed per the defined data-retention policy
  (**TBD**)

**TBD:** duplicate Student ID handling; import atomicity; encoding/delimiters;
delete cascade.

---

## 4. Assessment Creation

### AC-4.1 Teacher can create an assessment
- **Given** a teacher inside a classroom
- **When** the teacher creates an assessment/exam
- **Then** the assessment exists and belongs to that classroom

### AC-4.2 Assessment is private to its teacher
- **Given** an assessment owned by Teacher A
- **When** Teacher B requests it
- **Then** access is denied server-side

### AC-4.3 Assessment lifecycle status is visible
- **Given** an assessment progressing through its lifecycle
- **When** the teacher views it
- **Then** its status is distinguishable (e.g. created / template generated /
  answer key available / scanned / graded / awaiting review / finalized —
  exact model **TBD**)

### AC-4.4 Assessment history is retained
- **Given** previously completed assessments
- **When** the teacher views history
- **Then** past assessments and their outcomes remain accessible

---

## 5. Template Designer and OMR Sheet Generation

### AC-5.1 Teacher can define multiple sections
- **Given** an assessment template in progress
- **When** the teacher defines sections (e.g. Part 1 MC 1–10, Part 2 Matching
  11–20, Part 3 Word Box 21–40, Part 4 True/False 41–50)
- **Then** all sections are stored with their type, question range, question
  count, and choice labels

### AC-5.2 Questions and choices are distinct concepts
- **Given** a section with 10 questions and 4 choices
- **When** the template is saved
- **Then** the system stores **10 questions × 4 choices**, never conflating the
  two counts

### AC-5.3 Generated sheet matches the template
- **Given** a defined template
- **When** the system generates the OMR sheet
- **Then** the bubble layout exactly reflects the section types, question
  ranges, and choice labels defined

### AC-5.4 Sheet includes the identity header
- **Given** any generated sheet
- **When** the sheet is produced
- **Then** it contains a standard header area with Student Name,
  Section/Classroom, and Student ID

### AC-5.5 Output is printable PNG
- **Given** a generated sheet
- **When** the teacher downloads it
- **Then** a printable PNG is produced

### AC-5.6 Generated sheets are readable by AssessFlow's OMR engine
- **Given** any sheet generated by AssessFlow
- **When** it is completed and scanned
- **Then** the OMR engine can read it reliably (the template and the engine are
  one connected system)

### AC-5.7 No freeform page editor
- **Given** the template designer
- **When** the teacher designs a template
- **Then** the experience is structured (sections + parameters), not a freeform
  visual page editor

**TBD:** True/False bubble representation; pagination/styling; template reuse;
max questions per sheet; additional output formats.

---

## 6. Answer Key

### AC-6.1 Teacher can provide an answer key
- **Given** an assessment
- **When** the teacher provides the answer key
- **Then** the system can grade scans against it

### AC-6.2 Answer key is private
- **Given** an answer key owned by Teacher A
- **When** Teacher B requests it
- **Then** access is denied server-side

**TBD:** answer-key interface (manual entry / upload / both).

---

## 7. OMR Scanning and Bulk Processing

### AC-7.1 Teacher can bulk upload many completed sheets
- **Given** a batch of scanned sheets (a practical classroom batch, target
  ~30–100 images)
- **When** the teacher uploads them in one batch
- **Then** all are accepted for processing with **no arbitrary small limit**
  such as five images

### AC-7.2 System identifies the correct student
- **Given** a completed sheet with a Student ID marked in the header
- **When** OMR processes the sheet
- **Then** the correct student is identified and the result is attached to that
  student

### AC-7.3 System detects answers
- **Given** a scanned sheet
- **When** OMR processes it
- **Then** detected answers are returned as structured results

### AC-7.4 Blank answers are recognized
- **Given** a question left blank
- **When** OMR processes the sheet
- **Then** it is recorded as blank (and scored per the grading policy —
  **TBD**)

### AC-7.5 Multiple marks are handled correctly
- **Given** a single-answer question where a student shades both A and C
- **When** OMR processes the sheet
- **Then** the response is recorded as **invalid/multiple response** and graded
  **incorrect**

### AC-7.6 Confidence is reported
- **Given** a processed sheet
- **When** results are produced
- **Then** each answer carries a confidence indication distinguishing:
  1. high-confidence accepted,
  2. clearly invalid/multiple response,
  3. low-confidence/questionable requiring review

### AC-7.7 Individual sheet failures do not destroy the batch
- **Given** a bulk batch where some sheets fail to process
- **When** processing completes
- **Then** successful sheets are kept and failures are surfaced
  (exact behavior **TBD**)

### AC-7.8 Scans are discarded after processing
- **Given** processing and review completed and results saved
- **When** the retention condition is met
- **Then** scan files are discarded and results remain intact
  (retention rule **TBD**)

---

## 8. Teacher Review and Correction

### AC-8.1 Uncertain results can be reviewed
- **Given** results containing low-confidence/questionable items
- **When** the teacher opens review
- **Then** those items are presented for review

### AC-8.2 Teacher can correct a result
- **Given** a questionable or incorrect detected answer
- **When** the teacher overrides it
- **Then** the corrected value is used in grading and the original detection is
  retained for traceability (**TBD**)

### AC-8.3 Teacher can finalize results
- **Given** a reviewed assessment
- **When** the teacher finalizes it
- **Then** results are stored and marked finalized/completed

**TBD:** whether overrides survive a regrade; audit trail of overrides.

---

## 9. Grading

### AC-9.1 Results include raw score, percentage, and numerical grade
- **Given** a graded assessment
- **When** results are displayed
- **Then** each student shows raw score, percentage, and numerical grade

### AC-9.2 Numerical grade uses the Thai 1–5 scale
- **Given** a percentage
- **When** the grade is computed
- **Then** the result is in 1–5, with **no letter grades, no weighted
  assessments, no multiple scales**

**TBD — do not invent:** the exact percentage → 1–5 thresholds (owner will
provide).

### AC-9.3 Manual adjustment is allowed
- **Given** a result the system graded incorrectly or uncertainly
- **When** the teacher manually adjusts it
- **Then** the adjusted value is reflected in results

---

## 10. Assessment Versioning

### AC-10.1 Historical results do not silently change
- **Given** results produced under answer key Version 1 (Q5 = B)
- **When** the answer key is edited to Version 2 (Q5 = C)
- **Then** the old results remain associated with Version 1 and are unchanged

### AC-10.2 Explicit regrade is available (eventually)
- **Given** historical results tied to an older key version
- **When** the teacher intentionally triggers a regrade
- **Then** results are recalculated explicitly (mechanism **TBD**)

**TBD:** snapshot vs. version reference; regrade behavior with overrides.

---

## 11. Results, Progress, and Student View

### AC-11.1 Results show required fields
- **Given** graded results
- **When** the teacher views results
- **Then** they include Student ID, Student Name, Raw Score, Percentage,
  Numerical Grade, and question-level results where appropriate

### AC-11.2 Results table is sortable
- **Given** a results table
- **When** the teacher sorts by Student Name, Student ID, Score, Percentage, or
  Grade
- **Then** the table reorders accordingly

### AC-11.3 Teacher can view individual student results
- **Given** a student with results
- **When** the teacher opens that student's result
- **Then** their individual performance is shown

### AC-11.4 Teacher can view student progress
- **Given** a student with multiple assessments
- **When** the teacher opens student progress
- **Then** performance across assessments is shown over time

### AC-11.5 Teacher can open Student View
- **Given** a selected student
- **When** the teacher enters Student View
- **Then** a progress display suitable for showing to the student in person is
  presented, with **no student login required**

**TBD:** Student View content/appearance; question-level analytics scope.

---

## 12. Excel Export

### AC-12.1 Teacher can export results to Excel
- **Given** existing results
- **When** the teacher exports
- **Then** a spreadsheet file is downloaded containing the results

### AC-12.2 Exported file is usable offline
- **Given** an exported file
- **When** the teacher opens it outside AssessFlow
- **Then** it can be edited, recalculated, and used for school reporting

### AC-12.3 Export is not a lock-in
- **Given** a teacher who stops using the platform
- **When** they use their exported data
- **Then** their data remains usable without AssessFlow

**TBD:** exact columns, .xlsx vs .csv, which datasets are exportable.

---

## 13. Privacy, Security, and Backups

### AC-13.1 Data is encrypted in transit
- **Given** any client communication
- **When** data is transmitted
- **Then** it is encrypted

### AC-13.2 Backups run automatically
- **Given** the production system
- **When** the backup schedule elapses
- **Then** backups are created and stored securely per the retention policy

### AC-13.3 Data can be restored from backup
- **Given** a completed backup
- **When** a restore is performed
- **Then** teacher accounts, classrooms, students, assessments, answer keys,
  and results are recovered

### AC-13.4 Restore process is tested
- **Given** the restore procedure
- **When** it is exercised on a schedule
- **Then** it is verified to succeed (test cadence **TBD**)

**TBD:** backup provider/strategy, retention, restore cadence, audit logging,
data residency.

---

## 14. Online Access and Cost

### AC-14.1 Accessible from anywhere
- **Given** an internet-connected device
- **When** a teacher navigates to the deployed application
- **Then** they can log in and use the system

### AC-14.2 Hosting cost is understood before architecture lock-in
- **Given** the planned architecture
- **When** the plan is approved
- **Then** realistic current costs for hosting, database, auth, storage,
  backups, bandwidth, and OMR processing have been researched
  (research **TBD**)

---

## 15. V1 Reference

### AC-15.1 V1 remains untouched
- **Given** V2 development
- **When** work proceeds
- **Then** AssessFlowV1 is not modified, and no V1 code is copied into this
  repository

### AC-15.2 Proven V1 behavior is deliberately classified
- **Given** each V1 feature
- **When** it is evaluated for V2
- **Then** it is explicitly marked **KEEP / IMPROVE / REDESIGN / NEW / REMOVE**
  rather than assumed

---

## Summary of scenarios that cannot be finalized yet (TBD)

- Grading thresholds (percentage → 1–5)
- Admin permission set and admin visibility into teacher data
- CSV matching/deduplication and import atomicity
- Assessment status model details
- True/False bubble encoding and sheet pagination
- Answer-key interface
- Scan retention trigger and post-discard re-inspection
- Regrade behavior and override survival
- Batch failure behavior and progress feedback
- Excel export columns/formats
- Backup strategy and restore cadence
