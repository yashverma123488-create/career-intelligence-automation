# Questionnaire Overview

**Form title:** Career Readiness Assessment: Student Profile Form
**Time to complete:** about 10-15 minutes
**Purpose:** collect the details needed to generate a personalized Career Readiness Report with a readiness score, career alignment, skill gaps, and a customized 90-day action plan.

The form is a Google Form whose responses land in a Google Sheet. The workflow triggers on every new row.

## Sections

| Section | What it asks |
|---------|--------------|
| Personal details | Email, full name, phone, location, age, LinkedIn URL, gender |
| Current status | Currently enrolled (UG or PG), recently graduated (UG or PG, within 1 year), or working professional seeking a change or upskill |
| School education | 10th board, year, and marks; 12th stream, board, year, and result |
| Undergraduate | Institution, degree and specialization, joining and completion years, CGPA or percentage, core subjects |
| Postgraduate (optional) | University or business school, degree and specialization, years, CGPA or percentage |
| Career goals | Target job role, up to three industries, preferred company types, work location and mode, expected salary, role type, and motivation |
| Skills | Primary domain expertise, technical skills with proficiency levels, soft skills, languages |
| Experience | Internships, work experience, and projects, each with details when the answer is "Yes" |
| Credentials | Certifications or courses, achievements, awards, and leadership roles |
| Context | Career blockers, weekly time available for upskilling, and any additional goals or constraints |

## How answers are used

- **Email and name** identify the student and prevent duplicate processing.
- **Program and year** decide the roadmap length (see the README).
- **The full set of answers** is sent to the AI model, which returns one structured JSON package.
- **Experience, projects, education, and certifications** feed the resume, which may only use facts the student provided.
- **Career blockers and available time** shape the action plan.

## Notes for maintainers

- The form has two email fields: Google's automatic email collection and a typed "Email Address" question. The workflow reads the first column whose header contains "email".
- Changing question wording can affect keyword matching in the **Roadmap Duration** node.
- Questions about a PhD pathway were removed from the current version of the form.
- The form collects personal data (phone number, age, gender, LinkedIn). Keep the responses sheet private.
