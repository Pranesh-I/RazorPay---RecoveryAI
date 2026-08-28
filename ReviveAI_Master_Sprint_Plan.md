# ReviveAI — Master Sprint Plan

> **Project:** ReviveAI — Autonomous AI Revenue Recovery Agent  
> **Track:** AI Revenue Recovery  
> **Deadline:** September 5  
> **Core objective:** Build a complete closed-loop system that detects revenue loss, predicts recovery potential, selects the safest and highest-value intervention, executes it through Razorpay Test Mode, observes the outcome, learns from it, and maintains a complete audit trail.

---

# 1. Final Definition of Done

By the end of the project, the system should demonstrate:

```text
Payment / Checkout Event
        ↓
Revenue Leak Detected
        ↓
Recovery Case Created
        ↓
ML Prediction
        ↓
Action Evaluation
        ↓
Best Action Selected
        ↓
Policy Guardrails
        ↓
Action Executed
        ↓
Customer Completes Recovery
        ↓
Revenue Measured
        ↓
Learning Update
        ↓
Audit Trail
```

The final project should solve two primary revenue-loss scenarios:

1. **Failed Payment Recovery**
2. **Checkout Abandonment Recovery**

---

# 2. Recommended Architecture

```text
                         MERCHANT SYSTEM
                               │
               ┌───────────────┴────────────────┐
               │                                │
        Checkout Events                    Payment Events
               │                                │
               └───────────────┬────────────────┘
                               ▼
                    EVENT INGESTION LAYER
                               │
                               ▼
                     REVENUE LEAK DETECTOR
                               │
                  ┌────────────┴────────────┐
                  │                         │
            Payment Failure            Checkout Abandonment
                  │                         │
                  └────────────┬────────────┘
                               ▼
                        CASE BUILDER
                               │
                               ▼
                    RECOVERY INTELLIGENCE
                  ┌────────────┴─────────────┐
                  │                          │
                  ▼                          ▼
        Recovery Propensity Model      Action Selection
                  │                          │
                  └────────────┬─────────────┘
                               ▼
                     EXPECTED VALUE ENGINE
                               │
                               ▼
                       POLICY GUARDRAILS
                               │
                               ▼
                       EXECUTION ENGINE
                               │
                               ▼
                       RAZORPAY TEST MODE
                               │
                               ▼
                      OUTCOME OBSERVATION
                               │
                               ▼
                     LEARNING + ANALYTICS
                               │
                               ▼
                         AUDIT TRAIL
```

---

# 3. Tech Stack

## Frontend

- Next.js
- TypeScript
- Tailwind CSS
- shadcn/ui
- TanStack Query
- Zustand
- Recharts
- Framer Motion

## Backend

- Python
- FastAPI
- Pydantic
- SQLAlchemy
- Alembic
- httpx

## Async Processing

- Celery
- Redis

## Database

- PostgreSQL
- Supabase

## Machine Learning

- CatBoost
- scikit-learn
- Pandas
- NumPy
- SHAP
- Joblib

## Decision Learning

- Contextual Multi-Armed Bandit
- Thompson Sampling

## Payments

- Razorpay Test Mode
- Razorpay Payment Links
- Razorpay Webhooks

## Optional AI Layer

- LLM only for explanations and personalized messages
- LLM must **not** directly control money-related actions

---

# SPRINT 0 — PROJECT FOUNDATION

## Goal

Create the technical foundation and freeze the project architecture.

## Status

🔴 Not Started

## Tasks

- [ ] Create GitHub repository
- [ ] Create monorepo structure
- [ ] Initialize Next.js frontend
- [ ] Initialize FastAPI backend
- [ ] Set up PostgreSQL/Supabase
- [ ] Create Razorpay Test Mode account
- [ ] Obtain test API keys
- [ ] Configure environment variables
- [ ] Create `.env.example`
- [ ] Set up Redis
- [ ] Define database schema
- [ ] Create initial architecture diagram
- [ ] Freeze MVP scope

## Deliverable

A working local system:

```text
Frontend → FastAPI → PostgreSQL
```

## Done When

The frontend loads successfully and:

```text
GET /health
```

returns:

```json
{
  "status": "healthy"
}
```

---

# SPRINT 1 — BUILD THE RECOVERY WORLD

## Goal

Create a realistic synthetic customer and recovery environment that can generate training and evaluation data.

