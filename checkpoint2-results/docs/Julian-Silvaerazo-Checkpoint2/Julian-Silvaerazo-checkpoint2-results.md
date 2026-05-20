# Checkpoint 2 Results

**Date:** 2026-05-20  
**Team:** Julian Silva-erazo, Jesus Alvarez, Safayet Safin, Harry Yachimba

**Test record:** A cybersecurity news article titled “Microsoft Open-Sources RAMPART and Clarity to Secure AI Agents During Development” collected from The Hacker News RSS feed. The record included the article title, source, URL, published date, raw summary, and timestamp.

## End-to-End Status: PASSED

## Component-by-Component Results

### Ingestion
- **Status:** Working
- **What happened:** The ingestion workflow successfully collected the cybersecurity article from The Hacker News RSS feed and inserted the record into Airtable with the correct fields populated, including title, source, URL, published date, raw summary, and timestamp.
- **Screenshot Included:** running-component1.png

### AI Core
- **Status:** Working
- **What happened:** The AI Core workflow successfully detected the new Airtable record and processed the article using AI enrichment workflows. The system populated multiple analysis fields including severity classification, identified software, attack type, IOC information, enrichment timestamps, and processing status fields.
- **Screenshot Included:** running-component2.png

### Specialist
- **Status:** Working
- **What happened:** The Specialist/scoring workflow successfully processed the AI Core output and generated additional enrichment data including relevance scores, relevance labels, current status indicators, severity updates, and calculated confidence-related outputs. The workflow successfully updated the Airtable record automatically.
- **Screenshot Included:** running-component3.png

### Integration Dashboard
- **Status:** Working
- **What happened:** The fully processed record became visible inside the dashboard/interface view. The dashboard displayed the processed threat intelligence record along with analysis information and workflow-generated outputs, confirming successful end-to-end integration across all four components.
- **Screenshot Included:** component4-dashboard.png

## Gaps Found

- Some AI enrichment fields occasionally return incomplete or inconsistent values depending on the article content.
- Severity classifications are heavily concentrated in MEDIUM and HIGH categories, with very few LOW severity outputs.
- IOC extraction occasionally returns “None” when articles do not explicitly contain indicators of compromise.
- Dashboard updates may require a short refresh delay before newly processed records appear.
- Groq/Hugging Face API rate limits may slow processing when handling larger batches of records.
- Certain Airtable field mappings required adjustment during testing to maintain consistency between workflows.

## Fix Plan

1. Improve AI prompt engineering to generate more balanced severity classifications and improve IOC extraction accuracy — AI Core Team — Medium effort.
2. Add batching and wait-node logic in n8n workflows to reduce API rate-limit issues during larger processing runs — Workflow Team — Medium effort.
3. Standardize Airtable field names and workflow mappings across all components to prevent future integration mismatches — Integration Team — Low effort.

