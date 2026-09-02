---
name: resume-tailor
description: |
  Tailor the user's resume to a specific job by researching the company and
  the role, then surfacing the parts of their REAL background that make them
  uniquely qualified — in their own voice, not buzzword-matched.
  Use when: "tailor my resume", "tailor for <company>", "apply to <company>",
  "resume for this JD", or any request to adapt the resume to a job posting.
  Invoke with /job-hunt:resume-tailor.
argument-hint: "<company> [JD text or URL]"
user-invocable: true
---

# Resume Tailor

Adapt the user's resume to one job. The win condition is **"this person is
uniquely qualified for THIS role,"** not "this resume contains the keywords."
Research first, then edit the candidate's real history into the sharpest
truthful version of itself — keeping their voice intact.

## Workspace (read before anything else)

1. Read `~/.claude/job-hunt/config.md`. It records `workspace: <path>` and
   `master_resume: <path>` (the master resume source file). If the config is
   missing, STOP and tell the user to run `/job-hunt:init`.
2. Read `<workspace>/profile.md` — identity, constraints, the career-facts
   inventory (the ONLY source of claims), and any locked-section notes.
3. Read `<workspace>/voice.md` — register, signature moves, approved lines,
   banned words and patterns, formatting rules.
4. Read `<workspace>/templates/README.md` — the compile and verify commands
   for the user's resume toolchain, and any output-naming overrides.

## Files (single source of truth)

Resume files live in the workspace (a private git repo). The master lives at
the path in `config.md`; everything company-specific lives in
`applications/<company>/` (lowercase folder; create it for a new company).
Never write resume files directly to the workspace root or outside the
workspace.

| File | Role |
|------|------|
| master resume (path from `config.md`) | Master content. **Never edit for a tailoring job.** |
| resume layout/template (in `templates/`) | Layout. Don't touch unless layout breaks. |
| `applications/<company>/resume.<Company>.<ext>` | The per-job copy you edit. |
| `applications/<company>/<name>_<Company>.pdf` | The output (name per templates/README). |

Follow `templates/README.md` for fonts and toolchain. If a compile warns
about missing fonts or tools, stop and tell the user before proceeding — do
not silently substitute.

## Inputs

- **Company** (required): name of the company posting the role.
- **Job description** (required): pasted text, or a URL to fetch. If only a
  company + role title is given, ask for the JD — do not invent requirements.

## Workflow

### 1. Gather the posting
- If given a URL, fetch the JD. If pasted, use it verbatim.
- Extract: role title, team, seniority, must-haves vs nice-to-haves, the
  actual problems the role exists to solve, and any signals about how they
  work (on-call, ownership, ambiguity, scale, regulated domain, etc.).

### 2. Research the company (this is required, not optional)
Use web search/fetch. Aim for signal, not a profile dump. Look for:
- **What they actually build** and who their users are.
- **Engineering culture & values** — eng blog, "how we work," public talks,
  founders' writing. What do they reward? (ownership? velocity? rigor?)
- **Hiring practices** — interview style (take-homes, system design, values
  interviews), what they say they screen for, public job-ladder or values
  pages, employee-review signals.
- **Stage & context** — funding, size, recent launches, current pressures
  (scaling, compliance, a pivot). These tell you what pain the hire relieves.
Capture 4–8 concrete findings with sources. Discard generic filler.

### 3. Inventory the real background
Read the master resume (path from `config.md`) end to end AND read the
career-facts inventory in `profile.md`. Together these list the genuine,
specific things the user has done. These are the only raw materials. Nothing
gets invented. If the master and the inventory disagree, ask the user which
is correct — do not pick one silently.

### 4. Find the unique-fit thesis
Before editing, write one or two sentences: **why is the user specifically
right for this role?** Tie a real thing they've done (from step 3) to a real
thing the role needs (from steps 1–2). Examples of the *shape* (derive the
actual one from research and the inventory):
- regulated/compliance-heavy role → point to lived compliance work in the
  inventory, not a checkbox term.
- small team / high ownership → point to end-to-end ownership the user
  actually held.
- platform/internal-tooling role → point to internal tools the user shipped
  and the teams that adopted them.
If no honest thesis exists, say so plainly — a weak-fit role is worth telling
the user about, not papering over.

