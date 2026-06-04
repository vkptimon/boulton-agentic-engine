# Resume Agent: Project Scope

This document defines the full scope of the Resume Agent project, including the scoring
methodology, deployment strategy, and a realistic week-by-week plan based on
available time (2 hours on weekdays, 3 hours on weekends).

---

## 1. What This Project Is

A web application where a user pastes a job description, and the system:
1. Scores their stored resume against the JD.
2. Highlights gaps and suggests improvements to specific bullet points.
3. Lets the user review, approve, or modify suggestions via a diff interface.
4. Compiles a new, tailored PDF resume.

Target audience: job seekers (initially yourself, then others).

Stack: Python (FastAPI) backend, Vue.js frontend. Path A.

---

## 2. Multi-User Deployment Scope

Since this is intended for public use, the scope expands beyond a personal tool.
The following concerns must be addressed from the start.

### 2.1 Authentication and User Isolation
- Google OAuth 2.0 login (most job seekers have a Google account).
- Each user has their own profile, stored resume data, and history.
- No anonymous usage. Every action is tied to an authenticated user.

### 2.2 Resume Storage
- Users upload their LaTeX resume once via the profile section.
- The raw .tex file content is stored in a PostgreSQL database (not as vector embeddings).
- Parsed sections (Work Experience bullet points, Projects, Skills) are extracted
  and stored as structured JSON alongside the raw file.
- Users can update their master resume at any time; the system re-parses on update.

### 2.3 Rate Limiting and Cost Control
- LLM API calls are expensive. Each "boost" operation costs real money.
- Free tier: 3 boost operations per day per user.
- Paid tier (optional, future): more boosts per day via Stripe integration.
- Backend enforces rate limits per user via a simple counter in the database.

### 2.4 Deployment Architecture

```
[Vercel / Netlify]          [Railway / Render]          [Supabase / Railway PG]
  Vue.js Frontend   --->    FastAPI Backend      --->    PostgreSQL Database
     (Static)                (API + AI Logic)              (User Data)
                                  |
                                  v
                          [LLM Provider API]
                          (Gemini / OpenAI)
```

- Frontend: Static build deployed to Vercel or Netlify (free tier).
- Backend: FastAPI deployed to Railway ($5/month hobby plan) or Render.
- Database: PostgreSQL on Supabase (free tier, 500MB) or Railway's managed PG.
- LaTeX compilation: Tectonic installed in the backend Docker container.
- Estimated monthly cost for low traffic: $5-10/month (excluding LLM API usage).

### 2.5 Infrastructure Decisions

| Concern | Decision | Rationale |
| :--- | :--- | :--- |
| Hosting | Railway (backend) + Vercel (frontend) | Railway auto-detects Dockerfiles; Vercel handles Vue static builds for free. |
| Database | Supabase PostgreSQL (free tier) | 500MB is plenty for resume text data. No need for a paid DB early on. |
| File storage | Database (text column) | LaTeX files are small (5-20KB). No need for S3 or blob storage. |
| LLM provider | Google Gemini API | Generous free tier (15 RPM on Gemini Flash), cost-effective at scale. |
| LaTeX compiler | Tectonic (in Docker) | No system-level TeX installation needed. Single binary, auto-downloads packages. |
| Auth | Google OAuth 2.0 via FastAPI | Simple, widely supported, avoids building password management. |

---

## 3. ATS Score Calculation: Deterministic ML vs. LLM

This is a critical design decision. Here is a grounded comparison.

### 3.1 How Real ATS Systems Score Resumes

Most product-based companies (Google, Amazon, Microsoft, etc.) use enterprise ATS
platforms (Greenhouse, Lever, Workday, Taleo, iCIMS). These systems evaluate
resumes on the following factors:

**Hard Matching Factors (Algorithmic)**
1. Keyword Overlap: Exact match of skills, tools, and technologies mentioned in the JD.
   Example: JD says "Kubernetes", resume must contain "Kubernetes" (not just "container orchestration").
2. Job Title Relevance: How closely your previous titles match the target role.
3. Years of Experience: Extracted from date ranges in work history.
4. Education Requirements: Degree level and field matching.
5. Certification Matching: Specific certs mentioned in the JD (e.g., AWS Solutions Architect).
6. Location/Visa: Geographic and work authorization filters.

**Soft Matching Factors (Increasingly AI-Driven)**
7. Semantic Similarity: Understanding that "built CI/CD pipelines" is relevant to a JD asking for "DevOps experience".
8. Action Verb Strength: Preference for quantified impact statements ("reduced latency by 40%") over vague descriptions ("worked on backend").
9. Section Completeness: Presence of standard sections (Summary, Experience, Education, Skills).
10. Recency Weighting: More recent experience is weighted higher.
11. Keyword Density Without Stuffing: Natural distribution of relevant terms, not crammed into a skills dump.

### 3.2 Recommendation: Hybrid Approach

Neither pure ML nor pure LLM is the right answer alone.

