# Career Intelligence Automation

An end-to-end **n8n** workflow that turns a student's Google Form submission into a personalized career package: an AI-generated **Career Intelligence Report (PDF)**, an **ATS-ready Resume (PDF)**, and a personal **Google Sheets Career Tracker**, all delivered by email automatically.

Built for a career-guidance organization that assesses students and early-career professionals (undergraduates, postgraduates, recent graduates, and working professionals seeking a change).

> **Note:** This repository contains workflow templates only. All credentials, spreadsheet IDs, email addresses, and student data have been removed. See [docs/setup-guide.md](docs/setup-guide.md) to connect your own accounts.

---

## What it does

1. **Collects** a detailed profile through a Google Form (education, skills, experience, goals).
2. **Checks for duplicates** so the same student is not processed twice.
3. **Chooses a roadmap length** (90 days, 1 year, or 2 years) from the student's program and year.
4. **Analyzes the profile with Google Gemini** and produces one structured JSON package: readiness score, career alignment, gap analysis, target companies, compensation range, a 90-day action plan, and a resume.
5. **Validates** the AI output before using it.
6. **Saves records** to four master Google Sheets (student data, assessments, career plans, reports).
7. **Creates a personal tracker** (Dashboard, 90-Day Action Plan, Skill Tracker, Milestones) and shares it with the student.
8. **Generates two PDFs** (report and resume) through PDFShift.
9. **Runs a quality gate**, then **emails** the student the PDFs and tracker link.
10. **Logs** the student as processed. A separate error-handler workflow alerts the admin if anything fails.

## Workflow overview

```
Google Form → Google Sheet (new row)
        │
        ▼
 Normalize submission → Duplicate check ──(duplicate / no email)──► Stop
        │ new student
        ▼
 Roadmap duration → Gemini analysis → Validate JSON ──(invalid)──► Stop
        │ valid
        ├──► Save to 4 master sheets
        └──► Create tracker sheet → share with student
                    │
                    ▼
        Build report HTML + resume HTML → PDFShift → PDFs
                    │
                    ▼
        Quality gate ──(fail)──► Not sent
                    │ pass
                    ▼
        Email student → Record in "existing students" sheet
```

## Repository contents

| Path | Purpose |
|------|---------|
| `workflows/career-intelligence-automation-v1.json` | Main n8n workflow (46 nodes) |
| `workflows/error-handler.json` | Error workflow that emails an alert when the main one fails |
| `docs/questionnaire-overview.md` | What the student questionnaire collects |
| `docs/setup-guide.md` | Step-by-step setup and import instructions |
| `samples/sample-report.md` | A fictional example of the output |

## Tech stack

- [n8n](https://n8n.io) (workflow automation)
- Google Forms, Sheets, and Drive
- Google Gemini (via n8n's LangChain / Gemini chat model node)
- [PDFShift](https://pdfshift.io) (HTML to PDF)
- Gmail (delivery and alerts)

## How the roadmap length is chosen

| Profile | Roadmap |
|---------|---------|
| Experienced working professional | 90 days |
| BBA, 3rd or final year | 1 year |
| BBA, 1st or 2nd year | 2 years |
| MBA, 2nd or final year | 90 days |
| MBA, 1st year | 2 years |
| Any other program (for example PGDM, B.Com) | 90 days (default) |

The rules live in the **Roadmap Duration** node and match keywords in the form answers.

## Quick start

1. Import both JSON files into n8n (**Workflows → Import from file**).
2. Create your own credentials (Gmail, Google Sheets, Google Drive, PDFShift, Gemini).
3. Replace every `YOUR_...` placeholder with your own sheet IDs and email.
4. Set the main workflow's **Error Workflow** to the imported error handler.
5. Test with one sample form submission, then activate.

Full instructions: [docs/setup-guide.md](docs/setup-guide.md).

## Known limitations

- Report quality depends on the detail students provide in the form.
- Market and salary figures are model estimates, labelled as indicative and not live-verified.
- The roadmap rules look for specific keywords (BBA, MBA, year words). Changing question wording can change the result.
- If the AI output is invalid or a student is a duplicate, the run ends quietly without an email. It does not trigger the error handler, so consider adding an alert on those branches.
- The form trigger polls every minute, which can use many executions on cloud plans.

## Privacy

The workflow handles personal data (names, emails, phone numbers, education, and career details). If you deploy it, restrict access to the response and master sheets, tell students how their data is used, and follow the data-protection rules that apply to you.

## Disclaimer

Outputs are directional guidance based on the information provided. They are not a guarantee of employment, eligibility, or salary.

## License

Released under the [MIT License](LICENSE).
