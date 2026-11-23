# DemandSense Bus
## Scope and Acceptance Criteria

**Course:** SWE 305 – Software Project Management  
**Document Version:** 0.1  
**Date:** November 23, 2026  
**Document Status:** Draft – Pending Review  
**Responsible:** Umut Arda Ekiz (Project Manager)  
**Prepared by:** Yusuf Büyüktaş

---

## 1. Project Overview

DemandSense Bus is a mobile application prototype designed to estimate relative public transportation demand based on authenticated user interactions.

Instead of relying on physical sensors installed inside buses, the system analyzes how users interact with bus lines and stops through the mobile application.

Meaningful interactions are converted into weighted scores and compared with historical reference data to estimate whether the current demand is quieter than usual, normal, or busier than usual.

The system does not calculate exact passenger counts or actual bus occupancy percentages. Its results represent estimated interest based on application activity.

## 2. Project Scope

### 2.1 In-Scope Features

The project includes the following components and functionalities:

**Mobile Application**
- User authentication and sign-in.
- Display of selected pilot bus lines.
- Display of bus stops associated with selected lines.
- Bus line and stop detail screens.
- Relative demand indicators for selected bus lines.

**Backend Services**
- REST API for communication between the mobile application and backend.
- Authentication and session validation.
- Collection and validation of user interaction events.
- Interaction scoring based on predefined rules.
- Prevention of repeated interaction scoring within the cooldown period.
- Calculation of relative demand using historical baseline values.

**Data Management**
- Persistent storage for users, bus lines, stops, interaction events, and historical reference data.
- Storage and retrieval of synthetic historical activity data.
- Short-lived cooldown management.

**Simulation and Testing**
- Synthetic data generation for controlled testing scenarios.
- A small protected administration and testing panel.
- Automated testing of important system functionalities.
- Verification of scoring, cooldown, and demand classification behavior.
- Documentation of test results and known limitations.

**Project Delivery**
- Source code and project documentation.
- Installation and setup instructions.
- Test evidence.
- Final working demonstration.

### 2.2 Out-of-Scope Features

The following functionalities are explicitly excluded from the initial prototype:

- Integration with live municipal transportation systems.
- Installation or integration of physical sensors inside buses.
- Exact passenger counting.
- Reporting validated vehicle occupancy percentages.
- City-wide production deployment.
- Large-scale field testing.
- Operational uptime guarantees.
- Alternative route recommendations.
- Combining physical sensor data with application interaction data.

These capabilities may be considered for future development but are not part of the current project scope.

## 3. Project Constraints

The project is subject to the following constraints:

- Development is planned within an eight-week course project.
- The project team consists of seven members.
- The prototype focuses on a limited number of pilot bus lines and stops.
- Real municipal transportation data is not guaranteed to be available.
- Synthetic datasets may be used for simulation and demonstration.
- The estimated cash budget is between 0 and 1,500 TRY.
- The prototype will be demonstrated in a local development environment.

## 4. Scope Approval

**Status:** Pending Project Manager Review

The scope must be reviewed against the Project Charter and Project Plan before the project's technical rules and acceptance checklist are finalized.

## 5. Technical Rule Decisions

**Decision Status:** Proposed – Pending Team Approval

The following operating rules are based on the DemandSense Bus Project Plan. They must be reviewed and approved by the project team before implementation.

### 5.1 Interaction Scoring Rules

The system uses weighted interaction events to estimate user interest.

| Interaction Type | Condition | Score |
|---|---|---|
| Stop View | A user views a stop for at least 3 seconds | +1 |
| Direct Line Tracking | A user opens direct tracking for a bus line | +3 |
| Short Stop View | A user views a stop for less than 3 seconds | 0 |

The backend validates incoming events and records the authoritative event timestamp.

### 5.2 Cooldown Rules

To prevent repeated interactions from artificially increasing demand scores, the system applies a 15-minute (900-second) cooldown.

The cooldown key consists of:
- Authenticated user identity
- Target type
- Target ID

Only the first eligible event for a specific user and target is accepted during the cooldown period.

Different interaction types for the same target share the cooldown.

An atomic Redis operation will be used to enforce this rule.

An interaction submitted before the 900-second cooldown expires is not counted again.

At exactly 900 seconds, a new interaction becomes eligible.

### 5.3 Demand Aggregation Rules

Accepted interaction events are aggregated using a rolling 15-minute window.

Each stop-view event must include the selected bus line ID.

This prevents shared bus stops from incorrectly contributing to demand scores for multiple lines.

### 5.4 Historical Baseline Comparison

The system compares the current activity score with a historical baseline for the same bus line and corresponding 15-minute time period.

