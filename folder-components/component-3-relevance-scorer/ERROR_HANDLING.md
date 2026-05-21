# Component 3: Error Handling Implementation

## Overview
Component 3 (Relevance Scorer) now includes comprehensive error handling to gracefully manage failures instead of silently dropping records. This document outlines the implementation and how to use it.

## Error Handling Scenarios

### 1. Groq API Failures
**What happens:** When the Groq API call fails (timeout, API error, invalid response):
- The workflow catches the error via the "Handle Groq API Error" node
- The record is updated in Airtable with:
  - `Current Status`: **"Error"**
  - `Error Reason`: API error message (e.g., "API timeout", "Connection refused")
- The record remains in Airtable but is marked as failed for retry

**Why this matters:** Instead of silently failing, you can now see which records failed and why, enabling retry logic and debugging.

### 2. Airtable Update Failures
**What happens:** When updating the Airtable record fails (permission error, network failure, field mismatch):
- The workflow catches the error via the "Handle Airtable Update Error" node
- The error is logged with the record ID and error details
- Status is set to **"Error"** with reason

**Why this matters:** You can track update failures and distinguish them from API failures.

### 3. Invalid JSON Parsing
**What happens:** When Groq returns unparseable JSON:
- The "Code in JavaScript" node catches the error
- Falls back to default values (score=0, level="Low")
- Records are still marked as "Scored" with the parse error in the reason field
- These records are retryable since they show what went wrong

## Workflow Changes

### New Nodes Added
1. **Handle Groq API Error** - Catches Groq API failures and writes error records
2. **Handle Airtable Update Error** - Catches Airtable update failures and logs them

### Modified Nodes
1. **Groq Relevance Scoring** - Changed `onError` from "continueRegularOutput" to "stopWorkflow"
   - Now routes errors to "Handle Groq API Error" via error connection
   - Timeout set to 30 seconds for reliability

2. **Update record** - Changed `onError` from "continueRegularOutput" to "stopWorkflow"
   - Now routes errors to "Handle Airtable Update Error" via error connection

### New Airtable Fields
- **Error Reason** (text field) - Contains the error message when processing fails
- **Current Status** - Now includes "Error" as an option (alongside "New", "Enriched", "Scored")

## Using Error Monitoring in Airtable

### Create an Error Monitoring View
1. **In Airtable**, go to the "Threats" table
2. **Create a new View** > Select "Grid"
3. **Name it**: "ERROR MONITORING"
4. **Add Filter**: 
   - Field: `Current Status`
   - Condition: `is`
   - Value: `Error`
5. **Add Sorting** (optional):
   - Field: `Scored At`
   - Order: Descending (newest first)
6. **Save the view**

### What You'll See
- All records with `Current Status = "error"`
- Their `Error Reason` explaining what went wrong
- Timestamp of when the error occurred
- Original threat data for context

### Responding to Errors

**For API Timeout Errors:**
- Check if Groq API is experiencing issues
- Retry the record (you can manually trigger the workflow on that record)
- Consider implementing automatic retries with exponential backoff

**For Airtable Update Errors:**
- Check permissions on the token used
- Verify field names and data types match the schema
- Check for rate limiting

**For Parse Errors:**
- These are generally recoverable
- The record shows the raw output that failed to parse
- Might need to adjust the Groq prompt to enforce stricter JSON formatting

## Conditional Checks and If Statements
The workflow uses simple conditional checks to keep processing predictable and recoverable.
- `if Enriched=true and Scored=false`: only process records that still need scoring.
- `if Groq response contains valid JSON score`: update record normally.
- `if Groq response is invalid or missing fields`: mark the record as `Error` and store the reason.
- `if Airtable update fails`: capture the failure, stop the workflow, and preserve the record status.

How these checks helped:
- Prevented records from being scored multiple times.
- Kept workflow failures isolated so valid records still complete.
- Made errors visible in Airtable rather than losing or silently dropping records.
- Allowed direct retry of failed records after fixing the root cause.

## Testing Error Handling

### Test Groq API Error:
1. **Temporarily break the Groq API credential** (use invalid API key in the workflow config)
2. **Run the workflow** on a test record
3. **Verify** the record appears in your ERROR MONITORING view with error reason

### Test Airtable Update Error:
1. **Temporarily use invalid Airtable base ID** in the Update record node
2. **Run the workflow**
3. **Verify** error is caught and logged

### Test Recovery:
1. **Fix the issue** (restore valid credentials)
2. **Manually re-trigger** the workflow on an error record
3. **Verify** it now processes successfully

## Retrying Failed Records

To retry failed records:
1. **Filter by error status** in your ERROR MONITORING view
2. **Fix the underlying issue** (API, network, schema, etc.)
3. **Manually execute** the Component 3 workflow and it will process all records where `Enriched=1` and `Scored=0`
   - Failed records with `Current Status="error"` need to be reset to `Scored=false` first
4. **Or use n8n's retry feature** for the workflow execution

## Data Retention
- Error records remain in Airtable indefinitely
- The `Error Reason` field documents what went wrong
- Original threat data is preserved for investigation
- This creates an audit trail of all processing attempts

## Future Enhancements
- Implement automatic retry logic for transient failures
- Add exponential backoff for API timeouts  
- Create automated alerts when error rate exceeds threshold
- Add retry counters to track retry attempts per record