## Status

🔴 Not Started

## Why This Sprint Matters

Real merchant recovery data is usually private and unavailable. Therefore, we create a synthetic environment that behaves realistically.

The important principle is:

> The simulator can contain hidden customer behavior variables, but the ML model must never see them.

## Tasks

### 1. Customer Generator

Generate observable features:

- customer_type
- account_age_days
- previous_orders
- previous_successful_payments
- average_order_value
- days_since_last_purchase

Generate hidden variables:

- true_purchase_intent
- payment_capability
- price_sensitivity
- customer_patience
- channel_responsiveness

The hidden variables influence outcomes but are never used as ML features.

### 2. Checkout Generator

Generate:

- checkout_started_at
- checkout_duration
- cart_value
- checkout_stage
- device_type
- hour_of_day
- day_of_week

### 3. Payment Event Generator

Generate events:

- SUCCESS
- FAILED
- ABANDONED

Failure reasons:

- bank_timeout
- network_error
- insufficient_funds
- technical_error
- user_cancelled

### 4. Recovery Simulator

For each case:

```text
Customer Context
+
Transaction Context
+
Recovery Action
+
Hidden Customer Behaviour
+
Randomness
        ↓
Outcome
```

Generate:

- recovered
- recovered_amount
- customer_response
- fatigue_score
- opt_out

## Deliverable

```text
synthetic_recovery_dataset.csv
```

Target: **10,000+ scenarios**

## Done When

The generated dataset contains varied customers, transaction values, failure reasons, actions, and outcomes.

---

# SPRINT 2 — ML INTELLIGENCE

## Goal

Train the Recovery Propensity Model.

## Status

🔴 Not Started

## Main Question

> How likely is this revenue-loss case to be recovered?

## Recommended Model

**CatBoost Classifier**

CatBoost is suitable because the project contains many categorical features such as:

- failure_reason
- customer_type
- event_type
- payment_method
- time_bucket

## Input Features

```text
amount
event_type
failure_reason
customer_type
previous_orders
customer_lifetime_value
payment_attempts
previous_failures
cart_value
checkout_duration
hour_of_day
day_of_week
previous_recovery_attempts
```

## Target

```text
recovered
```

Where:

```text
1 = recovered
0 = not recovered
```

## Tasks

- [ ] Clean the dataset
- [ ] Handle missing values
- [ ] Define features
- [ ] Prevent hidden-variable leakage
- [ ] Split training and test data
- [ ] Train CatBoost
- [ ] Evaluate the model
- [ ] Save the trained model
- [ ] Generate feature importance
- [ ] Add SHAP explainability

## Metrics

Measure:

- ROC-AUC
- Precision
- Recall
- F1 Score
- Calibration

## Deliverables

```text
model.cbm
metrics.json
feature_importance.json
```

## Done When

The backend can perform:

```text
POST /predict
```

and return:

```json
{
  "recovery_probability": 0.82
}
```

---

# SPRINT 3 — ACTION DECISION ENGINE

## Goal

Build the intelligence that chooses the best recovery intervention.

## Status

🔴 Not Started

## Available Actions

```text
WAIT
RETRY
PAYMENT_LINK
REMINDER
STOP
MANUAL_REVIEW
```

## Core Idea

The system should not simply predict:

> Will this customer recover?

Instead, it should evaluate:

> What is the expected value of each possible intervention?

## Expected Value Formula

```text
Expected Value
=
Recovery Probability
×
Amount at Risk

-
Intervention Cost

-
Customer Fatigue Penalty
```

Example:

```text
Payment Link

Recovery Probability = 0.82
Amount = ₹10,000

Expected Recovery = ₹8,200

Intervention Cost = ₹10
Fatigue Penalty = ₹200

Expected Value = ₹7,990
```

## Decision Learning

Use a **Contextual Multi-Armed Bandit with Thompson Sampling**.

Each action is treated as an arm:

```text
WAIT
RETRY
PAYMENT_LINK
REMINDER
STOP
```

Context includes:

- customer history
- transaction value
- failure reason
- previous attempts
- checkout behavior

Reward is based on business value.

## Deliverable

```text
POST /decision
```

Example response:

