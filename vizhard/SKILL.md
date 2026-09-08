---
name: vizhard
description: The master orchestrator and reception intake for the data visualization workshop pipeline. Gathers user intent, establishes audience and narrative posture, inspects file availability, and coordinates downstream artisan skills (/barber, /cobbler, /carpenter, /tailor, /critique, /janitor) through strict sequential handoffs.
---

# Vizhard

## Overview

Vizhard is the front-desk orchestrator and intake checkpoint for the visualization pipeline. It does not parse raw tables, write visual encodings, or generate rendering code directly. Instead, it interviews the user to lock down the core communication intent, frames the demographic posture, verifies file presence, and calls each specialized workshop artisan in sequence with a clear handoff contract.

## Core Rule

Never proceed into data optimization or visualization without locking down the **Intent & Posture Contract**. Showing "everything" is a failure state. If the user invokes `/vizhard` without explicit demographic or intent parameters, halt immediately and present the triage intake menu.

Once intent is established, call the downstream skills one by one. Never let an artisan overstep its boundary or bleed into adjacent responsibilities.

## Intent Taxonomy

Every project must be anchored to exactly one primary intent posture before calling `/barber`:

- `a. general-overview`: Exploratory, balanced, accessible. Gives a clean orientation of the data landscape without aggressive editorial bias.
- `b. pattern-discovery`: High-density analytical discovery. Focused on correlations, clusters, anomalies, and multi-variable distributions.
- `c. dataset-juxtaposition`: Relational and comparative. Built to transpose or compare the target data against an external benchmark, baseline, or secondary dataset.
- `d. impact-shock`: Provocative, visceral, and narrative-led. Prioritizes extreme disparities, systemic tension, or surprising scale contrasts.
- `e. playful-expressive`: Casual, gamified, or data-art-oriented. Uses unconventional visual forms to entertain, delight, or invite tactile interaction.

## Workshop Sequence & Routing

Vizhard orchestrates the following pipeline order:

```
[User Request]
       │
       ▼
 1. /vizhard (Intake Checkpoint) ──► Produces INTENT_BRIEF
       │
       ▼
 2. /barber                      ──► Prunes & optimizes data guided by INTENT_BRIEF
       │
       ▼
 3. /cobbler                     ──► Classifies NOIR scale & maps to perceptual channels
       │
       ▼
 4. /carpenter                   ──► Decides Translate / Transform / Transpose operations
       │
       ▼
 5. /tailor + /apprentice-tailor ──► Establishes aesthetic, color systems, and code spec
       │
       ▼
 6. /critique                    ──► Quality review gate; routes backward if defects exist
       │
       ▼
 7. /janitor                     ──► Strips dead code, cleans artifacts, verifies syntax
```

## Intake Protocol

When invoked (e.g., `using /vizhard create a data visualisation for the csv file in this directory`), follow these operational steps:

### Step 1: Detect Dataset & Missing Parameters
1. Check the active directory or context for tabular files (`.csv`, `.tsv`, `.parquet`).
2. If multiple files exist, confirm which file is the primary target.
3. Review user prompt for explicit intent, audience demographic, or focal topic.

### Step 2: Present the Intake Triage
If intent or demographic context is absent, output the intake questionnaire:

```markdown
### 📋 Vizhard Intake Checkpoint

Target Dataset: `[detected_file_name.csv]`

Before the workshop begins, what is the core intent and audience for this piece?

**1. Primary Intent:**
- [a] **General Overview**: Accessible landscape summary of the data
- [b] **Pattern Discovery**: Deep-dive analytical correlations, distributions, or outliers
- [c] **Dataset Juxtaposition**: Benchmarking or transposing against an external baseline
- [d] **Impact & Shock**: Revealing visceral contrasts, scale disparities, or editorial tension
- [e] **Playful & Expressive**: Data-art, casual exploration, or unconventional mechanics

**2. Target Audience / Context:**
- Who is reading this, and what action, insight, or emotion should they walk away with?
```

### Step 3: Compile Handoff Contract
Once the user confirms or provides their choices, synthesize the intent into an internal handoff block:

```yaml
handoff_contract:
  dataset_path: "imdb_top_1000.csv"
  intent_mode: "impact-shock" # [general-overview | pattern-discovery | dataset-juxtaposition | impact-shock | playful-expressive]
  audience_demographic: "General filmgoers curious about box office vs. critical prestige"
  editorial_priority: "Highlight extreme budget/gross imbalances and rating discrepancies"
  must_protect_fields: ["Series_Title", "IMDB_Rating", "Gross", "Meta_score"]
  status: "dispatched-to-barber"
```

### Step 4: Dispatch Downstream
Pass the `handoff_contract` directly to `/barber`. Instruct `/barber` to prioritize fields marked in `must_protect_fields` while pruning the rest according to the selected intent mode.

## State Transitions & Loop Control

- **Pre-Barber Halt:** If the dataset cannot be located or is empty, halt and ask for the file path.
- **Critique Bounce:** If `/critique` rejects an artifact with `revise before signoff`, read the critique findings and re-dispatch only to the faulted artisan (`/cobbler`, `/carpenter`, or `/tailor`). Do not rerun `/barber` unless the data schema itself was judged insufficient.
- **Terminal Handoff:** Once `/critique` issues `pass` or `pass with cuts`, dispatch the artifact to `/janitor` for final sanitation before presenting the finished piece to the user.