**Layer 1: Deterministic Scoring (Fast, Cheap, Reproducible)**
- Use TF-IDF + Cosine Similarity for raw keyword overlap scoring.
- Use spaCy NER to extract skills, job titles, and organizations from both the resume and JD.
- Use a Sentence Transformer model (e.g., all-MiniLM-L6-v2, runs locally, no API cost)
  for semantic similarity between resume sections and JD requirements.
- This gives you a reproducible numerical score (0-100) that is consistent across runs.
- Cost: Zero. These models run locally on CPU in milliseconds.

**Layer 2: LLM Analysis (Contextual, Expensive, Non-Deterministic)**
- Use the LLM (Gemini Flash) only for the qualitative part:
  - Identify which specific bullet points are weak relative to the JD.
  - Generate rewritten bullet points with stronger alignment.
  - Explain why a particular skill gap matters.
- Do not use the LLM to produce the numerical score. It will give different numbers
  on every run, which destroys user trust.

**Why this matters:**
- If a user runs the same resume against the same JD twice and gets 72% the first time
  and 81% the second time, they will not trust the tool. The deterministic layer
  prevents this.
- The LLM layer adds the intelligence that keyword matching alone cannot provide.

### 3.3 Scoring Breakdown (Proposed Weights)

| Factor | Weight | Method |
| :--- | :--- | :--- |
| Keyword overlap (exact match) | 30% | TF-IDF on extracted skills/terms |
| Semantic similarity (meaning match) | 25% | Sentence Transformer cosine similarity |
| Section completeness | 10% | Rule-based check (has Experience, Education, Skills, Projects) |
| Action verb and quantification quality | 15% | Regex + heuristic scoring for numbers, percentages, metrics |
| Job title relevance | 10% | Sentence Transformer similarity on titles |
| Recency of experience | 10% | Date parsing, higher weight for recent roles |

---

## 4. Agent Design (Simplified)

The original plan had 4 agents. For Path A, we simplify to 3 clear stages,
not necessarily separate "agents" in the LangGraph sense, but distinct processing steps.

### Stage 1: Resume Parser
- Input: Raw .tex file content.
- Output: Structured JSON with sections, bullet points, dates, skills.
- Method: Regex + rule-based LaTeX parsing. No LLM needed.

### Stage 2: Scorer
- Input: Parsed resume JSON + raw JD text.
- Output: Numerical score (0-100) + per-section breakdown + identified gaps.
- Method: Deterministic (TF-IDF + Sentence Transformer + heuristics). No LLM needed.

### Stage 3: Booster (LLM)
- Input: Weak bullet points identified by the Scorer + JD context.
- Output: Suggested rewrites for each weak bullet point.
- Method: LLM (Gemini Flash) with structured output (JSON mode).
- Constraint: Only touches Work Experience, Projects, and Technical Skills sections.

### Final Step: PDF Compiler
- Input: Approved changes merged into the original .tex content.
- Output: Compiled PDF via Tectonic.
- Method: String replacement in .tex file, then shell call to `tectonic`.

---

## 5. Realistic Implementation Plan

Available time per week:
- Weekdays: 5 days x 2 hours = 10 hours
- Weekends: 2 days x 3 hours = 6 hours
- Total: 16 hours per week

Buffer: Plans always slip. Assume 70% efficiency (distractions, debugging, research).
Effective hours per week: ~11 hours.

### Week 1: Project Setup and LaTeX Parser (11 hrs)

| Day | Hours | Task |
| :--- | :--- | :--- |
| Wed | 2 | Initialize repos. Set up FastAPI project with uv/poetry. Set up Vue project with Vite. Establish folder structure. |
| Thu | 2 | Define the resume JSON schema. Write the LaTeX parser that extracts sections and bullet points from a .tex file. |
| Fri | 2 | Write unit tests for the parser against your own resume. Handle edge cases (nested itemize, custom commands). |
| Sat | 3 | Build the FastAPI endpoints: POST /resume/upload, GET /resume/parsed. Store parsed data in a local SQLite DB for now. |
| Sun | 3 | Set up the Vue project scaffold. Build the profile page where a user can paste/upload their LaTeX resume and see the parsed output. |

Deliverable: A working LaTeX parser and a basic UI that shows parsed resume sections.

### Week 2: Deterministic Scoring Engine (11 hrs)

| Day | Hours | Task |
| :--- | :--- | :--- |
| Mon | 2 | Implement TF-IDF + cosine similarity scoring between resume text and JD text. |
| Tue | 2 | Integrate spaCy NER for skill extraction from both resume and JD. |
| Wed | 2 | Add Sentence Transformer (all-MiniLM-L6-v2) for semantic similarity scoring. |
| Thu | 2 | Implement the composite scoring formula (weighted combination of all factors). Write tests. |
| Sat | 3 | Build the FastAPI endpoint: POST /score (accepts JD text, returns score breakdown). Connect to Vue frontend: JD input box and score display with color coding. |

Deliverable: Paste a JD, get a reproducible score with per-factor breakdown.

### Week 3: LLM Booster Agent (11 hrs)

