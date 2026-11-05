# SAW | OOC Criteria and Prediction Limits

Public report: https://nameuser-byte.github.io/SAW-Analysis/

## Current entrypoint

`index.html` contains an English contribution summary followed by the detailed Korean validation record and inline figures. The report uses educational synthetic data. It does not claim actual Fab operation, causal identification, production optimization or independent programming.

## Publication update — 2026-11-05

Replaces the earlier public entrypoint with the existing frozen validation report. Model fitting and frozen metric files were not changed. Old scripts, charts and CSVs elsewhere in this repository are historical materials, not the performance reference for this entrypoint.

Personal contribution: Requested row-by-row comparison of supplied and recalculated OOC labels and checked calculation ranges and formulas, while a teammate reviewed the reference rules.

AI contribution: implementation, calculations, figures, report generation and this publication update. Later validation is distinguished from original team work.

## Local preview

Run `python -m http.server 8780` from this repository, then open `http://127.0.0.1:8780/`. GitHub Pages is static: no Python inference endpoint is provided. Interactive examples use stored predictions or explicitly limited research rules.

## Evidence and limitations

Educational synthetic data. The official subgroup structure and OOC rules are unknown. Non-significant tests do not establish absence of an effect or independence. Positive wear-speed correlation does not demonstrate operator compensation. The browser tool below provides only research checks, not production forecasts.

Primary copied evidence: `SENSOR_PLAN.md`, `audit.json`, `claims.json`, `legacy_reproduction.json`, `results.json`, `saw_evidence.csv`. Data beside the report: `saw_evidence.csv`. See `PUBLICATION_MANIFEST.json` for source hashes and publication checks. Original educational data and frozen analysis artifacts remain unchanged in the source workspace.