```json
{
  "recommended_action": "PAYMENT_LINK",
  "expected_value": 7990,
  "alternatives": [
    {
      "action": "RETRY",
      "expected_value": 6200
    },
    {
      "action": "REMINDER",
      "expected_value": 5100
    }
  ]
}
```

## Done When

Different customer contexts produce different recommended actions.

---

# SPRINT 4 — POLICY AND SAFETY ENGINE

## Goal

Ensure the AI cannot perform unlimited, unsafe, or non-compliant actions.

## Status

🔴 Not Started

## Example Policies

```text
MAX_RETRIES = 2

MAX_RECOVERY_MESSAGES = 2

CUSTOMER_OPTOUT = HARD_STOP

HIGH_VALUE_THRESHOLD = ₹50,000

MIN_EXPECTED_VALUE = ₹100
```

## Flow

```text
AI Recommendation
        ↓
Policy Engine
        ↓
Allowed / Blocked / Manual Review
```

## Example

```text
Recommendation:
PAYMENT_LINK

Policy Checks:

Customer opted out?       NO
Message limit reached?    NO
Expected value positive?  YES
Manual approval needed?   NO

Result:
APPROVED
```

## Tasks

- [ ] Implement retry limits
- [ ] Implement message limits
- [ ] Implement customer opt-out
- [ ] Implement manual review threshold
- [ ] Implement minimum expected value
- [ ] Store every policy evaluation

## Done When

You can intentionally trigger a blocked action and display:

```text
ACTION BLOCKED

Reason:
Maximum recovery attempts reached.
```

---

# SPRINT 5 — EVENT AND RECOVERY ORCHESTRATION

## Goal

Connect all intelligence components into one complete pipeline.

## Status

🔴 Not Started

## Events

```text
PAYMENT_FAILED
CHECKOUT_STARTED
CHECKOUT_ABANDONED
PAYMENT_RECOVERED
```

## Recovery Lifecycle

```text
DETECTED
    ↓
ANALYZING
    ↓
DECIDED
    ↓
POLICY_CHECKED
    ↓
EXECUTING
    ↓
RECOVERED / FAILED / STOPPED / MANUAL_REVIEW
```

## Full Pipeline

```text
Event
 ↓
Revenue Leak Detection
 ↓
Create Recovery Case
 ↓
Collect Context
 ↓
ML Prediction
 ↓
Decision Engine
 ↓
Policy Evaluation
 ↓
Execution
```

## Tasks

- [ ] Event ingestion API
- [ ] Recovery case creation
- [ ] Feature collection
- [ ] ML inference
- [ ] Decision engine integration
- [ ] Policy integration
- [ ] Status transitions

## Done When

A single event can trigger the complete recovery pipeline.

---

# SPRINT 6 — RAZORPAY REAL INTEGRATION

## Goal

Connect the recovery workflow to a real payment infrastructure using Razorpay Test Mode.

## Status

🔴 Not Started

## Tasks

- [ ] Configure Razorpay Test Mode
- [ ] Create payment/order flow
- [ ] Integrate Payment Links
- [ ] Receive and verify payment status
- [ ] Implement webhook receiver
- [ ] Handle payment failures
- [ ] Handle payment success
- [ ] Connect recovery actions to Razorpay

## Target Demo Flow

```text
Payment fails
       ↓
Webhook arrives
       ↓
Recovery case created
       ↓
AI evaluates the case
       ↓
Payment Link selected
       ↓
Policy approves
       ↓
Razorpay Payment Link created
       ↓
Customer completes test payment
       ↓
Success webhook received
       ↓
Revenue marked as recovered
```

## Done When

You can demonstrate:

```text
FAILED PAYMENT
       ↓
AI DECISION
       ↓
RAZORPAY RECOVERY LINK
       ↓
SUCCESSFUL TEST PAYMENT
       ↓
REVENUE RECOVERED
```

---

# SPRINT 7 — LEARNING LOOP

## Goal

Allow the decision system to learn from observed outcomes.

## Status

🔴 Not Started

## Flow

```text
Action
   ↓
Outcome
   ↓
Reward Calculation
   ↓
Bandit Update
```

## Reward Formula

```text
Reward
=
Recovered Amount
-
Intervention Cost
-
Customer Fatigue Penalty
```

Examples:

```text
₹10,000 recovered
→ Positive reward

Reminder ignored
→ Small negative reward

Customer opts out
→ Larger negative reward
```

## Tasks

