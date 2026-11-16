# KAAVAL: Explainable Access Alerts for Shared Labs
> **Explainable Access Alerts, Historical Behavioural Pattern Recognition & Location Verification for Shared Academic Laboratories**  
> *A Hackathon Security & Access-Management Prototype*

---

## 🛡️ Core Innovations & Architecture

**KAAVAL** provides an end-to-end, explainable incident review process designed for university and shared research computing laboratories:

1. **Feature 1: Historical Behavioural Pattern Recognition**
   - Automatically groups events by account and sorts them chronologically.
   - Partitions event history into an empirical **Historical Baseline Period** versus **Recent Activity Period**.
   - Calculates first-attempt success rate, failed login counts, repeated failure sequences, failed-then-success patterns, and after-hours access.
   - Renders an interactive **SVG Line Chart** for cumulative login trends over time and a **Bar Chart** comparing baseline vs recent metrics.
   - Detects behavioural shift signals (e.g., failure spikes, unprecedented consecutive failure sequences, novel terminal usage, after-hours access shifts) with supporting event IDs and an honest *"Insufficient historical data"* fallback if fewer than 3 records exist.

2. **Feature 2: Evidence-Linked Incident Case Files**
   - Every detected incident generates a traceable, stable **Case File** (e.g., `CASE-RULA-EVT-1006`).
   - Re-uploading or refreshing the same dataset **never creates duplicate cases** and preserves all existing review statuses, remarks, and conversation messages.
   - Supports the **8 required review statuses**:
     1. `Pending Review`
     2. `Under Review`
     3. `Clarification Requested`
     4. `Response Received`
     5. `Further Verification Required`
     6. `Reviewed — No Further Action`
     7. `Escalated`
     8. `Inconclusive`
   - Clearly isolates triggering evidence from surrounding contextual events, known facts from evidentiary limits, and records a timestamped activity audit trail.

3. **Feature 3: Student–Teacher Case Conversation System**
   - **Teacher Workflow**: Open any Case File, review evidence and behavioural deviations, and post clarification inquiries to the student. Can also record **Private Faculty Internal Notes** (hidden from the student). Sending an inquiry automatically moves the case from `Pending Review` or `Under Review` to `Clarification Requested`.
   - **Student Workflow**: In the Student Portal, students see **"My Assigned Incident Cases"** explicitly matching their account. They can read inquiries, type explanations (e.g. Caps Lock error, project deadline), and send replies. Submitting a reply automatically advances the case status to `Response Received`!
   - **Role-Based Isolation**: Students can never see private internal notes (`isInternalNote: true`) or cases belonging to other students.

4. **Priority Feature: Explainable Location Verification**
   - Haversine spherical proximity engine with GPS accuracy radius ($r$) analysis.
   - If indoor satellite drift causes $[d - r, d + r]$ to cross the lab perimeter, marks the status **`UNCERTAIN`** rather than issuing a false penalty.
   - Built-in **Demo GPS Simulator (Judge Sandbox)** with presets for presentation.
   - Short-lived single-use demo verification codes (`KVL-XXXX`) with single-use invalidation.

---

## 🚀 Quick Start Guide

### 1. Requirements
- Node.js v18+ (tested on Node v22.14.0)
- npm v9+

### 2. Running Locally
```bash
# Start the Vite development server (running at http://localhost:5173/)
npm run dev
```

### 3. Running Automated Tests
```bash
# Run Vitest automated test suite:
npm test
```
All **41 automated tests across 6 test suites** pass with 100% success rate:
- `csvParser.test.js` (5 tests): CSV validation & chronological sorting.
- `detectionRules.test.js` (9 tests): Rule A, Rule B, boundaries, grouping, and `department_case` independence.
- `geoUtils.test.js` (8 tests): Haversine distance, GPS uncertainty, and error handling.
- `demoCode.test.js` (6 tests): Single-use demo code generation, expiration, and replay prevention.
- `caseManagementAndBehavior.test.js` (10 tests): Behavioural metrics, trend charts, Case File stability, duplicate prevention, 8 review statuses, student-teacher messaging, and private note isolation.
- `storageAndWorkflow.test.js` (3 tests): LocalStorage prototype persistence and audit trails.

### 4. Building for Production
```bash
npm run build
```

---

## 🎯 Step-by-Step Manual Verification Guide

### Scenario: Complete End-to-End Incident Review Flow

1. **Inspect Historical Behavioural Pattern Recognition**:
   - Navigate to **Incident Investigation** in the sidebar.
   - Select Case `CASE-RULA-EVT-1006` (Account: `student_arun`, Terminal: `LAB-PC-04`).
   - Click **Tab 2: Historical Behavioural Analysis**:
     - View the **Login Trend Line Chart** showing cumulative successes (green) and failures (red) with the amber dashed period boundary line.
     - View the **Comparison Bar Chart** showing the failure rate jump from 0% baseline to 60% recent.
     - Read the **Identified Behavioural Pattern** card: *"Sudden Elevation in Authentication Failures"*, supported by events `EVT-04` and `EVT-05`, explaining why recent activity deviates from the historical baseline.

2. **Faculty Sends Clarification Request (Teacher Persona)**:
   - Ensure the active persona is set to **Teacher** (`prof_raman`).
   - Click **Tab 3: Case File & Clarification Inquiry**.
   - Notice the initial message from Dr. S. Raman asking for clarification.
   - Check the **"Record as Private Faculty Note"** checkbox, type: *"Checked security badge swipe: student entered building at 08:30"*, and click **Save Note**.
   - Notice the note is rendered with a yellow background and a `🔒 PRIVATE FACULTY NOTE` banner.

3. **Student Reads Inquiry and Submits Clarification (Student Persona)**:
   - Switch active persona to **Student** (`student_arun`) using the sidebar switcher.
   - In the sidebar, select **Student Portal**.
   - Scroll down to **"My Assigned Incident Cases & Clarification Inquiries"**:
     - Notice Case `CASE-RULA-EVT-1006` is listed with badge **`Clarification Requested`**.
     - Notice the private faculty note is **strictly hidden** from the student!
     - In the reply text area, type: *"My keyboard had Caps Lock on by accident during the morning lab session. I retyped my password and logged in."*
     - Click **Send Reply**.
     - Notice the case review status automatically updates to **`Response Received`**!

4. **Faculty Concludes Investigation & Records Review Outcome**:
   - Switch active persona back to **Teacher** (`prof_raman`).
   - Return to **Incident Investigation** -> **Tab 3: Case File & Clarification Inquiry**.
   - See the student's reply displayed under the student's name with a timestamp.
   - Under **Faculty Case Status & Final Review Verdict**, select status **`Reviewed — No Further Action`**, enter remarks *"Student confirmed Caps Lock error; verified benign"*, and click **Update Case Status**.
   - Expand the **Traceable Case Activity History** table to observe the complete timestamped audit trail of the case from creation to resolution.

---

## 🔒 Prototype Scope & Security Notes
- **Local Demo Environment**: KAAVAL is an educational hackathon demonstration prototype. State persistence uses browser `localStorage`. Role switching allows simulating both student and faculty interactions on a single machine for evaluation. In a production environment, role-based authorization and messaging would be enforced by a hardened backend service with cryptographic identity tokens.
- **Rule Invariance**: The `department_case` column is strictly isolated from detection logic to guarantee impartial, rule-based alerts.
