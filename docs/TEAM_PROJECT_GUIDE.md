# Team Project Guide — Building an App with se-agent 


You are building a real (small) app as a team, the way Sommerville describes software being built:
understand the process, agree requirements, model the system, choose an architecture, implement,
test, and plan for change. se-agent is your **assistant**, not your team member. It drafts; you decide.

**The rule that matters most:** the AI writes first drafts. Every artifact it produces must be
read, corrected, and *owned* by a named human on your team. "The AI said so" is never a justification
in a design review.

---

## 1. How the project runs

| Part | What it is |
|---|---|
| Team | 4–5 students, roles below |
| Product | A small app, grown from the water-tracker brief or a team-chosen idea approved by the instructor |
| Method | Agile, in short sprints (Sommerville Ch 3), with plan-driven *documents* where they earn their keep |
| AI | Your team's own structured prompts in `prompts/`, run through se-agent |
| Hub | Your team's GitHub fork: code, docs, issues, and the sprint board |

### Roles (rotate the "Scrum" role each sprint)
- **Product Owner** — owns the brief and requirements; decides priority.
- **Process lead / Scrum master** — runs stand-ups, keeps the board honest, runs retrospectives.
- **Architect** — owns the architecture description and its decisions log.
- **Quality lead** — owns the test plan, runs the tests, owns the definition of done.
- **Prompt engineer** — owns `prompts/`: maintains the 8-section prompts, reviews every run.

Everyone writes code and everyone reviews. Roles mean *accountable*, not *sole worker*.

---

## 2. The phases (Sommerville mapping)

Each phase has: **purpose**, a **prompt** your team writes, **human work**, and **a deliverable** committed to `docs/`.
The workbook (`TEAM_WORKBOOK.md`) is the fill-in version.

### Phase 0 — Team & process (Ch 2, Ch 3)
- Choose your process: say *why* (plan-driven, incremental, agile; Ch 2) and which agile practices you use (stories, sprints, stand-ups, retrospectives; Ch 3).
- Create the repo board: columns *Backlog / Sprint / Doing / Review / Done*.
- Write your **Definition of Done** (e.g., reviewed by one other person, tests pass, docs updated).
- **Deliverable:** `docs/00_team_charter.md`.

### Phase 1 — Requirements (Ch 4)
- Interview the “customer” (the instructor / your Product Owner). Write **user stories** with acceptance criteria, and separate **functional** from **non-functional** requirements (performance, usability, security, cost).
- Write the **project brief** (`docs/BRIEF_TEMPLATE.md`) — this is the *input to se-agent*. Keep it under ~4000 characters; the router warns beyond that.
- Prompt idea: a requirements-engineer prompt that turns the brief into numbered, testable requirements and flags ambiguity.
- **Human check:** is every requirement testable? Could two people read it the same way? Remove anything unneeded (YAGNI).
- **Deliverable:** `docs/01_requirements.md`.

### Phase 2 — System modeling (Ch 5)
- Context model (what is inside/outside the system), use cases or interaction (sequence) models, a structural (class) model, a behavioral (state) model for anything with modes.
- Text diagrams are fine (Mermaid is rendered by GitHub).
- **Human check:** do the models agree with the requirements and with each other?
- **Deliverable:** `docs/02_models.md`.

### Phase 3 — Architecture (Ch 6)
- Choose a pattern deliberately (layered, MVC, client-server, pipe-and-filter…) and write down *why*, tied to the non-functional requirements.
- Keep a **decisions log**: decision, alternatives, reason, consequences.
- If your app calls an AI model or uses Hugging Face (see §6), the architecture must show where, what happens when it is slow, wrong, or unavailable, and what it costs per use.
- **Deliverable:** `docs/03_architecture.md`.

### Phase 4 — Design & implementation (Ch 7)
- Design patterns where they help (Observer, Strategy, Facade…), reuse before writing (libraries, open source; mind licenses), configuration management (branches, pull requests, code review).
- Separate logic from display so logic can be unit-tested.
- Use se-agent's generated code as a **starting draft**; the team integrates, simplifies, and fixes it.
- **Deliverable:** working code in the repo, merged through reviewed pull requests.

