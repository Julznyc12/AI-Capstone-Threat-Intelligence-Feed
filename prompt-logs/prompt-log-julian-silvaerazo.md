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
### [2026-05-21] — Writing n8n Error Handling Logic with Copilot

**Context:**
I was implementing error handling for the n8n workflow that processes threat intelligence records into the relevance scorer. I needed robust handling for malformed payloads and API failures while preserving traceability for later debugging.

**Prompt:**
> Help me write n8n error handling logic to catch malformed record payloads, handle Flowise API failures gracefully, and route failed records for review. Include retry policies, logging, and how to preserve original input data for later inspection.

**Result:**
Copilot suggested using a combination of n8n error triggers, conditional nodes, and a dedicated error-handling branch. It recommended:
- catching exceptions from the Flowise request node
- sending failed records to a separate error review Airtable table
- using a retry loop with backoff for transient API errors
- adding a JSON validation step before scoring

**Evaluation:**
The generated logic helped me structure the error path and identify gaps in our current workflow. I had to adjust Copilot’s suggestions to match n8n’s actual node naming and our existing Airtable structure.

**What I changed:**
I replaced generic node names with our workflow’s actual node labels, and I simplified the decision logic so failed records are routed only if the payload fails validation or if the score service returns an unrecoverable error.

**What I learned:**
Copilot can help design error handling patterns effectively, but its recommended implementation details still need to be aligned precisely with the platform’s node model and the project’s data schema.
---

### [2026-05-21] — Designing the Confidence Routing Threshold

**Context:**
I needed to decide how to route relevance scores based on confidence and whether low-confidence records should go to a manual review queue or continue through the pipeline.

**Prompt:**
> Help me design a confidence routing threshold for relevance scores. Recommend a threshold value and routing strategy for high confidence, low confidence, and uncertain records in our threat intelligence scoring pipeline.

**Result:**
Copilot recommended a three-tier routing strategy:
- high confidence (score > 0.8) → auto-approve and publish
- medium confidence (0.5–0.8) → flag for review but still include in downstream processing
- low confidence (< 0.5) → route to manual review or quarantine

It also suggested attaching a `confidence_reason` field to explain why a record was classified as low or medium confidence.

**Evaluation:**
The suggestion was useful as a starting point, but I needed to refine the threshold bands to align with our data distribution and risk posture. I also clarified that `medium confidence` records should remain visible in the dashboard while still being highlighted for review.

**What I changed:**
I adjusted the bands to use `0.75` as the high-confidence cutoff and `0.4` as the low-confidence cutoff based on our initial relevance score histograms. I also documented the rationale in the workflow so the team can tune the threshold later.

**What I learned:**
Copilot is helpful for framing routing strategies and generating rationale, though final thresholds must come from actual score distributions and team risk preferences.
---

### [2026-05-21] — Generating Test Records for the Error Path

**Context:**
I wanted concrete test data that would exercise the error-handling branch in the scoring pipeline, especially malformed structured fields and missing required threat attributes.

**Prompt:**
> Generate test records that should trigger the n8n error path. Include examples of malformed JSON, missing required fields, invalid URL formats, and unsupported threat categories.

**Result:**
Copilot produced several realistic test cases, including:
- payloads missing `threat_type` or `source_url`
- malformed `affected_products` JSON strings
- invalid `confidence_score` values outside the expected numeric range
- unsupported category labels like `unknown-threat`

**Evaluation:**
The generated records were directly useful for building automated tests and manually validating the error branch. I had to sanitize a couple of examples where the JSON format was still valid but semantically wrong for our schema.

**What I changed:**
I converted the Copilot examples into Airtable import rows and added additional test cases for empty arrays and `null` values in required fields.

**What I learned:**
Copilot can rapidly generate useful test data variations, but I must always validate them against the actual schema constraints and processing logic.
---

### [2026-05-21] — Debugging a Dashboard View Filter Expression

**Context:**
I was troubleshooting a dashboard filter expression that was not returning expected low-confidence records in the review queue.

**Prompt:**
> Debug this dashboard view filter expression: `AND({relevance_score} < 0.75, {status} != "archived", OR({confidence_status} = "low", {review_flag} = 1))`. Explain why it may be excluding the expected records.

**Result:**
Copilot explained that the `OR` condition can still fail if `confidence_status` is missing or if `review_flag` is not exactly numeric `1`. It suggested explicitly handling empty values and verifying field types in the filter.

**Evaluation:**
The insight was correct and led me to discover that some records had blank `confidence_status` values, causing the filter to exclude them even though `relevance_score` and `status` matched.

