# dbCloudGate

**GCP Onboarding Platform** — an interactive, single-page prototype (POC) that guides an application team through the end-to-end journey of onboarding an on-premises application to Google Cloud Platform (GCP).

dbCloudGate turns a fragmented, document-heavy migration intake process into a single guided workflow: you provide a NAR ID and architecture documentation, and the platform "reads" it, extracts the technical profile, scores readiness, estimates cost, recommends a GCP blueprint, raises the change request, and walks you through Discovery and Design.

---

## Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [The Onboarding Workflow](#the-onboarding-workflow)
- [Lifecycle Phases](#lifecycle-phases)
- [Sample Applications](#sample-applications)
- [Concepts & Terminology](#concepts--terminology)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [Project Structure](#project-structure)
- [Disclaimer](#disclaimer)

---

## Overview

dbCloudGate is a **self-contained, front-end-only demonstration** of a cloud migration onboarding tool for a large regulated enterprise (e.g. a bank). The entire application — UI, styling, state management, sample data, and simulated "AI" analysis — lives in a single `index.html` file with no build step and no backend.

It simulates how an **IT Application Owner (ITAO)** would take an application from initial identification through readiness evaluation, demand management, discovery, and design, with an embedded assistant to answer questions and flag blockers along the way.

The header identifies it as the **dbCloudGate — GCP Onboarding Platform**, running in a **POC** environment.

## Key Features

- **Guided, multi-step stepper UI** — A sticky top navigation shows lifecycle bands and the individual steps within the current phase, including completed, current, and blocked states.
- **NAR-based application lookup** — Enter a NAR ID and the platform auto-resolves the application name, description, owning domain, domain lead, data classification, and recommended migration type.
- **Simulated AI document analysis** — Uploaded architecture documents / links are "read" with an animated Vertex AI / Document AI experience that types out the extracted profile, upstream/downstream systems, and component mappings.
- **Architecture extraction & GCP mapping** — Presents the as-is architecture (SQL Server DW, SSIS, dbt, Tableau, etc.) and maps each component to a recommended GCP target service, each backed by an Architecture Decision Record (ADR).
- **Readiness scoring** — A weighted, multi-dimensional readiness score (out of 100) with `READY` / `CONDITIONALLY READY` / `NOT READY` status.
- **Gap register with blockers** — Tracks gaps by severity (`BLOCKER`, `HIGH`, `MEDIUM`, `LOW`). Open blockers gate progression; resolving them lifts the score.
- **Migration-type fit analysis** — Compares `ReHost`, `RePlatform`, and `ReArchitect` and highlights the suggested approach.
- **Cost estimation** — Side-by-side current (on-prem) vs. target (GCP) monthly cost breakdown, with cost notes and confidence levels.
- **Blueprint recommendation** — Recommends a primary GCP blueprint (with fit score, ERI reference, and pros/cons) plus an alternative.
- **Change request (CR) intake** — A conversational intake flow that collects the remaining details, raises a CR / Epic, and manages toll-gate and change-control approvals.
- **Discovery & Design checklists** — Role-tagged task lists (ITAO, SRE, Security, Data Eng, BI team, etc.) for the Discovery and Design phases.
- **Embedded assistant** — A floating help panel (`dbCloudGate Assistant`) that provides context-aware guidance, suggested questions, and blocker explanations.
- **Toast notifications** — Inline feedback for lookups, uploads, and approvals.

## The Onboarding Workflow

The application walks through nine stages, grouped into phases:

| # | Stage | Phase | Purpose |
|---|-------|-------|---------|
| 01 | **Application** | Readiness Evaluation | Identify the application by NAR ID, confirm domain & domain lead, select migration type, and provide architecture documentation. |
| 02 | **Architecture** | Readiness Evaluation | Review and confirm the auto-extracted architecture profile, upstream/downstream systems, and GCP component mapping. |
| 03 | **Readiness** | Readiness Evaluation | View the weighted readiness score and work through the gap register, resolving blockers. |
| 04 | **Recommendation** | Readiness Evaluation | Review cost analysis and the recommended GCP blueprint. |
| 05 | **Raise CR** | Demand Management | Conversational intake that captures remaining details and raises the change request / Epic. |
| 06 | **Approval** | Demand Management | Domain Lead and Demand Management approval. |
| 07 | **Change Control** | Demand Management | Toll-gate and change-control sign-off. |
| 08 | **Discovery** | Discovery | Role-based discovery checklist (questionnaire, data residency, landing zone, outstanding actions). |
| 09 | **Design** | Design | Design checklist covering core and migration-type-specific / regulated-data tasks. |

Progression is **gated**: open blockers prevent moving past blockable stages (Readiness, Raise CR, Approval, Change Control).

## Lifecycle Phases

The right-hand rail tracks the full application lifecycle, of which this POC actively covers the first phases:

1. Readiness Evaluation
2. Demand Management
3. Discovery
4. Cloud Onboarding *(out of scope in this POC)*
5. Design
6. Build
7. Testing and Final Design
8. Deployment / Release Management
9. On-Prem Decommissioning

## Sample Applications

Two demo applications are bundled so the workflow can be explored end-to-end without real data:

| NAR ID | Application | Domain | Data Classification | Recommended Migration |
|--------|-------------|--------|---------------------|-----------------------|
| `118135-1` | **EDW-Core** | Corporate Shared Function | Confidential — no PII in scope | RePlatform |
| `103799-1` | **EDW-Regulatory** | Regulatory Reporting | Strictly Confidential — Regulated (GDPR, MiFID II) | RePlatform |

Both are modelled on an on-premises **Microsoft SQL Server 2019** enterprise data warehouse (~1 TB) with SSIS, Python/Pandas, dbt Core, and Tableau, mapped to GCP targets such as **BigQuery, Cloud Composer, Dataflow, Datastream, Looker, Pub/Sub, Cloud KMS/DLP**, etc.

The regulated example (`103799-1`) demonstrates additional controls: DPIA, data classification, column-level security, VPC Service Controls, KMS/DLP, and EU-only data residency.

## Concepts & Terminology

- **NAR ID** — Application identifier used to look up the application profile from the register.
- **ITAO** — IT Application Owner (the primary user driving the request).
- **Domain / Domain Lead** — Business domain owning the application and the lead who must approve the request (each domain has a lead, backup, email, and SLA).
- **Migration Types** — `ReHost` (lift & shift), `RePlatform` (managed services with targeted change), `ReArchitect` (cloud-native redesign).
- **ADR** — Architecture Decision Record referenced for each target component (e.g. `ADR-BI-002` mandating Looker over Tableau for BigQuery in the EU).
- **ERI** — Enterprise Reference Implementation associated with a blueprint.
- **Blueprint (GCP-BP-…)** — A recommended reference architecture with a fit score.
- **DEx Tool** — Data Exchange Registry used for data-residency requests.
- **Landing Zone (LZ)** — The provisioned GCP environment for the application.

## Tech Stack

- **HTML5** — single-page structure.
- **CSS3** — custom design system defined with CSS variables (navy/blue enterprise palette), responsive layout, animations.
- **Vanilla JavaScript** — all application logic, state management (`S` state object), rendering, and simulated AI interactions; no frameworks.
- **Google Fonts** — IBM Plex Sans and IBM Plex Mono.
- **Inline SVG** — icons and the architecture diagrams.

There are **no external dependencies, no build tooling, and no backend** — everything runs in the browser.

## Getting Started

Because dbCloudGate is a single static file, running it is trivial:

### Option 1 — Open directly
Clone the repository and open `index.html` in any modern web browser:

```bash
git clone https://github.com/PinkuBarman/dbCloudGate.git
cd dbCloudGate
# then open index.html in your browser
```

### Option 2 — Serve locally (recommended)
Serving over HTTP avoids any browser file restrictions:

```bash
# Python 3
python -m http.server 8000
# then visit http://localhost:8000
```

### Try it out
1. On the first screen, look up a sample NAR ID: `118135-1` or `103799-1`.
2. The domain, domain lead, and suggested migration type populate automatically.
3. Choose a migration type and click **load sample documents** (or add a sample link).
4. Click **Upload and analyse** and watch the simulated analysis.
5. Confirm the architecture, resolve any blockers, review cost and blueprint, and continue through the remaining stages.
6. Use the floating assistant (bottom-right) for context-aware help at any step.

## Project Structure

```
dbCloudGate/
├── index.html   # The entire application: UI, styles, state, data, and logic
└── README.md    # This file
```

## Disclaimer

This is a **proof-of-concept / demonstration** environment. All applications, people, domains, email addresses, cost figures, and "AI" analysis are **sample/simulated data** for illustration only and do not represent real systems or a live GCP onboarding process.
