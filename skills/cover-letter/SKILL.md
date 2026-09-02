---
name: cover-letter
description: |
  Draft a cover letter for a specific job in the user's voice: research the
  company, identify its stated mission, and build a short story-driven letter
  rather than a keyword match. Use when: "write a cover for <company>",
  "cover letter for this JD", "draft a cover", "/job-hunt:cover-letter <url>",
  or any request to produce a cover letter for a job application. Pairs with
  /job-hunt:resume-tailor (resume side) but runs standalone.
allowed-tools: Read, Write, Edit, Grep, Glob, WebFetch, WebSearch, Bash(typst:*), Bash(curl:*), Bash(python3:*), mcp__claude-in-chrome__tabs_context_mcp, mcp__claude-in-chrome__tabs_create_mcp, mcp__claude-in-chrome__navigate, mcp__claude-in-chrome__get_page_text, mcp__claude-in-chrome__read_page
argument-hint: "[company name or JD URL]"
---

# Cover Letter

Write a cover letter that tells one story: why the user is right for THIS job
and why they actually want it. The letter carries story and motivation; the
resume carries keywords. Never let the letter become a requirements checklist.

## Workspace (single source of truth)

Start every run here:

1. Read `~/.claude/job-hunt/config.md`. If it is missing, STOP and tell the
   user to run `/job-hunt:init`. It records `workspace: <path>` and
   `master_resume: <path>`.
2. Read `<workspace>/profile.md` (identity, location, constraints, confirmed
   motivation anchors, and the career-facts inventory that is the ONLY source
   of claims) and `<workspace>/voice.md` (register, signature moves, approved
   lines, banned words and patterns, formatting rules).
3. Before drafting a single word, read `<workspace>/examples/` — finished,
   user-approved letters are the best voice source.

Company-specific work lives in `<workspace>/applications/<company>/`
(lowercase folder). Create it for a new company.

| File (in `applications/<company>/`) | Role |
|------|------|
| `cover.<Company>.md` | Letter text. THE file to edit. `#` lines are notes, never shown in output. Blank line = new paragraph. |
| Layout/source file per `templates/` | Layout only; reads the `.md` at compile time. Copy from an existing cover template and update its internal references (title, read path). Only if the toolchain uses a separate layout file. |
| `<LastnameFirstname or per templates>_<Company>_Cover.pdf` | Output. Distinct name from the resume PDF. |
| `essay.<Company>.md` | Answers to the application's open ended questions. One heading per question, carrying the verbatim prompt and its word or character limit. Only if the form asks any. |
| Tailored resume variant | If one exists (see /job-hunt:resume-tailor). |

**Compile and verify** using the commands in `<workspace>/templates/README.md`
(the user's toolchain: typst, latex, docx, or md). Do not hard-code a compiler.
After compiling: confirm the page count matches `templates/README.md` (a cover
is normally 1 page), export a PNG of the PDF, and actually look at the image
before declaring done. A file that never compiled, or that you never viewed, is
not finished.

Commit and push to the workspace repo after a letter is finalized. Never commit
to the plugin repo.

## Workflow

### 1. Get the JD

WebFetch first. If 403: try curl with a browser UA; for Ashby boards use the
public API (`https://api.ashbyhq.com/posting-api/job-board/<org>?includeCompensation=true`);
for authwalled pages use the user's Chrome session. If nothing works, ask the
user to paste it. Never write from a guessed JD.

Extract: title, team, seniority, the role's actual center of gravity (what the
team's top priority is, not the requirements list), culture signals, location
and remote policy, comp.

**Location tripwire, check before any research or drafting.** Resolve the
EMPLOYER'S OWN posting (not the aggregator) and read its location/workplace
line first. Aggregators mislabel: a role posted as four-days-in-office has been
listed as "Remote," and a role screened to specific state or time-zone
residency has been fed as nationwide. Compare the employer's stated
location and workplace policy against the user's location and constraints in
`profile.md`. If the posting requires office presence, relocation, or residency
the user cannot meet, STOP and ask before spending anything further. Whether a
job justifies a move or a screen-out risk is the user's call, not letter
material.

### 2. Open the actual application page

Aggregators (builtin.com, LinkedIn, Indeed, Wellfound) mirror the JD text and
strip the form. The form is the deliverable spec. Follow the "Apply" link
through to the employer's own board and READ THE FORM before drafting a word.

Answer three questions:

- **Is a cover letter accepted at all?** Plenty of ATS forms have no upload
  slot and no letter field. If there is none, say so before writing one.
- **Required or optional?** Greenhouse and Lever both mark this. Optional is
  still usually worth writing; required just confirms it ships.
- **What open ended questions does the form ask?** This is the real find.
  "Why do you want to work at X?", "What excites you about the mission?",
  "Tell us about a time you..." Capture each prompt VERBATIM along with its
  word or character limit.

Where the form lives:

| ATS | How to read it |
|-----|----------------|
| Greenhouse | `job-boards.greenhouse.io/<org>/jobs/<id>` — custom questions live in the page HTML |
| Lever | `jobs.lever.co/<org>/<id>/apply` shows the full form |
| Ashby | JD via the public posting API; the form itself only appears on the page |
| Workday, Rippling, authwalled | use the user's Chrome session, never guess |

Report to the user before drafting: whether a letter is wanted, and the verbatim
list of questions with limits. Open ended questions are part of THIS job, not a
follow up task. Draft them alongside the letter per step 8.

If the application page cannot be reached, say that plainly and ask the user to
paste the form. Never assume the form wants a letter, and never invent a
question it did not ask.

### 3. Research the company (required)

Find, with sources:
- **Mission as they state it** — their words, verbatim, from their own site.
- **Values / how-we-work language** — careers page, eng blog, founder writing.
- **Recent concrete milestones with dates** — funding, launches, contracts,
  certifications. These become P3's earned specifics.
- **What the product actually is** and who uses it.

**Verify before you cite.** Two failure modes seen in real sessions: asserting a
"fact" about the company's world that was false (a domain claim the user, who
knew the field, caught), and stating a pending milestone as done (an
authorization described as issued when it had not been). Every company fact in
the letter must trace to a dated source, and forward-looking claims get
rewritten as what has already happened. Check the company newsroom the day of
submission — milestones move.