### Phase 5 — Testing (Ch 8)
- Development testing (unit, component, system), test-driven where practical, then release testing against the requirements and user testing with someone outside the team.
- Map **each requirement to at least one test**; keep that map in `docs/05_test_plan.md`.
- An AI-written test that passes is not proof; check that a test fails when the code is broken (try it).
- **Deliverable:** tests that run with one command, plus the test plan.

### Phase 6 — Evolution & delivery (Ch 9)
- Plan for change: what is most likely to change, and how hard would it be? Write a **change request** for one realistic change and carry it out.
- Retrospective: what did the process, the AI, and the team do well or badly?
- **Deliverable:** `docs/06_evolution_and_retro.md`, a demo, and a short run-it-yourself README for your app.

---

## 3. Sprint rhythm (adapt to the weeks you have)

| When | Do |
|---|---|
| Sprint start | Pick stories from the backlog; estimate; assign owners |
| Daily / each class | 3-line stand-up: done / next / blocked |
| Mid-sprint | Request an AI run if needed (see §4) |
| Sprint end | Demo working software; retrospective (one improvement for next sprint) |

Suggested pacing across the remaining weeks: **Sprint 1** — charter, brief, requirements, first prompts;
**Sprint 2** — models, architecture, first working slice; **Sprint 3** — features, tests, evolution change;
**Final** — polish, demo, retrospective. Your instructor sets the real dates.

---

## 4. Using se-agent (and the cost rules)

**Who runs it:** the instructor's account pays for runs. Teams do **not** put their own keys in the repo.

**Requesting a run:** open an Issue using the *Run request* template. State: which brief file/branch,
which prompts, what you expect to get, and why a run is worth it *now*. The instructor runs it and
posts the output folder (or the log) back to the Issue.

**Before you request:**
1. Run `uv run pytest` in your fork — everything must pass.
2. Run `uv run python cost_estimate.py` and read the Light/Typical/Heavy figures.
3. Re-read your brief. A vague brief produces a vague (and still expensive) run.
4. Make sure each prompt follows the 8-section template and is committed.

**Budget rules**
- One run per team per sprint by default; ask if you need more.
- Cheap model (`gpt-6-luna`) for iteration; a stronger model only for a planned demo run.
- The router has a **spending guard** (default $2.00 / 1,000,000 tokens per run). A run that hits it stops and saves what it finished.
- Never paste your own key into any file in the repo; `.env` is git-ignored — keep it that way.

**After a run:** the Prompt engineer reviews `output/<timestamp>/` — the step files, `run.log`, `usage.json`, and the generated `app/` files — and files Issues for everything that is wrong. Then the team decides what to adopt.

---

## 5. AI use: what is expected

- **Allowed:** drafting requirements, models, tests, and code from your own prompts, then improving them.
- **Required:** record in each deliverable which parts were AI-drafted and what your team changed or rejected (a short “AI contribution” note).
- **Not allowed:** submitting unreviewed output; hiding AI use; putting secrets in prompts or briefs.
- **Evaluated:** quality of your prompts, quality of your *review*, and whether you can explain and defend every design decision without the AI.

---

## 6. Optional: AI features inside your app (Hugging Face)

Your app may use models for text, images, or audio. This is a *design* topic as much as a coding one:
latency, cost, failure handling, privacy, and testing non-deterministic output. Ideas that fit a small app:

| Capability | Example feature | Notes |
|---|---|---|
| Text classification / sentiment / summarization (transformers `pipeline`) | Auto-tag entries; summarize a week | CPU-feasible with small models |
| Speech-to-text (e.g. Whisper-family) | “Say what you drank” | Needs audio permissions; test on noisy input |
| Text-to-speech | Read the daily summary aloud | Accessibility angle |
| Image generation (diffusion) | Illustrate a streak badge | Needs a GPU or a hosted API; slow and costly |
| Embeddings + search | Find similar past entries | Small, cheap, and testable |

Rules: get instructor approval first; every AI feature needs a non-AI fallback; document the model name, license, and what data leaves the user's device.

---

## 7. Grading outline (instructor adjusts)
Process & collaboration (repo history, board, reviews) · Requirements & models · Architecture & decisions log ·
Working software & tests · Prompts and review of AI output · Evolution change & retrospective · Demo.
