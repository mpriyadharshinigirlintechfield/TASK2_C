# AI Resume Intelligence – Document Analyzer

AI-powered resume analysis, structured information extraction, adaptive summaries, and completeness insights.

**Candidate:** M. PRIYADHARSHINI · 921023104029 · CSE, Nadar Saraswathi College of Engineering and Technology · **Task 2 – AI-Powered Document Intelligence Platform**

## Project Overview
Upload a resume (PDF or TXT, or paste text). The server extracts the text, sends it to Google Gemini, validates the returned JSON, and shows a results dashboard with a summary, structured fields, a skills chart, a completeness score, and missing/uncertain information. Resumes are the single supported document type.

## Features
- PDF / TXT upload (plus paste-text fallback), filename, size and status display
- Basic / Standard / Detailed analysis levels that change the AI prompt and output
- AI summary, personal info, skills badges, education, work experience, key findings
- Missing and uncertain information lists
- Skills bar chart (CSS bars)
- Completeness score computed by code with a progress bar
- Loading and friendly error states; responsive UI

## Technology Stack
Next.js 15 (App Router) · React 19 · TypeScript · Tailwind CSS · Gemini API (REST, server-side) · pdf-parse · CSS bars for visualization (no chart library needed) · Vercel

## AI Implementation
- `app/api/analyze/route.ts` receives the file, extracts text (`lib/pdf.ts`) and calls `lib/gemini.ts`.
- Gemini is called server-side only; `GEMINI_API_KEY` never reaches the browser.
- The system prompt: analyze only the given text, never invent data, use `null` when unavailable, return only JSON.
- `responseMimeType: application/json` and low temperature (0.1) are used for reliable structured output.
- The response is parsed safely and normalized (`lib/validation.ts`): missing fields become `null` / `[]`, and malformed JSON returns a friendly error.
- Missing info: the model lists absent fields in `missing_information` and ambiguous items in `uncertain_information`.

## Adaptive Analysis
- **Basic:** 2-sentence summary, essential fields, max 2 findings.
- **Standard:** 3-4 sentence summary, all fields with short descriptions, 3-5 findings.
- **Detailed:** 5-7 sentence summary, full detail, 6-10 findings (strengths, projects, gaps), thorough missing/uncertain lists.

## Validation
Completeness = (present fields / 6) × 100, checking Name, Email, Phone, Skills, Education, Work Experience. Calculated in `computeCompleteness()` — not by the AI. Skills chart bars show how often each extracted skill appears in the resume text (derived, not an AI proficiency rating).

## Setup
```bash
npm install
cp .env.example .env.local     # Windows: copy .env.example .env.local
# edit .env.local and set GEMINI_API_KEY (free key: https://aistudio.google.com/app/apikey)
npm run dev
```
Open http://localhost:3000. Build check: `npm run build`.

## Deployment (Vercel)
1. Push the project to GitHub (do not commit `.env`).
2. Go to vercel.com → Add New → Project → import the repo.
3. In Environment Variables add `GEMINI_API_KEY`.
4. Click Deploy, then paste the live URL into `candidate_details.txt`.

## Testing
Run the app, upload `public/sample-resume.txt` (fictional candidate), try each analysis level, and compare the output. Also try an unsupported file or an empty file to see the error handling.

## Known Limitations
- Resume-focused implementation only
- AI extraction can occasionally need human verification
- Scanned/image PDFs need OCR (not included)
- Skills visualization is based on extracted skills and their mentions in the text
- Free-tier Gemini rate limits may apply
