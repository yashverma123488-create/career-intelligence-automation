# Setup Guide

This guide connects the workflow templates to your own accounts. The files in `workflows/` contain placeholders in place of real IDs and credentials.

## Prerequisites

- An n8n instance (cloud or self-hosted)
- A Google account for Forms, Sheets, and Drive
- A Gmail account for sending email
- A [PDFShift](https://pdfshift.io) account and API key
- A Google Gemini API key

## 1. Prepare Google Sheets

Create these spreadsheets (one tab each is enough, named as shown):

| Purpose | Tab name | Suggested columns |
|---------|----------|-------------------|
| Form responses (created automatically by your form) | Form responses 1 | Form questions |
| Existing students (duplicate check) | `EXISTING STUDENTS` | Email, Name, plus the tracking columns the workflow writes |
| Master: student data | `Sheet1` | Timestamp, Name, Email, Location, Current Status, Experience Level, Target Role, Target Industry |
| Master: assessments | `Sheet1` | Timestamp, Name, Email, Readiness/Alignment/Fit scores and categories |
| Master: career plans | `Sheet1` | Timestamp, Name, Email, Roadmap Duration, Primary Career, Final Direction |
| Master: reports | `Sheet1` | Timestamp, Name, Email, Report Status, Readiness Score |

The workflow appends rows by matching column headers, so create the header row first.

## 2. Create credentials in n8n

| Credential type | Used by |
|-----------------|---------|
| Google Sheets OAuth2 | Reading and writing sheets, creating the tracker |
| Google Sheets Trigger OAuth2 | Form response trigger |
| Google Drive OAuth2 | Sharing the tracker with the student |
| Gmail OAuth2 | Student email and error alerts |
| Google Gemini (PaLM) API | Career Intelligence Engine |
| HTTP custom auth (PDFShift) | Report and resume PDF conversion |

For PDFShift, set the header `X-API-Key` to your key in a custom auth credential.

## 3. Import the workflows

1. In n8n, choose **Workflows → Import from file**.
2. Import `error-handler.json` first and note its workflow ID (visible in the URL).
3. Import `career-intelligence-automation-v1.json`.

## 4. Replace the placeholders

Search each workflow for `YOUR_` and `REPLACE_WITH` and fill in your own values.

| Placeholder | Replace with |
|-------------|--------------|
| `YOUR_FORM_RESPONSES_SHEET_ID` | ID of your form responses spreadsheet |
| `YOUR_FORM_RESPONSES_TAB_GID` | Tab (gid) of the responses sheet |
| `YOUR_EXISTING_STUDENTS_SHEET_ID` | ID of the duplicate-check spreadsheet |
| `YOUR_MASTER_*_SHEET_ID` | IDs of the four master spreadsheets |
| `REPLACE_WITH_YOUR_CREDENTIAL_ID` | Select your credential in each node's dropdown |
| `YOUR_ERROR_HANDLER_WORKFLOW_ID` | Workflow ID of the imported error handler |
| `your-alerts-email@example.com` | The address that should receive error alerts |
| `https://example.com/your-logo.png` | URL of your logo for the report header |

The sheet ID is the long string in a spreadsheet's URL between `/d/` and `/edit`. In the nodes, it is easiest to re-pick each document from the dropdown.

## 5. Check the AI node

Open **Gemini 3 Flash Preview** and confirm the model name is available on your account. Choose a different Gemini model if it is not. Keep the temperature low (the template uses 0.15) so the JSON output stays consistent.

## 6. Test

1. Keep the workflow inactive and submit one test response through your form, using an email address you control.
2. Click **Execute workflow** and watch each node.
3. Confirm: master sheets get rows, the tracker is created and shared, two PDFs are generated, and the email arrives.
4. Activate the workflow.

## Troubleshooting

| Symptom | Likely cause |
|---------|--------------|
| Run stops after "Duplicate Check" | Email already listed in `EXISTING STUDENTS`, or no email was found in the row |
| Run ends at "Invalid Intelligence Output" | The model returned malformed JSON; retry, lower the temperature, or check the token limit |
| PDF step fails | PDFShift key missing or out of credits |
| Rows land in the wrong columns | Header names in the master sheets do not match what the workflow sends |
| No error alert received | The error workflow is not set in the main workflow's settings |
