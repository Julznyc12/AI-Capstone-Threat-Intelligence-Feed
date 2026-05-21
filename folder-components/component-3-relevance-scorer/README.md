# Component 3 — Relevance Scorer

## Overview
Component 3 evaluates threat intelligence records and assigns a relevance score so the pipeline can prioritize the most important items.

This component reads enriched threat records from Airtable, calls the Groq scoring API, and updates each record with a score and status.

## What it does
- Finds records where `Enriched=true` and `Scored=false`
- Sends threat data to the Groq relevance scoring service
- Parses the returned score and level
- Updates the Airtable record with:
  - `Relevance Score`
  - `Relevance Level`
  - `Current Status`
  - `Scored=true`
- Handles API and Airtable update failures by setting `Current Status=Error`

## Inputs
The workflow expects records in Airtable with at least these fields:
- `Title`
- `Summary`
- `Enriched` (boolean)
- `Scored` (boolean)
- `Current Status`

## Airtable schema
The `Threats` table should include these fields:
- `Title` (single line text)
- `Summary` (long text)
- `Enriched` (checkbox or boolean)
- `Scored` (checkbox or boolean)
- `Current Status` (single select with values like `New`, `Enriched`, `Scored`, `Error`)
- `Relevance Score` (number)
- `Relevance Level` (single line text or single select)
- `Error Reason` (long text)

## Outputs
Each processed record is updated with:
- `Relevance Score`
- `Relevance Level`
- `Scored=true`
- `Current Status=Scored`

If processing fails, the record is updated with:
- `Current Status=Error`
- `Error Reason`

## Files in this component
- `Component 3 - Relevance Score.json` — n8n workflow export
- `ERROR_HANDLING.md` — error handling design and troubleshooting
- `IMPLEMENTATION_CHECKLIST.md` — implementation status and next steps
- `README.md` — this file

## How to use
1. Import `Component 3 - Relevance Score.json` into n8n.
2. Confirm Airtable credentials, base, and table settings are correct.
3. Confirm the `Threats` table has:
   - `Error Reason` (long text)
   - `Current Status` with an `Error` option
4. Run the workflow.

## Recommended tests
- Test a valid record with `Enriched=true` and `Scored=false` and verify it updates to `Scored`.
- Test a broken Groq API credential and verify the record updates to `Current Status=Error` with a reason.
- Test a broken Airtable update configuration and verify failure is caught.

## Notes
- The workflow is designed to preserve failed records rather than drop them.
- Error handling is built into both the Groq API call and Airtable update steps.
- Use Airtable views to monitor `Current Status=Error` records for retry and troubleshooting.