### 4. Find the personal motivation — ask, don't invent

P3 requires a real reason the user wants this job. **Never fabricate or infer
it silently.** The user's confirmed motivation anchors live in `profile.md`;
use only those. If research suggests a new angle (geography, domain, personal
connection), propose it explicitly flagged as inference and ask before using it.
A borrowed or reason-shaped paragraph reads worse than a plain one, and the user
will catch it.

### 5. Draft in the three-beat form

Total ~150–250 words (survey data says 250–400 max; shorter beats longer):

- **P1 — Thesis.** 2–3 sentences. A belief about the work, stated as fact,
  aimed at this company's actual architecture or failure mode. Not a greeting,
  not "I am writing to apply."
- **P2 — Evidence.** Opens with an accountability claim tied to P1's noun. Two
  to four real systems with numbers, drawn only from the career-facts inventory
  in `profile.md`. Only systems that actually ran: an MVP that never took
  production traffic cannot carry a stakes claim. Ends with a through-line, not
  a list.
- **P3 — Why this company.** One or two earned, dated company specifics. The
  real personal reason (from step 4). A concrete "I want to..." closer, no
  hedged endings.

The opener line, the P3 opener, and the closer come from the user's signature
moves and approved lines in `voice.md`. Study `examples/` for how these letters
actually sound before you write them.

### 6. Self-review before showing the draft

- **Kill AI tells:** correlative constructions ("not X, but Y", "I don't mean
  X. I mean Y"), aphoristic balance closes, "worth a look?", significance-
  reaching abstractions ("people live downstream of that problem"), causal
  chains with missing links. The concrete sentence survives; the profound one
  gets cut.
- **Hostile read:** every claim gets the least charitable interpretation.
  "Actually used" reads as defensive. Self-reported multipliers get probed
  (tripling = 200% increase, not 300%). Diagnosing the company from a press
  release gets torched.
- **Duration honesty:** don't stretch timelines (e.g. "two years of AI work"
  when the field is younger than that). Follow the duration-honesty notes in
  `profile.md`.
- **Claims trace to inventory:** every claim maps to the career-facts inventory
  in `profile.md`, a finished letter, or the user's explicit confirmation. Only
  systems that actually ran can carry stakes claims.
- **Apply `voice.md`:** run the banned-words and banned-patterns list and the
  formatting rules from `voice.md` over the whole draft, including any PDF title
  metadata.

### 7. Iterate with the user — their edits are canon

The user rewrites drafts. When they do: diff, infer why each change was made,
give ranked feedback (must-fix grammar/typos → weak lines → nits), and never
revert their wording without asking. Flag typos; fix only on their word. When
they ask for options, give 3–5 labeled variants with a pick and a reason — no
file edits during option rounds.

### 8. Open ended questions and essay fields

Answer whatever step 2 found on the form. Each answer expands the letter with
NEW material and never duplicates numbers or phrases the letter already used.
One focused answer beats a survey. Respect the stated limit; if none is given,
match the letter's economy.

A mission or "why us" essay is P3 with room to breathe, so it goes where P3
could not: the specific thing about the product, the person it reaches, the part
of the craft that holds the user's attention. If P3 runs on craft because there
is no personal pull, the essay does too. It does not manufacture conviction to
fill the box.

Save every answer to `essay.<Company>.md`, one heading per question with the
prompt quoted verbatim above the answer, so the file can be pasted straight into
the form later.

## Rules

- Read the application form before drafting. What the form asks for is the
  deliverable; the JD only says what to put in it.
- Story over keywords. Do not address tech-stack gaps in the letter unless the
  user explicitly asks; gaps are screen-call answers, not letter content.
- Every claim traces to the career-facts inventory in `profile.md`, a finished
  letter, or the user's explicit confirmation. No invented metrics, motives, or
  timelines. Flag stretches.
- JD must-have lists are wish lists; don't let them scare the letter into
  defensiveness. But never claim proficiency the user does not have.
- One technology mention per sentence at most, and only inside a system story.
- Personal facts about third parties (family and others) stay vague: no names,
  no units.
- Location tension is flagged, not hidden (a home-city anchor in a letter to a
  four-days-in-office company in another city works against the user — say so).

## Voice

Generic floor: direct, first person, concrete over corporate, contractions,
short declaratives, plain verbs. Never the résumé clichés "passionate about",
"results-driven", "synergize". When unsure, write plainer. The user's specific
register, signature moves, approved lines, and full banned list live in
`voice.md`, and the finished letters that show the voice in action live in
`examples/`. Read both before drafting a single word.
