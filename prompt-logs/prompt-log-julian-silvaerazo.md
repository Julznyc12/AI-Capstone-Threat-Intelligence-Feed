# Prompt Log — Julian Silva-Erazo

**Project:** Threat Intelligence Feed Dashboard  
**Team:** Jesus Alvarez, Harry Yachimba, Julian Silva-Erazo, Safayet Safin  
**My Component:** Component 3: Relevance Scorer  
**AI Tools Used:** GitHub Copilot

---

## How to Use This Log

Add an entry for each significant AI interaction:
- Copilot Chat conversations where you asked it to generate, explain, or debug something
- Moments where Copilot was wrong and you had to fix it (these are the most valuable entries)
- Cases where you refined a prompt to get a better result

Don't log: every autocomplete of a bracket or variable name.

---

## Entries

### [2026-05-20] — Creating the Relevance Scoring Component README and Workflow Logic

**Context:**  
I was working on Component 3: Relevance Scorer for the Threat Intelligence Feed Dashboard project. I had the Airtable schema, n8n workflow, and Flowise scoring chain open in VS Code while using GitHub Copilot Chat to help structure the README documentation and scoring workflow logic.

**Prompt:**
> Create a README.md for the Relevance Scorer component of our Threat Intelligence Feed Dashboard. Explain what the component does, inputs/outputs, Airtable fields used, workflow steps in n8n, and how relevance scores are calculated against the organization’s tech stack.

**Result:**  
Copilot generated a structured README that included:
- component overview
- workflow inputs and outputs
- Airtable field mappings
- scoring field examples
- setup instructions for Groq API, Flowise, and n8n
- workflow steps for processing enriched threat records

It also suggested fields like:
- relevance_score
- relevance_level
- matched_tech
- relevance_reason
- scored_at

**Evaluation:**  
The output was mostly accurate and gave a strong starting structure, but some field names did not exactly match the Airtable schema being used in the project. Copilot also assumed some workflow steps were fully automated even though parts of the pipeline still required manual validation and testing.

**What I changed:**  
I updated the README to match the exact Airtable schema and corrected several field names so they aligned with the actual database tables. I also added more accurate workflow descriptions based on how our n8n and Flowise integrations were currently functioning. Some sections were simplified to better reflect the real implementation instead of theoretical automation.

**What I learned:**  
I learned that Copilot is very useful for generating documentation structure and workflow explanations quickly, but it still requires careful validation against the actual project implementation. Providing detailed project context and schema information significantly improves the quality of the generated outputs.
---
### [2026-05-21] — Debugging the Checkpoint 2 Failure with Copilot

**Context:**
I was troubleshooting a Checkpoint 2 end-to-end failure where the integration test was breaking during the final scoring and dashboard update step.

**Prompt:**
> Analyze this failure in our n8n + Airtable integration: the Checkpoint 2 end-to-end flow fails when writing a scored threat record, and the error mentions a field mismatch and invalid expression. Suggest likely causes and how to fix it.

**Result:**
Copilot identified that the issue likely came from an Airtable field name mismatch between the Relevance Scoring table and the n8n write node. It also suggested validating the field names in both the automation and the scoring formula.

**Evaluation:**
The guidance was useful and matched the actual bug; I confirmed the field name mismatch and corrected the n8n node configuration.

**What I changed:**
I updated the n8n workflow to use the exact Airtable field names and repaired the expression used to populate `current_status`.

**What I learned:**
Copilot is good at pointing out likely integration issues when given the surrounding context of the error and the affected systems.
---

### [2026-05-22] — Fixing a Field Name Mismatch

**Context:**
During field validation, I found that the Relevance Scoring table was using `relevance_level` but the scoring workflow still populated a field named `severity_level`.

**Prompt:**
> Help me fix this Airtable field mismatch. The workflow currently writes `severity_level` into the scoring table, but the table schema uses `relevance_level`. Provide a corrected field mapping and a short checklist for schema alignment.

**Result:**
Copilot provided a corrected mapping and a checklist emphasizing snake_case consistency, exact field names, and checking automation field references.

**Evaluation:**
The checklist helped me find one more indirect mismatch in the `matched_tech` mapping, and the field mapping advice prevented a future integration break.

**What I changed:**
I corrected the workflow mapping, renamed the field in the scoring payload, and added a quick schema alignment checklist to my project notes.

**What I learned:**
Explicitly naming the exact schema fields and comparing them directly to the Airtable table structure avoids repeated integration failures.
---

### [2026-05-23] — Generating Integration Test Data

**Context:**
I needed realistic test data for the integration test covering ingestion, enrichment, scoring, and dashboard output.

**Prompt:**
> Generate 10 sample threat intelligence records for an end-to-end integration test. Include fields for `title`, `source`, `url`, `published_date`, `raw_summary`, `affected_software`, `attack_type`, `iocs`, `matched_tech`, and `relevance_reason`.

**Result:**
Copilot produced realistic cybersecurity threat examples with fields that matched our schema and included varied severity and relevance scenarios.

**Evaluation:**
The generated test data saved time and helped uncover edge cases in the scoring logic, especially for threats with multiple matched technologies.

**What I changed:**
I adapted the generated examples into Airtable test records and used them to validate the end-to-end n8n flow.

**What I learned:**
Copilot can rapidly create useful test dataset examples when prompts include the exact schema and expected field types.
---

### [2026-05-24] — Debugging a Broken n8n Expression

**Context:**
One of the n8n automation expressions used to calculate `current_status` was failing, causing the workflow to stop before writing the final scored data.

**Prompt:**
> Debug this n8n expression: it should set `current_status` to `scored` when scoring is complete, but the node returns an error and prevents the workflow from continuing. Suggest a corrected expression and explain why the original fails.

**Result:**
Copilot explained the expression error and suggested a corrected version that evaluated the checkbox and text fields properly.

**Evaluation:**
The fix worked immediately, and the workflow resumed successfully through the scoring-to-dashboard step.

**What I changed:**
I replaced the broken expression with the corrected version and added a validation step to catch expression errors before the workflow runs.

**What I learned:**
When debugging automation expressions, it's important to provide the exact node context and the expected output format to Copilot.
---

### [2026-05-25] — Re-running the Audit Prompt and Comparing Results

**Context:**
After updates to the workflow and schema, I re-ran the audit prompt to compare results and confirm that the fixes were reflected in the project documentation.

**Prompt:**
> Re-run the audit prompt for the Threat Intelligence Feed Dashboard project and compare the new results to the previous audit. Highlight any changes in the Current State, schema updates, and known issues.

**Result:**
Copilot produced a refreshed audit summary and noted the schema changes, Checkpoint 2 findings, and improved automation state.

**Evaluation:**
The new audit prompted me to document the latest project status and confirm that the current state description was accurate.

**What I changed:**
I updated the project audit section in `copilot-instructions.md` with the current state and issue list, then saved the prompt log entry here.

**What I learned:**
Using Copilot to re-run audits after code or schema changes is a good way to keep project documentation aligned with actual progress.
---