- [ ] Observe recovery outcomes
- [ ] Calculate reward
- [ ] Store reward
- [ ] Update bandit parameters
- [ ] Record before/after action confidence

## Done When

You can show that an observed outcome updates the action-learning system.

---

# SPRINT 8 — AUDIT AND EXPLAINABILITY

## Goal

Make every automated action explainable and traceable.

## Status

🔴 Not Started

## Audit Example

```text
10:00 — Payment failure detected
10:00 — Recovery case created
10:01 — Customer context collected
10:01 — Recovery probability predicted: 82%
10:01 — Actions evaluated
10:01 — PAYMENT_LINK selected
10:01 — Policy checks passed
10:02 — Payment Link created
10:10 — Payment completed
10:10 — ₹10,000 recovered
```

## Decision Explanation

Display:

```text
WHY THIS ACTION?

Positive Signals:
+ Returning customer
+ Temporary payment failure
+ Strong purchase history

Risk Signals:
- High transaction value

Recovery Potential:
82%

Recommended Action:
PAYMENT_LINK

Expected Value:
₹7,990
```

## Done When

Every important system action can be traced from event to final outcome.

---

# SPRINT 9 — FRONTEND COMMAND CENTER

## Goal

Build a visually strong, real-time product interface.

## Status

🔴 Not Started

## Main Screens

### 1. Revenue Dashboard

Display:

- Revenue at risk
- Revenue recovered
- Recovery rate
- Net recovery value
- Active recovery cases

### 2. Recovery Cases

Display:

- Case ID
- Customer
- Amount
- Problem type
- AI recommendation
- Status

### 3. Case Details

Display:

```text
Payment Failed

₹15,000 at risk

Recovery Potential:
82%

Recommended Action:
PAYMENT_LINK

Expected Value:
₹12,100

Policy:
PASSED

Audit Trail:
Failure Detected
Prediction Generated
Action Selected
Payment Link Created
Revenue Recovered
```

### 4. Live Agent Activity

Display the system working:

```text
Detecting failed payment...
Evaluating recovery potential...
Comparing interventions...
Checking policy...
Executing action...
Observing outcome...
Revenue recovered.
```

## Done When

A judge can understand the entire system by following one recovery case through the UI.

---

# SPRINT 10 — BATCH EXPERIMENT AND VALIDATION

## Goal

Prove that ReviveAI performs better than simpler recovery strategies.

## Status

🔴 Not Started

## Experiment

Run the same batch of cases through:

### Strategy 1 — No Intervention

```text
Do nothing
```

### Strategy 2 — Rule-Based Recovery

```text
Apply fixed rules
```

### Strategy 3 — ReviveAI

```text
ML + Decision Learning + Policy Engine
```

## Metrics

Measure:

- Total revenue recovered
- Recovery rate
- Number of interventions
- Net recovery value
- Average value per intervention
- Customer fatigue
- Number of actions avoided

## Deliverable

A reproducible experiment:

```text
python run_experiment.py
```

## Done When

You have measured evidence comparing ReviveAI against baseline strategies.

---

# SPRINT 11 — FAILURE HANDLING

## Goal

Demonstrate that the system handles execution failure safely.

## Status

🔴 Not Started

## Failure Scenario

```text
AI selects:
CREATE PAYMENT LINK
```

But the external API fails.

```text
Attempt 1 → FAILED

Wait

Attempt 2 → FAILED

Maximum attempts reached

STOP

Move case to manual review

Create audit event
```

## Required Protection

- No infinite retries
- No duplicate payment creation
- No duplicate customer messages
- Complete failure logging
- Manual review fallback

## Done When

The dashboard can show:

```text
RECOVERY EXECUTION FAILED

No duplicate payment was created.
Maximum retry limit reached.
Case moved to manual review.
```

---

# SPRINT 12 — FINAL DEMO AND HACKATHON PREPARATION

## Goal

Prepare a reliable 3–5 minute demonstration.

## Status

🔴 Not Started

## Demo Story

### Step 1 — Explain the Problem

A merchant loses revenue because customers:

- experience payment failures
- abandon checkout

### Step 2 — Trigger a Real Case

```text
Priya attempts a ₹15,000 payment.
The payment fails.
```

### Step 3 — ReviveAI Detects Revenue at Risk

```text
₹15,000 Revenue At Risk
```

