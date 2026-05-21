# Component 2: AI Summarizer & IOC Extractor

## What It Does

This component automates the analysis of threat intelligence articles using AI-powered summarization and indicator extraction. It receives raw threat intelligence text from the Feed Collector (Component 1), processes it through Flowise LLM chains powered by Groq, and produces structured threat data: summaries, severity classifications, attack types, IOCs (malware names, IPs, domains, CVEs), and remediation recommendations.

---

## How It Connects to Other Components

### Inputs (from Component 1: Feed Collector)
| Field | Type | Example |
|-------|------|---------|
| `input_text` | Long Text | Raw article text about a threat (300–5000 words) |
| `status` | Single Select | `"new"` (triggers Component 2 to process) |

### Processing
- Receives triggered webhook or scheduled poll from n8n
- Sends `input_text` to Flowise LLM chain
- Parses JSON response from Groq API

### Outputs (written to Airtable, consumed by Component 3: Relevance Scorer)
| Field | Type | Example |
|-------|------|---------|
| `summary` | Long Text | "Critical RCE vulnerability in Apache Log4j discovered..." |
| `severity` | Single Select | `critical`, `high`, `medium`, `low` |
| `attack_type` | Text | "Remote Code Execution" |
| `indicators` | Long Text | JSON array: `["CVE-2021-44228", "192.168.1.1", "malware.com"]` |
| `recommendations` | Long Text | "Update Log4j to 2.17.1 or later..." |
| `status` | Single Select | `"summarized"` (signals Component 3 to score) |

---

## Setup Instructions

### Prerequisites
- **Flowise Cloud account** (with LLM chain already deployed)
- **Groq API key** (free tier available at console.groq.com)
- **n8n Cloud account** (with HTTP Request node permissions)
- **Airtable base** with "Threat Intelligence" table matching schema above

### Step 1: Set Up Groq API Key

