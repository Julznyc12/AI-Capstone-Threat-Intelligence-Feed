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
