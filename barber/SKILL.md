---
name: barber
description: Tabular data pruner and sanitizer. Consumes the intent brief from /vizhard, audits raw datasets for token bloat, embedded delimiter collisions, and dirty numeric fields, and trims the dataset to its essential analytical core while protecting designated intent fields.
---

# Barber

## Overview

Barber is the data trimmer and sanitizer of the workshop. It receives the dataset and `handoff_contract` from `/vizhard` [cite: 2], shears away non-essential columns, strips high-overhead token sinks, sanitizes numbers, and eliminates delimiter collision risks [cite: 1]. Barber does not classify statistical variable types or design visual encodings; it hands off a lean, safe, and normalized tabular foundation to `/cobbler` [cite: 2].

## Core Rule

Every cut must be justified by the `intent_mode` and `editorial_priority` received from `/vizhard` [cite: 2]. Never delete fields listed under `must_protect_fields` in the handoff contract [cite: 2].

Never print truncated row sequences into the conversation [cite: 1]. If the trimmed data exceeds 150 rows, Barber writes and runs a deterministic Python script to export the sanitized data and passes only the shape, profile, and head/tail sample forward [cite: 1].

## Trimming Rules by Intent Mode

Barber applies differentiated pruning criteria based on `/vizhard`'s intent classification [cite: 2]:

- `general-overview`:
  - Keep: 4–6 core macro fields (key identifier, 1 primary category, 2 primary quantitative metrics, 1 temporal field) [cite: 1].
  - Prune: Secondary categories, sub-rankings, narrative summaries, and redundant IDs [cite: 1].
- `pattern-discovery`:
  - Keep: High-density multi-variable dimensions (keep multiple continuous numerics, discrete counts, and categorical groupings to enable multi-axis correlation or clustering).
  - Prune: Unstructured narrative text, external URLs, and redundant unique identifiers [cite: 1].
- `dataset-juxtaposition`:
  - Keep: Standardized normalization keys (dates, ISO country codes, standardized names, standardized currencies/units) necessary to join or align against external benchmarks.
  - Prune: Internal platform-specific metadata, secondary local categorizations, and verbose text [cite: 1].
- `impact-shock`:
  - Keep: Extreme contrast fields, maximum/minimum outlier attributes, and high-disparity metrics specified in `must_protect_fields` [cite: 2]. Keep short punchy title/name identifiers to preserve human-scale recognition [cite: 1].
  - Prune: Moderating middle-ground fields, secondary metadata, long plot descriptions, and boilerplate links [cite: 1].
- `playful-expressive`:
  - Keep: Expressive, quirky, or tactile attributes (e.g., character names, colors, short quotes, runtimes, ratings, discrete tags).
  - Prune: Heavy narrative paragraphs, bureaucratic codes, and irrelevant administrative columns [cite: 1].

## Universal Sanitation Protocol

1. **Delimiter Collision Neutralization:**
   - Detect embedded commas, unescaped quotes, or line breaks inside text cells [cite: 1].
   - Convert default CSV comma separation (`,`) to pipe (`|`) or tab (`\t`) delimiters to prevent token desynchronization [cite: 1].
2. **Numeric De-junking:**
   - Strip currency symbols (`$`, `€`, `¥`), percentage signs, and thousand commas (`28,341,469` $\rightarrow$ `28341469`) [cite: 1].
   - Convert measurements with textual unit tags (`142 min` $\rightarrow$ `142`) and rename headers to reflect unit (`runtime_min`) [cite: 1].
3. **Null & Missing Value Handling:**
   - Cast ambiguous null strings (`"N/A"`, `"none"`, `"-"`, `""`) to standardized empty strings or explicit `null` [cite: 1].
   - If a row has nulls in `must_protect_fields`, flag or drop according to user intent [cite: 2].
4. **Header Sanitization:**
   - Normalize all column names to lower `snake_case` without punctuation or whitespace [cite: 1].

## Workflow

```mermaid
graph TD
    VizhardContract[Vizhard Handoff Contract] --> Audit[1. Schema & Token Sink Audit]
    Audit --> IntentFilter[2. Intent-Guided Column Pruning]
    IntentFilter --> Sanitize[3. Numeric & Delimiter Sanitation]
    Sanitize --> Verify{Row Count <= 150?}
    Verify -->|Yes| DirectPayload[Inline Delimited Block]
    Verify -->|No| ScriptExport[Python Pandas Export & Profile]
    DirectPayload --> CobblerHandoff[Handoff to /cobbler]
    ScriptExport --> CobblerHandoff
```

1. **Read Vizhard Handoff:**
   Extract `dataset_path`, `intent_mode`, and `must_protect_fields` [cite: 2].
2. **Execute Python Inspection:**
   Run pandas to get initial shape, memory usage, column null counts, and character lengths [cite: 1].
3. **Apply Intent Shearing:**
   Drop non-essential columns while protecting designated intent fields [cite: 1, 2].
4. **Clean Types & Delimiters:**
   Cast numeric columns, sanitize headers, and export to pipe-delimited format (`|`) [cite: 1].
5. **Output Handoff Spec to `/cobbler`:**
   Generate clean summary profile and path to the sanitized file [cite: 2].

## Handoff Contract to `/cobbler`

```yaml
barber_handoff:
  original_file: "imdb_top_1000.csv"
  sanitized_file: "imdb_top_1000_sanitized.tsv"
  delimiter: "|"
  row_count: 1000
  columns_retained:
    - name: "series_title"
      raw_header: "Series_Title"
      status: "retained_identifier"
    - name: "released_year"
      raw_header: "Released_Year"
      status: "retained_temporal"
    - name: "runtime_min"
      raw_header: "Runtime"
      status: "sanitized_integer"
    - name: "genre"
      raw_header: "Genre"
      status: "retained_categorical"
    - name: "imdb_rating"
      raw_header: "IMDB_Rating"
      status: "sanitized_float"
    - name: "meta_score"
      raw_header: "Meta_score"
      status: "sanitized_float"
    - name: "gross_usd"
      raw_header: "Gross"
      status: "sanitized_integer"
  columns_dropped: ["Poster_Link", "Overview", "Certificate", "Star3", "Star4"]
  token_reduction_pct: "78%"
  ready_for_cobbler: true
```
