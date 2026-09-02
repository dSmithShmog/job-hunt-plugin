# job-hunt

A Claude Code plugin for running a job hunt in **your own voice** — resume
tailoring, story-driven cover letters, and verified LinkedIn outreach — with a
hard separation between the tooling (this public repo) and your personal data
(a private local workspace the plugin sets up for you).

## What's in it

| Skill | Invoke | What it does |
|-------|--------|--------------|
| **init** | `/job-hunt:init` | One-time setup. Builds your private workspace: extracts your writing voice from real samples, interviews you for constraints and motivations, inventories your real career facts, and records your resume toolchain. |
| **resume-tailor** | `/job-hunt:resume-tailor <company> [JD]` | Researches the company, finds an honest unique-fit thesis, proposes bullet-level diffs (before/after/why) for your approval, then compiles a per-company variant. Never invents metrics; never touches your master. |
| **cover-letter** | `/job-hunt:cover-letter [company or JD URL]` | Reads the actual application form (not the aggregator), researches the company with dated sources, and drafts a short story-driven letter in your voice — plus answers to the form's open-ended questions. |
| **job-contact** | `/job-hunt:job-contact <company>` | Finds the best person to message about a job you applied to, verifies they still work there via your logged-in LinkedIn session, and drafts no-ask outreach. Never sends anything. |

## The privacy model

This repo contains **zero personal data** — no resume content, no letters, no
names, no locations. Everything personal lives in a workspace you choose
(ideally a private git repo):

```
<your-workspace>/
  profile.md              # who you are: constraints, confirmed motivations,
                          # career-facts inventory (the only source of claims)
  voice.md                # how you write: register, signature moves, bans
  templates/              # your resume/cover templates + compile instructions
  examples/               # finished letters you approved (voice ground truth)
  applications/<company>/ # per-company output
```

A pointer at `~/.claude/job-hunt/config.md` tells the skills where the
workspace is. Every skill refuses to run until `/job-hunt:init` has created it.

## Install

```
/plugin marketplace add dSmithShmog/job-hunt-plugin
/plugin install job-hunt@job-hunt
```

Then run `/job-hunt:init`.

## Design principles

- **Truth over keywords.** Claims trace to your career-facts inventory or your
  explicit confirmation. Stretches get flagged, not hidden.
- **Ask, don't invent.** Motivation comes from you; inferred angles are
  proposed as inferences, never silently written.
- **Your edits are canon.** When you rewrite a draft, the skills learn the
  rule and never revert your wording.
- **Form first.** The application form is the deliverable spec; the JD is just
  input.
- **Drafts only.** Nothing is ever submitted or sent on your behalf.

## Requirements

- Claude Code.
- Your own resume toolchain (Typst, LaTeX, docx, or plain Markdown — recorded
  during init).
- `job-contact` additionally needs the Claude in Chrome extension with a
  logged-in LinkedIn session.

## License

MIT
