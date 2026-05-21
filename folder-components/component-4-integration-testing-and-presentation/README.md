# Component 4: Integration, Testing & Presentation

**Owner:** Safayet Safin

## Overview
This component integrates all previous stages of the Threat Intelligence Feed Dashboard. It takes the processed data and presents it in an accessible, interactive Airtable dashboard. This allows security analysts to quickly view and analyze prioritized threat intelligence without digging through raw logs or databases.

## Tools Used
* Airtable Dashboard
* GitHub
* draw.io

## Data Flow
* **Input:** Prioritized threat records from Airtable. These records have already been normalized, summarized, and scored by Components 1, 2, and 3.
* **Output:** A visual dashboard displaying prioritized threat intelligence, AI generated summaries, and indicators of compromise (IOCs).

## Key Features
* **Unified View:** Brings together the feed collection, AI summarization, and relevance scoring into a single interface.
* **Filtering and Sorting:** Security analysts can easily filter threats based on severity levels or relevance scores to focus on the most critical issues first.
* **Clear Visuals:** Displays extracted IOCs and concise threat summaries for rapid decision making.

## Standalone Demo
The demo for this component highlights the Airtable dashboard in action. It showcases how to view the prioritized threats and utilize the filtering and search functions to isolate specific security alerts based on their severity or relevance to the organization.

<div align="center">
  <img src="https://github.com/user-attachments/assets/d55be9e4-a9d5-421c-892b-d01d143cb52b" alt="Airtable Dashboard View" width="800">
</div>

## Testing Details
Testing for this component ensures that data from all previous components flows correctly into the final Airtable views. It verifies that:
1. Summaries and IOCs extracted by Component 2 appear correctly in the designated fields.
2. Relevance scores assigned by Component 3 map accurately to the sorting functions.
3. The dashboard UI updates properly as new threats are ingested from the pipeline.
4.