| Day | Hours | Task |
| :--- | :--- | :--- |
| Mon | 2 | Set up Gemini API integration. Write the prompt template for bullet point rewriting. |
| Tue | 2 | Implement structured output parsing (LLM returns JSON with original and suggested bullet points). |
| Wed | 2 | Build the "boost" endpoint: POST /boost. Wire it to the scorer's gap analysis. |
| Thu | 2 | Handle the retry logic (one additional retry as per original requirements). |
| Sat | 3 | Build the diff review UI in Vue: side-by-side cards with green (added), red (removed), blue (edited) highlighting. Add Approve/Reject/Retry buttons. |

Deliverable: Full boost flow working end-to-end locally.

### Week 4: PDF Generation and Polish (11 hrs)

| Day | Hours | Task |
| :--- | :--- | :--- |
| Mon | 2 | Install Tectonic. Write the string-replacement logic that merges approved changes back into the .tex file. |
| Tue | 2 | Implement the PDF compilation endpoint: POST /compile. Handle Tectonic errors gracefully. |
| Wed | 2 | Build the PDF download flow in the frontend. Add the filename convention (master-resume-DD-MM-YYYY.pdf). |
| Thu | 2 | End-to-end testing: upload resume, paste JD, score, boost, approve, download PDF. Fix bugs. |
| Sat | 3 | UI polish: loading states, error messages, responsive layout, color theme. |

Deliverable: Complete local application. Upload, score, boost, compile, download.

### Week 5: Auth, Database, and Multi-User (11 hrs)

| Day | Hours | Task |
| :--- | :--- | :--- |
| Mon | 2 | Set up Google OAuth 2.0 with FastAPI (using authlib or fastapi-users). |
| Tue | 2 | Migrate from SQLite to PostgreSQL. Set up Alembic for migrations. |
| Wed | 2 | Implement user isolation: each user sees only their own resume and history. |
| Thu | 2 | Add rate limiting: 3 boosts per day per user, enforced server-side. |
| Sat | 3 | Add a history page: list of past JDs scored against, with scores and dates. |

Deliverable: Multi-user capable application with auth and rate limiting.

### Week 6: Containerization and Deployment (11 hrs)

| Day | Hours | Task |
| :--- | :--- | :--- |
| Mon | 2 | Write the Dockerfile for the FastAPI backend (including Tectonic installation). |
| Tue | 2 | Write the CI/CD pipeline (GitHub Actions): lint, test, build Docker image. |
| Wed | 2 | Deploy backend to Railway. Set up environment variables (DB URL, API keys). |
| Thu | 2 | Deploy Vue frontend to Vercel. Configure CORS and API base URL. |
| Sat | 3 | End-to-end testing on production. Fix deployment-specific bugs. Set up a custom domain if desired. |

Deliverable: Live, deployed application accessible via a public URL.

### Week 7: Hardening and Launch (11 hrs)

| Day | Hours | Task |
| :--- | :--- | :--- |
| Mon | 2 | Add input validation and sanitization (prevent LaTeX injection, XSS). |
| Tue | 2 | Add error monitoring (Sentry free tier or simple logging). |
| Wed | 2 | Write a minimal landing page explaining what the tool does. |
| Thu | 2 | Load testing: simulate 10 concurrent users, identify bottlenecks. |
| Sat | 3 | Final bug fixes. Write a README. Prepare for sharing. |

Deliverable: Production-ready application.

---

## 6. Total Timeline Summary

| Phase | Weeks | Cumulative Hours |
| :--- | :--- | :--- |
| Core functionality (parser, scorer, booster, PDF) | Weeks 1-4 | ~44 hrs |
| Multi-user and auth | Week 5 | ~55 hrs |
| Deployment | Week 6 | ~66 hrs |
| Hardening and launch | Week 7 | ~77 hrs |

Realistic delivery: 7 weeks from start to a deployed, multi-user application.

This assumes no major scope changes. If you add features like PDF upload parsing
(instead of LaTeX-only), multiple resume templates, or a paid tier with Stripe,
add 1-2 weeks per feature.

---

## 7. Risks and Mitigations

| Risk | Impact | Mitigation |
| :--- | :--- | :--- |
| LaTeX parser breaks on unusual resume formats | Users cannot onboard | Provide a reference LaTeX template. Validate on upload. |
| LLM generates invalid LaTeX special characters | PDF compilation fails | Escape all LLM output before injecting into .tex. Validate with a dry-run compile. |
| Gemini API rate limits or cost spikes | Service degrades under load | Enforce per-user rate limits. Cache identical JD+resume pairs. |
| Tectonic compilation is slow in Docker | Poor user experience | Run compilation asynchronously. Show a progress indicator. Set a 60-second timeout. |
| Sentence Transformer model is too large for Railway | Deployment fails | Use the smallest model (all-MiniLM-L6-v2, ~80MB). Or fall back to TF-IDF only. |

---

## 8. Out of Scope (For Now)

The following are explicitly deferred to avoid scope creep:
- PDF upload and OCR parsing (LaTeX-only for v1).
- Multiple resume templates or theme customization.
- Stripe payment integration.
- Browser extension for auto-filling job applications.
- Mobile-responsive design beyond basic usability.
- Admin dashboard or analytics.