The historical baseline is calculated as the mean activity score from matching 15-minute periods for the same bus line.

When the baseline is positive, the demand ratio is calculated as:

**Demand Ratio = Current Activity Score / Historical Baseline Score**

The proposed classification rules are:

| Demand Ratio | Classification |
|---|---|
| Below 0.80 | Quieter Than Usual |
| 0.80 to 1.20 (inclusive) | Normal |
| Above 1.20 | Busier Than Usual |

### 5.5 Missing or Zero Baseline

If historical baseline data is unavailable or the baseline score is zero, the system returns:

**Insufficient Reference Data**

The system must not calculate a demand ratio using a zero baseline.

Synthetic historical samples may be used to demonstrate the system's demand classification functionality.

### 5.6 Security and Validation

The system will:
- Associate accepted interaction events with authenticated users.
- Validate incoming event fields on the server.
- Use server-side timestamps for authoritative event timing.
- Apply request rate limiting.
- Reject unauthorized requests to protected endpoints.
- Minimize the personal data stored by the prototype.

### 5.7 Rule Approval

The following decisions require team confirmation before implementation:

- Aggregation window definition.
- Demand classification thresholds.
- Cooldown key and event-sharing semantics.
- Attribution of shared bus stops to selected lines.

**Approval Status:** Pending Review

## 6. Project Acceptance Criteria

The following acceptance criteria are based on the DemandSense Bus Project Plan.

Each criterion defines a condition that must be verified before the final project delivery.

### AC1 — End-to-End User Functionality

The application must allow an authenticated user to:

- Sign in successfully.
- Browse selected pilot bus lines.
- View stops associated with the selected lines.
- View relative demand information.

**Verification:** Recorded end-to-end test scenario and demonstration.

### AC2 — Interaction Scoring Accuracy

The system must correctly apply interaction scoring rules:

- A 2.9-second stop view contributes 0 points.
- A 3.0-second stop view contributes +1 point.
- Direct line tracking contributes +3 points.

**Verification:** Automated tests with predefined inputs and expected scores.

### AC3 — Cooldown Enforcement

The system must prevent repeated interactions from artificially increasing demand scores.

- Duplicate interactions for the same authenticated user and target must be suppressed before 900 seconds.
- A new eligible interaction must be accepted at exactly 900 seconds.
- Concurrent duplicate requests must not bypass the cooldown mechanism.

**Verification:** Cooldown boundary tests and concurrent-request tests.

### AC4 — Synthetic Data Simulation

The system must support a simulation with at least 20 distinct synthetic users.

The simulation must demonstrate that user interactions can change the displayed demand category when historical reference values are configured accordingly.

**Verification:** Saved simulation data, baseline values, calculated scores, and demand categories before and after execution.

### AC5 — Demand Classification Accuracy

The system must correctly classify relative demand into:

- Quieter Than Usual
- Normal
- Busier Than Usual

Tests must verify:

- The 0.80 demand-ratio boundary.
- The 1.20 demand-ratio boundary.
- Missing historical baseline data.
- Zero baseline values.

**Verification:** Automated classification and boundary tests.

### AC6 — Security and Access Control

The system must:

- Reject unauthorized access to protected endpoints.
- Validate authenticated sessions.
- Enforce configured API request limits.

**Verification:** API tests involving invalid authentication and rate-limit responses.

### AC7 — Installation and Final Delivery

The project must provide:

- A working local setup.
- Installation and configuration instructions.
- Test evidence.
- Documentation of known limitations.
- A final repository release tag.
- A project demonstration.

**Verification:** Clean installation rehearsal, final repository review, and demonstration.

---

## 7. Acceptance Checklist

| ID | Acceptance Criterion | Verification Evidence | Status |
|---|---|---|---|
| AC1 | End-to-end user functionality | Scenario and demo | Not Executed |
| AC2 | Interaction scoring accuracy | Automated rule tests | Not Executed |
| AC3 | Cooldown enforcement | Boundary and concurrency tests | Not Executed |
| AC4 | 20-user synthetic simulation | Simulation results | Not Executed |
| AC5 | Demand classification accuracy | Classification tests | Not Executed |
| AC6 | Security and access control | API security tests | Not Executed |
| AC7 | Installation and final delivery | Setup rehearsal and demo | Not Executed |

**Possible Test Statuses:**
- Not Executed
- Passed
- Failed
- Blocked

The status of each acceptance criterion will be updated as testing progresses.

## 8. Review and Approval

**Document Status:** Draft – Pending Review

**Prepared by:** Yusuf Büyüktaş

**Project Manager:** Umut Arda Ekiz

**Approval Status:** Pending

The project manager and team must review the scope, technical rules, and acceptance criteria before adopting them as the approved project baseline.