### 5. Propose bullet-level diffs — DO NOT edit files yet
Do NOT write `resume.<company>.<ext>` in this step. Do NOT touch any locked
section: sections `profile.md` marks as locked stay verbatim, and the summary
is locked by default (it is the user's voice and stays verbatim in every
tailored variant unless `profile.md` explicitly says otherwise).

Instead, present a **diff proposal** for the user to review before anything
gets written. For each proposed change to the experience section, show:

- **File location** (which job + which bullet index, or "reorder").
- **Before** — the exact current bullet text from the master resume.
- **After** — the proposed rewrite, or `<drop>` / `<move to position N>`.
- **Why** — one sentence tying the change to a JD requirement or research
  finding (not a keyword).

Group proposals by job, in the order they appear in the master. Include
reorder proposals (bullet-order-within-job, or job-order if breaking
reverse-chron — flag that explicitly).

Allowed diff types:
- **Rewrite** a bullet to sharpen framing toward what they value.
- **Reorder** bullets within a job so role-relevant ones lead.
- **Drop** a bullet that's noise for this role.
- **Surface** a detail already true but previously omitted (only if the user
  confirms or it's directly supported by existing content).
- **Reorder jobs** (rare — keep reverse-chron unless strong reason; flag).

**Hard rules on proposed diffs:**
- No invented metrics, titles, dates, team sizes, or management scope.
- No keyword stuffing. If a JD term doesn't describe something they actually
  did, it doesn't go in. Matching the *substance* beats matching the
  *vocabulary*.
- Preserve voice: apply the register, signature moves, banned words, and
  formatting rules from `voice.md`. Generic floor: first person, direct,
  concrete over corporate. If a proposed rewrite sounds like a LinkedIn
  auto-write, rewrite it. When unsure, write plainer, not fancier.
- Every "After" must trace to something in the master, the career-facts
  inventory, or the user's confirmation.
- Flag stretches: if framing leans hard, mark it in the proposal.
- **Never propose a locked-section edit.** Summary stays verbatim by default.

Then ask the user which proposals to apply. They may accept all, some, or
none, or ask for alternate framings.

### 6. Apply approved diffs + compile — only after the user confirms
Once the user picks the subset to apply:

1. Copy the master to `applications/<company>/resume.<Company>.<ext>` if it
   doesn't exist yet.
2. Apply ONLY the approved diffs to the copy. Leave locked sections and the
   summary alone.
3. Compile using the exact command in `templates/README.md`, writing the
   output PDF into `applications/<company>/`.
4. Verify page count per `templates/README.md` (1 page for a resume unless
   the README says otherwise). If it spilled, tighten via wording/reorder —
   **do not** shrink fonts or rewrite the template.
5. Render an image of the PDF and actually look at it before declaring done —
   confirm it compiled clean, fits the page budget, and reads right.

### 7. Report the changes
Show the user, concisely:
- **Fit thesis** (the 1–2 sentences from step 4).
- **Research that drove it** — 3–5 findings with sources.
- **Which proposed diffs were applied** — reference the diff labels from
  step 5.
- **Stretches / things to verify** — anything leaning, plus questions whose
  answers would strengthen the resume (don't guess them).
- Path to the PDF.

Commit and push the finalized work to the workspace repo — never to the
plugin repo.

## Attention checklist (re-read your own diff proposal before presenting)
- [ ] Are all locked sections (summary by default) untouched in every proposal? (must be yes)
- [ ] Did any number, title, or scope change vs the master? (must be no)
- [ ] Would a hiring manager see a specific reason they fit, or just keywords?
- [ ] After applying, is it within the page budget from templates/README?
- [ ] Did I cite real research, or hand-wave the company?
- [ ] Did I flag every stretch instead of hiding it?

## Voice reference (don't drift from this)
Apply `voice.md` as the source of truth. Generic floor: direct, first-person,
concrete, no résumé clichés ("results-driven," "synergize," "passionate
about"). When unsure, write plainer, not fancier.

**Corrections compound.** When the user rewords a bullet, rejects a phrasing,
or corrects a fact or number during iteration, record the durable lesson
(dated) in `voice.md` (style) or `profile.md` (facts) before finishing — a
correction that lives only in the chat dies with the session. Recorded
corrections outrank old examples when they conflict.
