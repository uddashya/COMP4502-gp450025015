# EDA Workspace

## Purpose

This directory contains exploratory Python notebooks and small supporting datasets used during inspection, cleaning, feature engineering, and preliminary analysis.

These materials are separate from the final ARDL reporting workflow in `ARDL/`.

## Typical Contents

- Jupyter notebooks for asset-specific exploratory analysis
- helper CSV files used by the notebooks
- intermediate exploratory outputs that inform later modeling decisions

## Status

The files in this directory should be treated as exploratory rather than final production analysis.

Notebook paths and package usage may vary by file, and this directory does not currently represent a fully standardized Python pipeline.

## Guidance

- Add new exploratory notebooks here rather than at the repository root
- Keep small notebook-specific datasets here when they are not part of the final ARDL workflow
- Promote only stable, final methods into the main econometric workflow when needed
