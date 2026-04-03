<img width="1693" height="501" alt="image" src="https://github.com/user-attachments/assets/303253b3-ee42-4412-8a23-9cebf52bd714" />
# Candidate Intake & Review Automation (n8n)

This project is an automated candidate intake workflow built in n8n. It processes webhook submissions, stores candidate data, sends internal notifications, handles failures with retries, logs executions, and triggers external webhook events.

---

## 🚀 Features

- Webhook-based candidate intake
- Deduplication using email (no duplicate rows)
- Google Sheets integration for tracking candidates
- Status machine:
  - `NEW`
  - `NEEDS_REVIEW`
  - `EMAIL_FAILED`
- Internal email notification (clean summary + resume link only)
- Retry logic (3 attempts on failure)
- Failure handling with status updates
- Run logging for every execution
- External webhook POST after success
- HTTP response with `run_id` and `final_status`

---

## Workflow Overview

### Success Flow

1. Webhook receives candidate submission
2. Candidate is inserted/updated in Google Sheets (`NEW`)
3. Internal notification email is sent
4. Status updated to `NEEDS_REVIEW`
5. Run log entry created
6. External webhook POST triggered
7. HTTP response returned

---

### Failure Flow (Email fails)

1. Webhook receives candidate submission
2. Candidate is inserted/updated (`NEW`)
3. Email fails → retried 3 times
4. Still fails → status set to `EMAIL_FAILED`
5. Run log entry created
6. HTTP response returned

---

## Webhook Input Example
```json
{
  "name": "Cayden Marty",
  "email": "cayden@example.com",
  "role_type": "Automation Intern",
  "resume_link": "https://example.com/resume",
  "notes": "Test submission"
}
```

---

## Outbound Webhook Payload
```json
{
  "run_id": "12345",
  "candidate_email": "cayden@example.com",
  "role_type": "Automation Intern",
  "final_status": "NEEDS_REVIEW",
  "timestamp": "2026-04-03T12:00:00Z"
}
```

---

## Google Sheets Structure

| Name | Email | Role Type | Resume Link | Notes | Status | Run ID |
|------|-------|-----------|-------------|-------|--------|--------|

---

## Run Log Sheet

| Timestamp | Candidate Email | Actions | Result | Run ID |
|-----------|-----------------|---------|--------|--------|

---

## Tech Stack

- n8n (Docker)
- Google Sheets API
- Gmail API
- Webhooks (Webhook.site)

---

## Key Design Decisions

- Deduplication based on email to ensure rerunnable workflow
- Status only moves to `NEEDS_REVIEW` after successful notification
- Retry logic prevents transient failures from breaking flow
- Logs ensure traceability of every execution
- External webhook only fires after confirmed success

---

## Setup Notes

1. Enable Google Sheets API + Google Drive API
2. Connect Gmail credentials in n8n
3. Configure webhook node to use **Respond to Webhook**
4. Export/import workflow JSON to reuse

---

## Files

> _Add workflow JSON and any supporting files here._

---

## Author

**Cayden Marty**