### Step 4 — Show Intelligence

```text
Recovery Potential: 82%

WAIT          → ₹2,000 expected value
RETRY         → ₹7,800 expected value
PAYMENT LINK  → ₹12,100 expected value
```

### Step 5 — Show Guardrails

```text
✓ Customer has not opted out
✓ Attempt limit not reached
✓ Expected value is positive
✓ No manual approval required
```

### Step 6 — Execute

```text
Razorpay Payment Link Created
```

### Step 7 — Recover

Complete the test payment.

```text
₹15,000 RECOVERED
```

### Step 8 — Show Batch Proof

Compare:

```text
Baseline
vs
Rule-Based
vs
ReviveAI
```

### Step 9 — Show Graceful Failure

Demonstrate an external API failure and manual-review fallback.

## Done When

You have:

- [ ] Live demo
- [ ] Backup recorded demo
- [ ] Architecture diagram
- [ ] ML metrics
- [ ] Business metrics
- [ ] Batch experiment results
- [ ] Failure scenario
- [ ] Final pitch

---

# 4. Critical Milestones

## Milestone 1 — Brain Alive

Complete:

- Sprint 0
- Sprint 1
- Sprint 2
- Sprint 3
- Sprint 4

Expected result:

```text
Input Case
    ↓
ML Prediction
    ↓
Action Selection
    ↓
Policy Approval / Rejection
```

---

## Milestone 2 — Agent Alive

Complete:

- Sprint 5
- Sprint 6
- Sprint 7

Expected result:

```text
Real Event
    ↓
Agent Decision
    ↓
Recovery Action
    ↓
Observed Outcome
    ↓
Learning Update
```

---

## Milestone 3 — Product Alive

Complete:

- Sprint 8
- Sprint 9
- Sprint 10
- Sprint 11

Expected result:

```text
Working Product
+
Audit Trail
+
Analytics
+
Batch Validation
+
Failure Handling
```

---

## Milestone 4 — Hackathon Ready

Complete:

- Sprint 12

Expected result:

```text
Live Demo
+
Backup Demo
+
Pitch
+
Architecture
+
Measured Results
```

---

# 5. Master Sprint Status Board

| Sprint | Module | Goal | Status |
|---|---|---|---|
| S0 | Foundation | Project infrastructure running | 🔴 Not Started |
| S1 | Simulation | Generate realistic recovery data | 🔴 Not Started |
| S2 | ML | Train recovery propensity model | 🔴 Not Started |
| S3 | Decision Engine | Select highest-value action | 🔴 Not Started |
| S4 | Safety | Bound all AI actions | 🔴 Not Started |
| S5 | Orchestration | Connect the complete pipeline | 🔴 Not Started |
| S6 | Razorpay | Execute real test-mode recovery | 🔴 Not Started |
| S7 | Learning | Learn from action outcomes | 🔴 Not Started |
| S8 | Audit | Explain and trace decisions | 🔴 Not Started |
| S9 | Frontend | Build recovery command center | 🔴 Not Started |
| S10 | Validation | Prove business impact | 🔴 Not Started |
| S11 | Failure Handling | Demonstrate graceful failure | 🔴 Not Started |
| S12 | Demo | Final hackathon preparation | 🔴 Not Started |

---

# 6. Recommended Execution Order

Do not build every sprint strictly one after another without testing.

## Phase 1 — Core Intelligence

```text
S0 → S1 → S2 → S3 → S4
```

At the end:

> The AI brain should work completely through APIs or scripts before serious frontend development.

## Phase 2 — Closed-Loop Agent

```text
S5 → S6 → S7
```

At the end:

> A real event should enter the system, trigger a decision, execute a recovery action, and produce an observed outcome.

## Phase 3 — Product and Proof

```text
S8 → S9 → S10 → S11
```

At the end:

> The product should be explainable, visually clear, experimentally validated, and resilient to failure.

## Phase 4 — Hackathon Ready

```text
S12
```

---

# 7. Final Project Principle

The dashboard is not the core innovation.

The core innovation is:

> **A revenue-loss event enters ReviveAI → the system estimates recovery potential → compares possible interventions → selects the highest-value safe action → checks deterministic policies → executes the action through Razorpay → observes the result → updates its learning → records the complete audit trail.**

That complete closed loop is what the project should demonstrate.