**What I changed:**
I updated the filter expression to include a fallback for missing confidence, and I cleaned the source data so `review_flag` was consistently stored as a number.

**What I learned:**
Copilot can help debug logical filter expressions effectively, especially by pointing out hidden edge cases like blank field values and type mismatches.
---

### [2026-05-21] — Refining the Flowise Prompt for Relevance Scoring

**Context:**
I was iterating on the Flowise prompt used to generate relevance scores for threat records. The model was returning inconsistent score distributions, so I needed help tightening the instruction set.

**Prompt:**
> Refine a scoring prompt for an LLM to assign a normalized relevance score between 0 and 1 for threat intelligence records. Emphasize technology stack matching, threat severity, and evidence strength.

**Result:**
Copilot drafted a concise prompt that included:
- clear scoring criteria and numeric range
- examples of high/medium/low relevance scenarios
- a requirement to justify scores with a short reason
- a normalization step for all output values

**Evaluation:**
The prompt was much stronger than the original version and helped reduce ambiguous results in early tests. I still needed to validate the exact output format against our parser.

**What I changed:**
I added explicit instructions for the model to output JSON with `score`, `confidence`, and `reason` fields only. I also included a requirement to use `0.00`–`1.00` formatting.

**What I learned:**
Copilot can help convert vague scoring requirements into a more disciplined LLM prompt, but the model’s output structure must still be enforced in the pipeline.
---

### [2026-05-21] — Asking Copilot for API Retry Strategy and Rate Limit Handling

**Context:**
Our integration with external APIs occasionally hit transient failures and rate limits. I wanted a retry strategy that balanced reliability with not overloading the service.

**Prompt:**
> Suggest an API retry strategy for n8n that handles HTTP 429 rate limit responses and 5xx transient errors. Include recommended wait times, retry limits, and when to fail fast.

**Result:**
Copilot recommended:
- exponential backoff for 5xx errors
- a fixed delay after 429 responses using `Retry-After` when available
- a maximum of 3 retries
- failing immediately for 4xx errors other than 429

**Evaluation:**
The recommendations were well aligned with standard API best practices. I used them as the basis for our n8n retry node configuration.

**What I changed:**
I configured the workflow to inspect `Retry-After` headers and to stop retrying on invalid auth or bad request errors. I also logged all retries to our error review table.

**What I learned:**
Copilot can provide good retry heuristics, and it helps to turn those ideas into concrete workflow settings quickly.
---

### [2026-05-21] — Validating Dashboard Filter Behavior with Sample Records

**Context:**
After fixing the filter expression, I wanted to validate it with sample dashboard records to ensure the review queue included the intended low-confidence items.

**Prompt:**
> Create sample dashboard records that will test a view filter for `relevance_score < 0.75`, not archived, and low confidence or review flag set. Include records that should pass and fail.

**Result:**
Copilot generated six sample record descriptions covering:
- low-score + low-confidence + not archived (should pass)
- low-score + missing confidence_status + review_flag = 1 (should pass)
- low-score + archived (should fail)
- high-score + low-confidence (should fail)
- low-score + blank review_flag (should fail unless confidence_status = low)
- low-score + low-confidence + archived (should fail)

**Evaluation:**
The test cases were helpful to verify filter logic and to catch edge cases with blank fields. They also helped me confirm that the dashboard view should behave consistently.

**What I changed:**
I added these samples to our manual QA checklist and used them to confirm the filter after changing the query.

**What I learned:**
Copilot is useful for turning a logical condition into specific testing examples, especially when validating dashboard behavior.
---

### [2026-05-21] — Documenting the Manual Review Pipeline for Low-Confidence Records

**Context:**
I needed to document the manual review workflow so the team could understand how low-confidence threat records are handled once they leave the automatic scoring pipeline.

**Prompt:**
> Write a short step-by-step description of a manual review pipeline for records flagged as low-confidence. Include who should review them, what fields to inspect, and how to reclassify or archive them.

**Result:**
Copilot produced a practical review workflow that included:
- triage by the analyst team
- inspecting `relevance_reason`, `confidence_reason`, and `matched_tech`
- checking source URLs and threat evidence
- editing `review_flag`, `confidence_status`, and `status`
- archiving false positives

**Evaluation:**
The draft was a solid foundation for our team guide. I tailored it further to our actual Airtable field names and review responsibilities.

**What I changed:**
I renamed generic field references to our specific columns and added a note to preserve original raw payloads for audit purposes.

**What I learned:**
Copilot can accelerate documentation of operational processes, but the final version needs to reflect the real team roles and data model.