1. Go to [console.groq.com](https://console.groq.com)
2. Sign up or log in
3. Create an API key in **Keys** section
4. Copy the key (you'll paste it into Flowise in Step 2)

### Step 2: Configure Flowise LLM Chain

**Note:** If your team has already deployed a Flowise chain, skip to Step 3.

1. Log in to [Flowise Cloud](https://flowiseai.com)
2. Create a new Chatflow with these nodes:
   - **Input:** Text input node (label: "input_text")
   - **LLM:** Groq model node
     - Model: `mixtral-8x7b-32768` (fast) or `llama2-70b-4096` (more accurate)
     - API Key: Paste your Groq API key
   - **Prompt:** Create a prompt template:
     ```
     You are a cybersecurity threat intelligence analyst. 
     Read the following threat article and respond with ONLY valid JSON (no markdown, no extra text):
     
     Article: {input_text}
     
     Return JSON in this format:
     {
       "summary": "2-3 sentence summary of the threat",
       "severity": "critical|high|medium|low",
       "attack_type": "e.g., Remote Code Execution, SQL Injection, Phishing",
       "indicators": ["CVE-2021-44228", "192.168.1.100", "malware.example.com"],
       "recommendations": "Bullet-point remediation steps"
     }
     ```
   - **Output:** JSON output node
3. Test with sample threat article text
4. Deploy and copy the **Prediction API endpoint URL** (format: `https://flowiseai.com/api/v1/prediction/<chain-id>`)

### Step 3: Configure n8n Workflow (Trigger & Update)

**Workflow: Component 2 – Summarize & Extract IOCs**

1. Log in to [n8n Cloud](https://n8n.io)
2. Create a new workflow with these nodes:

   **Node 1: Airtable Trigger**
   - Type: Airtable trigger (or HTTP Poll if using webhook)
   - Event: "Record updated" or "Record created"
   - Condition: `status` field equals `"new"`
   - Base & Table: Select your Threat Intelligence table

   **Node 2: Extract input_text**
   - Type: Airtable Read Records
   - Pull the full record to access `input_text` field
   - Or use a Set node to extract: `{{ $json.fields.input_text }}`

   **Node 3: Call Flowise LLM Chain**
   - Type: HTTP Request
   - Method: `POST`
   - URL: Paste your Flowise prediction endpoint
   - Headers: `Content-Type: application/json`
   - Body (JSON):
     ```json
     {
       "input_text": "{{ $json.fields.input_text }}"
     }
     ```
   - Response: Expect JSON with summary, severity, attack_type, indicators, recommendations

   **Node 4: Parse JSON Response**
   - Type: Set
   - Extract fields from Flowise response:
     ```
     summary: {{ $json.text }} or {{ $json.summary }}
     severity: {{ $json.fields.severity }}
     attack_type: {{ $json.fields.attack_type }}
     indicators: {{ $json.fields.indicators }}
     recommendations: {{ $json.fields.recommendations }}
     ```

   **Node 5: Update Airtable Record**
   - Type: Airtable Update
   - Record ID: `{{ $json.id }}` (from original trigger)
   - Fields to update:
     - `summary`: `{{ $json.summary }}`
     - `severity`: `{{ $json.fields.severity }}`
     - `attack_type`: `{{ $json.fields.attack_type }}`
     - `indicators`: `{{ JSON.stringify($json.fields.indicators) }}`
     - `recommendations`: `{{ $json.fields.recommendations }}`
     - `status`: `"summarized"` (triggers Component 3)

3. **Error Handling:**
   - Add an Error node to catch Flowise timeouts or malformed responses
   - Log error to Airtable `status` field as `"failed"`
   - Alert team (optional: Slack webhook)

4. Test with a record that has `status: "new"` and verify it updates to `status: "summarized"`

### Step 4: Verify Airtable Field Names Match n8n Expectations

Open your Airtable base and confirm:
- [ ] Table name: "Threat Intelligence"
- [ ] Field: `input_text` (Long Text)
- [ ] Field: `summary` (Long Text)
- [ ] Field: `severity` (Single Select with values: critical, high, medium, low)
- [ ] Field: `attack_type` (Text)
- [ ] Field: `indicators` (Long Text)
- [ ] Field: `recommendations` (Long Text)
- [ ] Field: `status` (Single Select with values: new, summarized, scored, reviewed)

If names differ, update n8n field mappings accordingly.

---

## How to Test It

### Test 1: Unit Test (Flowise Chain Alone)

1. Go to Flowise → Your Chain → **Chat**
2. Paste a sample threat article (300–1000 words) as input
3. Verify JSON output contains:
   - `summary` (string, 2–3 sentences)
   - `severity` (one of: critical, high, medium, low)
   - `attack_type` (string)
   - `indicators` (array of strings)
   - `recommendations` (string)
4. **Expected result:** Valid JSON with all fields populated

**Sample test articles:**
- [CVE-2021-44228 (Log4j)](https://nvd.nist.gov/vuln/detail/CVE-2021-44228)
- [Microsoft Exchange RCE](https://www.cisa.gov/news-events/alerts/2021/03/01/stopping-malicious-hacks-targeting-exchange-servers)

### Test 2: Integration Test (n8n → Flowise → Airtable)

1. Create a test record in Airtable:
   - Title: "Test: Log4j RCE"
   - `input_text`: Paste a 300-word threat article
   - `status`: Set to `"new"`

2. Trigger the n8n workflow manually (or wait for scheduled trigger)

3. Monitor n8n execution logs:
   - [ ] Airtable Read triggers successfully
   - [ ] Flowise HTTP Request returns 200 status
   - [ ] JSON parsing succeeds (no undefined fields)
   - [ ] Airtable Update writes all fields

4. Check Airtable record:
   - [ ] `summary` is populated
   - [ ] `severity` matches vulnerability (should be high/critical for Log4j)
   - [ ] `attack_type` is reasonable (e.g., "Remote Code Execution")
   - [ ] `indicators` contains CVE-2021-44228
   - [ ] `status` changed to `"summarized"`

5. **Expected result:** Record fully updated within 10–30 seconds

### Test 3: Error Handling

1. Create a test record with empty `input_text`
2. Trigger workflow
3. Verify:
   - [ ] n8n logs show error
   - [ ] Airtable record either not updated or marked with `status: "failed"`
   - [ ] Workflow doesn't crash; other records still process

### Test 4: End-to-End (Component 1 → Component 2 → Component 3)

See Component 3 README for full end-to-end validation.

---

## Known Limitations

### 1. **Token Limits**
- Groq's free tier has rate limits (~30 requests/min)
- Articles >10,000 words may timeout or truncate
- **Workaround:** Truncate input_text to 5,000 words in n8n before sending to Flowise

### 2. **IOC Accuracy**
- AI-extracted IOCs may include false positives (e.g., URLs that look like domains but aren't)
- No validation against known threat feeds
- **Recommended:** Component 3 (Relevance Scorer) should validate IOCs against VirusTotal or similar

### 3. **Non-English Content**
- Flowise chain assumes English-language input
- Mixed-language or non-English articles may produce lower-quality summaries
- **Workaround:** Add pre-processing step to detect language and translate if needed

### 4. **Severity Classification**
- AI severity ratings are context-dependent; manual review recommended for critical/high ratings
- No automatic CVE CVSS score lookup
- **Recommended:** Component 3 should cross-reference CVEs with NVD database

### 5. **Response Time**
- Flowise API calls typically take 5–15 seconds
- Large batches (100+ records) may hit rate limits
- **Recommended:** Implement backoff/retry logic in n8n for production

### 6. **Field Truncation**
- Airtable Long Text fields have a soft limit of ~100KB
- Very long recommendations may be truncated
- **Recommended:** Monitor field sizes; alert if approaching limit

---

## Troubleshooting

| Issue | Cause | Solution |
|-------|-------|----------|
| n8n shows "401 Unauthorized" | Groq API key expired or invalid | Regenerate key in Groq console; update Flowise |
| Flowise returns 500 error | LLM chain misconfigured or Groq overloaded | Check Flowise logs; try simpler input; wait and retry |
| `indicators` field is empty array | AI didn't extract IOCs from article | Check if article contains actual threat indicators; may be false negative |
| Record stuck at `status: "new"` | n8n workflow not triggered | Check n8n trigger condition; manually test Airtable read |
| JSON parse error in n8n | Flowise response isn't valid JSON | Add Set node to validate response; log raw output for debugging |

---

## Team Contacts

- **Component 2 Owner:** Harry Yachimba
- **Component 1 (feeds into this):** Jesus Alvarez
- **Component 3 (consumes this output):** Julian Silva-Erazo
- **Integration Lead:** Safayet Safin

---

## Checkpoint 2 Acceptance Criteria

- [ ] One threat article processed end-to-end (text → summary + IOCs + severity)
- [ ] Record status transitions from `"new"` → `"summarized"` automatically
- [ ] All output fields populated correctly in Airtable
- [ ] n8n workflow completes in <30 seconds
- [ ] Error handling prevents workflow crashes
- [ ] Component 3 can successfully read and score the output
