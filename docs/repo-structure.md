# Repository Structure

## Purpose

This document explains the current repository structure and the reason it is not being fully refactored into a stricter `analysis/` and `data/` layout.

## Principles

- Preserve the existing ARDL workflow without editing analysis code
- Separate final econometric work from exploratory notebook work
- Make the repository readable and defensible for supervisors, reviewers, and collaborators

## Directory Roles

### `ARDL/`

This directory contains the final R Markdown scripts used for the asset-level econometric analysis.

It also contains the datasets those scripts currently depend on through relative paths. Because the scripts are path-coupled, the `ARDL/` directory is treated as a stable unit and should not be reorganized unless the R code is intentionally refactored.

### `EDA/`

This directory contains exploratory Python notebooks and small supporting datasets used during inspection, preprocessing, and early-stage analysis. These files are not assumed to be part of the final production pipeline.

### `docs/`

This directory contains repository-level documentation that explains layout, conventions, and project policy.

## Why `ARDL/` Was Not Restructured

A cleaner long-term design would separate code and data into top-level `analysis/` and `data/` directories. That change was not applied here because the current requirement is to avoid any code edits to the existing ARDL scripts.

Without code changes, moving the ARDL files or their dependent datasets would create a reproducibility risk. For that reason, the repository has been professionalized around the current layout rather than through internal refactoring.

## Suggested Rules Going Forward

- Keep final R analysis in `ARDL/` unless you are ready to refactor paths
- Put new exploratory notebooks in `EDA/`
- Avoid adding new loose files at the repository root
- Document any new dataset placed in `EDA/` or `ARDL/FYP data/`
- If a future refactor is approved, move to a root-based path system before relocating files
