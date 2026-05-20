# Prompt Log - Jesus Alvarez

**Project:** Threat Intelligence Feed Dashboard  
**Team:** Threat Intelligence Feed Dashboard  
**My Component:** Component 1 - Feed Collector  
**AI Tools Used:** GitHub Copilot, ChatGPT

---

# How to Use This Log

This file tracks significant AI interactions used during development of the capstone project.

---

## [2026-05-11] - Capstone Audit Report

**Context:**  
I was working on the capstone integration audit for Checkpoint 2 using GitHub Copilot Chat inside VS Code. The goal was to evaluate the current state of the multi-component AI pipeline and identify workflow gaps before the checkpoint submission.

**Prompt:**  
> I need you to act as a capstone project advisor for a university AI integration course. I'm going to describe my group project, and I need you to interview me about its current state, then produce a structured gap analysis.

**Result:**  
Copilot generated a structured interview process that asked questions about:
- project overview
- Airtable schema
- workflow handoffs
- field consistency
- automation status
- testing coverage

After the interview, Copilot generated a "Checkpoint 2 Readiness Assessment" report with:
- current status
- working components
- critical gaps
- schema issues
- recommended fix order
- test data recommendations

**Evaluation:**  
The audit was useful because it helped identify missing automation between components and weaknesses in end-to-end testing. Some details were generalized and required manual correction, especially around exact schema field names and workflow ownership.

**What I changed:**  
I reviewed the generated recommendations and compared them against the actual project structure. I clarified which components were partially working versus fully automated and verified the Airtable field naming conventions manually.

**What I learned:**  
I learned that AI tools are useful for identifying integration risks and organizing project planning, but outputs still need to be validated carefully against the real implementation.

---

## [2026-05-18] - Generating End-to-End Test Data

**Context:**  
I was preparing a realistic cybersecurity threat intelligence record to test the complete workflow for Checkpoint 2 integration testing.

**Prompt:**  
> Generate a realistic cybersecurity threat intelligence test record that can be used for end-to-end workflow testing in a threat intelligence dashboard project.

**Result:**  
The AI generated a realistic cybersecurity news record containing:
- threat title
- source
- URL
- summary
- timestamps
- threat context

The generated record was later used during end-to-end workflow validation across all four project components.

**Evaluation:**  
The generated test data was realistic and useful for validating workflow automation and Airtable integration.

**What I changed:**  
I adjusted some field formatting to match the Airtable schema and workflow field names used by the team.

**What I learned:**  
I learned that AI-generated test data can accelerate integration testing and help simulate realistic production workflows.

---

## [2026-05-18] - Airtable Field Mapping Validation

**Context:**  
I was reviewing Airtable field consistency between AI Core outputs and Specialist workflow inputs after identifying possible schema inconsistencies during testing.

**Prompt:**  
> Help identify possible Airtable field mismatches between AI enrichment workflows and relevance scoring workflows.

**Result:**  
The AI highlighted several schema areas that required additional validation, including:
- status labels
- IOC field naming
- confidence score formatting
- recommendation field mappings

**Evaluation:**  
The output helped identify areas where naming consistency could become a future integration problem.

**What I changed:**  
I reviewed and validated the Airtable schema manually and standardized several workflow field references.

**What I learned:**  
I learned that maintaining consistent schema conventions is critical when multiple workflows and AI systems interact with the same database.

---

## [2026-05-19] - Debugging Workflow Automation

**Context:**  
During end-to-end testing, I needed to confirm whether records written by Component 1 were automatically triggering AI Core processing.

**Prompt:**  
> Help troubleshoot why an Airtable-triggered AI workflow may not automatically process new records during integration testing.

**Result:**  
The AI suggested validating:
- Airtable trigger conditions
- status labels
- workflow timing
- field mappings
- automation sequencing

The troubleshooting process helped confirm that the ingestion-to-AI-Core handoff was functioning correctly during testing.

**Evaluation:**  
The troubleshooting guidance was useful for validating automation behavior and workflow reliability.

**What I changed:**  
I reviewed workflow trigger conditions and confirmed records were correctly moving into AI processing workflows.

**What I learned:**  
I learned how important workflow orchestration and status consistency are for reliable automation.

---

## [2026-05-20] - Re-Running the Capstone Audit

**Context:**  
I reran the Checkpoint 2 capstone audit after completing end-to-end testing to evaluate current project readiness and remaining integration gaps.

**Prompt:**  
> Review the current capstone project state and generate a Checkpoint 2 readiness assessment with integration gaps, schema risks, and recommended fixes.

**Result:**  
The AI generated an updated readiness assessment containing:
- working components
- validated workflow handoffs
- schema consistency risks
- recommended fix order
- edge-case testing recommendations

**Evaluation:**  
The audit helped summarize the project’s current integration state and identify remaining reliability risks before final submission.

**What I changed:**  
I manually reviewed the generated assessment and adjusted several sections to better reflect the actual state of the workflows and automation testing.

**What I learned:**  
I learned that AI-assisted audits are useful for organizing integration testing and identifying operational risks, but human validation is still necessary to ensure technical accuracy.

---

## [2026-05-20] - n8n Error Handling Logic

**Context:**  
I was improving Component 1 of the Threat Intelligence Feed Dashboard by adding error handling and validation logic to the n8n ingestion workflow. The goal was to prevent malformed or incomplete records from entering the main AI pipeline.

**Prompt:**  
> Help me create an IF node in n8n that validates whether records contain required fields like title, source, url, and raw_summary before entering Airtable.

**Result:**  
The AI helped design an IF node after the Merge node that validates required fields using “is not empty” conditions. Invalid records are automatically routed into a separate Airtable error-handling node.

**Evaluation:**  
The solution worked correctly and allowed the workflow to separate valid records from malformed records automatically. The validation logic improved reliability and demonstrated edge-case handling for Checkpoint 2.

**What I changed:**  
I added validation conditions for:
- title
- source
- url
- raw_summary

I also added an Airtable error logging node that creates records with:
- Error Reason
- Current Status = Error

**What I learned:**  
I learned how to implement validation-based branching in n8n workflows and how error handling can improve automation stability in AI pipelines.

---

## [2026-05-20] - Generating Error Test Records

**Context:**  
I needed to test whether the new error handling branch correctly detected malformed data and routed failed records into Airtable error logs.

**Prompt:**  
> Help me generate a bad test record that will fail the IF node validation and trigger the error handling path.

**Result:**  
The AI suggested intentionally leaving the `raw_summary` field blank inside one normalized feed record to simulate malformed data.

**Evaluation:**  
The test worked successfully. The IF node routed the invalid record into the false branch, and Airtable created an error record with an error message and Error status.

**What I changed:**  
I temporarily modified one normalized record so:
- raw_summary = ""

This triggered the error handling workflow during execution.

**What I learned:**  
I learned how to test edge-case behavior and validate that malformed records are safely handled instead of entering the production pipeline.
