---
name: init
description: |
  Set up (or refresh) the private job-hunt workspace that every other
  job-hunt skill reads from: profile, voice rules, templates, and examples.
  Extracts the user's writing voice from real samples and interviews for
  what samples can't show. Use when: "set up my job hunt workspace",
  "/job-hunt:init", the first run of any job-hunt skill (missing
  ~/.claude/job-hunt/config.md), "onboard my resume/letters into job-hunt",
  "migrate my job-hunt setup to this machine", or "update my voice/profile
  for job applications".
argument-hint: "[workspace path]"
user-invocable: true
allowed-tools: Read, Write, Edit, Glob, Grep, Bash(git:*), Bash(mkdir:*), Bash(ls:*)
---

# Job Hunt Init

Build the private workspace that makes the other job-hunt skills
(`/job-hunt:cover-letter`, `/job-hunt:resume-tailor`, `/job-hunt:job-contact`)
work in THIS user's voice with THIS user's real history. The plugin ships no
personal data; everything personal lands here, locally, ideally in a private
git repo the user controls.

## The two artifacts

1. **Pointer config** at `~/.claude/job-hunt/config.md` — tells every skill
   where the workspace is. See [references/config-template.md](references/config-template.md).
2. **Workspace** at the user's chosen path:

   | Path | Role |
   |------|------|
   | `profile.md` | Identity, location + constraints, confirmed motivation anchors, career-facts inventory (the ONLY source of claims), duration-honesty notes. |
   | `voice.md` | Register, signature moves, approved lines, banned words and patterns, review corpses, formatting rules, outreach register. |
   | `templates/` | The user's resume and cover templates, plus `templates/README.md` with exact compile and verify commands for their toolchain. |
   | `examples/` | Finished, user-approved letters and messages. Best voice source; grows over time. |
   | `applications/<company>/` | Per-company outputs (created by the other skills). |

## Workflow

### 1. Check for an existing setup

Read `~/.claude/job-hunt/config.md`. If it exists and points at a real
workspace, this is a **refresh**: ask what to update (voice, profile,
templates) and jump to that step. Otherwise this is a first run.

### 2. Choose the workspace location

Ask the user where it should live. Good answers, in order of preference:

- An existing private resume/applications repo they already keep.
- A new directory (suggest `~/Documents/job-hunt/`) — offer to `git init` it.

Warn once: this workspace will hold personal data. It must never live inside
a public repo, and the other skills will commit to it if it is a git repo.
Create the directory structure and write the pointer config.

### 3. Gather raw materials

Ask for paths or pastes of whatever exists:

- Current resume (any format). Record its path as `master_resume` in the config.
- Past cover letters, application answers, outreach messages — especially any
  the user actually sent or explicitly liked. These seed `examples/`.
- Other writing in their natural professional voice (emails, posts, READMEs).
- Existing templates (Typst, LaTeX, docx, Markdown toolchains all fine).

No materials is fine; the interview below carries more weight then.

### 4. Extract the voice

Read every sample end to end, then follow
[references/voice-extraction.md](references/voice-extraction.md) to draft
`voice.md` from [references/voice-template.md](references/voice-template.md).
Show the draft and iterate. The user's edits are canon: when they change a
line, keep their wording and infer the rule it implies. Do not finalize a
voice.md the user has not read.

If there are no samples, run the extraction guide's interview questions
instead and mark `voice.md` as interview-derived so future skills know to
lean on `examples/` as it fills up.

### 5. Build the profile

Draft `profile.md` from [references/profile-template.md](references/profile-template.md).
Two sources:

- **The resume** fills the career-facts inventory: real systems, real numbers,
  real dates. Read them back to the user and have them confirm each number —
  these become the only claims the other skills may make.
- **An interview** fills what no document shows. Ask, one topic at a time:
  - Location, timezone, and what office/relocation arrangements are acceptable.
  - Hard constraints (comp floor, visa, industries they won't touch).
  - Motivation anchors: real reasons they want particular kinds of work.
    Record only what they confirm; the cover-letter skill is forbidden from
    inventing motivation, so what's written here is all it gets.
  - Duration facts: career start date, when key specialties began. These
    prevent accidental resume inflation later.

### 6. Set up templates

Copy existing templates into `templates/` and write `templates/README.md`
recording, exactly:

- The compile command(s) for resume and cover letter.
- The verify steps (expected page count; how to render a preview image).
- File-naming conventions for per-company outputs.

If the user has no templates, offer a plain-Markdown starter (letter as `.md`,
export via whatever they have — even print-to-PDF) rather than forcing a
toolchain. Whatever the choice, `templates/README.md` must exist: the other
skills follow it blindly.

### 7. Seed examples and finish

Copy approved past letters/messages into `examples/` with a one-line header
each (role, company type, what the user liked about it). If the workspace is
a git repo, commit everything. Then summarize what was set up and point at
the next step: `/job-hunt:resume-tailor` or `/job-hunt:cover-letter` for a
real posting.

## Done when (validate before declaring finished)

- [ ] `~/.claude/job-hunt/config.md` exists with `workspace:` and `master_resume:` set to real paths.
- [ ] All five workspace paths exist (`profile.md`, `voice.md`, `templates/`, `examples/`, `applications/`).
- [ ] Every number in the career-facts inventory was read back and confirmed by the user.
- [ ] `voice.md` passed the calibration test in the extraction guide ("does this sound like you?" = yes).
- [ ] `templates/README.md` records a compile command and a verify step, even for a Markdown starter.
- [ ] If the workspace is a git repo: committed. If it's inside a public repo: refused and re-asked.

## Rules

- Never write personal data anywhere except the workspace and the pointer
  config. Never into the plugin, never into a public repo.
- Ask, don't invent: every anchor, number, and preference is either from a
  document the user supplied or confirmed by them out loud.
- Voice extraction describes how the user writes, not how cover letters
  "should" sound. If their samples break conventional advice, the samples win.
- A refresh never silently overwrites: show diffs of `voice.md` / `profile.md`
  changes before writing.
