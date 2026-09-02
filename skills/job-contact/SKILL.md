---
name: job-contact
description: |
  Find the best person to message on LinkedIn about a job the user has
  applied to, verify they are real and current, and deliver their profile
  link plus the reasons they were chosen, with outreach drafted in the
  user's no-ask register. Always checks first whether the user already
  knows someone there: a 1st-degree connection at the company is a
  referral path that outranks every cold target. Requires the Chrome
  browser tools (claude-in-chrome) with a logged-in LinkedIn session;
  will not run on web search alone. Use when: "find me someone to reach
  out to", "who should I message at <company>", "find a contact for this
  job", "/job-hunt:job-contact <company or job url>". Pairs with
  cover-letter (the application side); runs after an application is in or
  imminent.
argument-hint: "<company name or job URL>"
disable-model-invocation: true
---

# Job Contact

Find the one person worth messaging about a job the user applied to,
prove they still work there, and draft outreach that costs the reader
nothing. Verification is most of the work: web search returns stale
titles, and messaging someone who left the company is worse than
messaging no one.

Start every run: read `~/.claude/job-hunt/config.md`. If it is missing,
STOP and tell the user to run `/job-hunt:init`. Then read `profile.md`
and `voice.md` from the workspace it points to; read `examples/` before
drafting any message.

## Requirement: Chrome, verified first

This skill runs on the user's logged-in LinkedIn session through the
Chrome tools (mcp__claude-in-chrome__*). Web search and WebFetch are
NOT acceptable substitutes: they serve cached, stale profiles, which is
exactly the failure this skill exists to prevent, and LinkedIn blocks
anonymous fetches anyway.

Before any research, verify the connection: load the Chrome tools via
ToolSearch if deferred, then call tabs_context_mcp. If the extension
does not respond or no browser is connected, STOP and tell the user to
open Chrome and connect the Claude extension. Do not fall back to
WebSearch for people-finding; the only permitted WebSearch use here is
non-people context (company news, funding dates) where staleness is
detectable from dates.

## Where results go

Workspace applications folder: `<workspace>/applications/<company>/outreach.md`
(create alongside the cover-letter files; lowercase folder). One file
per company holds every draft, the target's verified profile URL, and
the research trail that justified the pick. Commit with the rest of the
company folder in the workspace repo (never the plugin repo).
If outreach targets a single named person, `outreach.<FirstLast>.md`
also works; keep to one convention per company.

## Target ranking

Work down this list; stop at the first verified hit.

0. **A 1st-degree connection who works there.** A referral beats every
   cold message; many forms ask "were you referred?" outright. Found
   via the connection search below. If one exists, the deliverable
   becomes THEM plus a referral ask (register still applies: name the
   role, make declining easy), and the cold-outreach targets below
   drop to backup.
1. **The person who posted the job.** They own the req and expect
   inbound. Find them via the company's LinkedIn feed, a content search
   for hiring posts, and the recent activity of company recruiters and
   the relevant team lead. Many reqs have no poster: a listing marked
   "Promoted by hirer · Responses managed off LinkedIn" is a paid
   promotion with nobody behind it, so record that and move on.
2. **Head of the function** the role sits in (Head of Infra, VP Eng at
   a small company). At 51-200 people this person is the hiring manager
   or one step above. Right target when the message carries no ask.
3. **A peer holding the same title as the open role.** Best odds of
   replying; can describe the actual job; cannot hire. Check the
   LinkedIn job listing's "People you can reach out to" box, which
   sometimes names one. Prefer as second touch after 1 or 2.
4. **Recruiting/People Ops staff.** Warm path when they are 2nd degree
   or when they personally posted the role.

Degree beats seniority for getting seen: a 2nd-degree function head
outranks a 3rd-degree peer. Log everyone found, ranked, in outreach.md;
the runner-up is the second touch if the primary stays silent.

## Connection search (run before the org map)

Three searches, cheapest first. <id> is the numeric company id from the
people page's canned-search link.

1. **1st degree at the company**:
   `search/results/people/?currentCompany=%5B%22<id>%22%5D&network=%5B%22F%22%5D`
   Anyone here is ranking item 0. Empty result: say so and move on.
2. **2nd degree with the bridge named**: same URL with
   `network=%5B%22S%22%5D`. Result cards name a mutual ("X is a mutual
   connection"). Log the bridges next to their targets in the ranked
   table.
3. **Full mutual list for a chosen target**: the shared-connections
   search LinkedIn links from a target's card
   (`connectionOf=<urn>&origin=SHARED_CONNECTIONS_CANNED_SEARCH`).
   Card mutuals are a sample, not the set; use this before deciding a
   target has no useful bridge.

**Every mutual gets the who-are-they check before being used.** Mutuals
surfaced by these searches are frequently recruiters (placement-network
artifacts, known to both sides as recruiters): worthless as warm paths
and citing one reads as manufactured warmth. A mutual is a real bridge
only if they plausibly know the user and the target as colleagues, not
as inventory. Private connection lists hide mutuals; absence of a
shown mutual is not proof of no relationship.

## Verification (non-negotiable)

- **currentCompany filter is the only source of truth.** Company id
  from the /company/<slug>/people/ page's canned-search link, then
  `linkedin.com/search/results/people/?currentCompany=%5B%22<id>%22%5D&keywords=...`.
  Company People pages, TheOrg, LeadIQ, and web search all serve stale
  profiles. A web search's top "VP of Engineering" can have left while
  the headline still names the old employer; the filter catches it.
- **Check recent activity.** Posts or reposts in the last few months
  mean a live account and give the outreach a hook. A dead account
  wastes the message.
- **Check who a mutual connection actually is** before citing them. A
  shown mutual is often a recruiter who places candidates at companies
  like the target's; both sides know them as a recruiter, and naming
  them reads as manufactured warmth. Confirm the mutual is a real
  colleague-level bridge before using them.
- **Read the listing itself.** Repost age and applicant count
  ("Reposted 2 weeks ago", "Over 100 people clicked apply") are intel:
  a reposted role did not fill, which is worth knowing on a call.
- Names conflicting across sources: trust the currentCompany filter,
  then the person's own profile, in that order. Say so in outreach.md.

## Message register

Default register (overridden only by the user's `## Outreach` section in
voice.md): the message acknowledges a cold DM costs the reader time,
says the user applied, and says briefly who they are. Nothing else.

- **No question. No ask. No "15 minutes." No pretending a conversation
  is underway.** These get rejected.
- Open by naming what it is: a cold note, kept short.
- One identity line in the user's voice (from voice.md / examples/), at
  most two resume numbers.
- Close by thanking them for the read, not for a future reply.
- Connection-invite notes: under 300 characters, verified by counting.
  Longer version for InMail or post-accept.
- Apply the banned-word list and formatting rules from voice.md. If
  voice.md has an `## Outreach` section, its approved phrasing and
  openers/closers override this default register.
- Duration honesty: use the career start / duration facts recorded in
  profile.md; flag which number a draft uses.

## Deliverable

The answer to the user is a LINK and a WHY, in the reply itself, not
buried in a file:

- **Profile URL of the primary target**, stated plainly at the top.
- **Why this person**: the ranking rule they won on (posted the req,
  owns the function, same title), their degree, and the verification
  that passed (currentCompany filter, recent activity, mutual checked).
  Each reason one line.
- **Runner-up with URL** and the one-line case for them as second touch.
- Drafts follow the link, never replace it.

outreach.md still records everything (drafts, ranked table, research
trail), but a reply that makes the user open the file to learn who to
message has failed. Never deliver a URL that was not visited or taken
verbatim from a LinkedIn results page this session; a guessed slug is
worse than "slug not captured, find via <people page URL>", which is
the honest fallback.

## Sequence

0. Verify Chrome is connected (tabs_context_mcp responds). Not
   connected: stop, ask the user to connect, do not proceed on WebSearch.
1. Confirm the application is in (or say the drafts assume it).
2. Run the connection search (ranking item 0). A 1st-degree hit
   reorders everything: deliverable becomes the referral path.
3. Hunt the job poster (ranking item 1); record the negative result if
   none exists.
4. Map the org with the currentCompany filter; build the ranked table
   (name, verified title, degree, location, profile URL).
5. Verify the primary: activity check, mutual check.
6. Draft: invite note (<300 chars, counted) + longer version, in the
   register above. Show both; the user edits; their wording is canon.
7. Write outreach.md with drafts, URLs, ranked table, and research
   trail (including what was ruled out and why). Commit and push with
   the company folder in the workspace repo.
8. Reply with the Deliverable: primary's profile URL + why chosen,
   runner-up + why, then the drafts.
9. Never send anything. Drafts only; sending is the user's. A referral
   ask is also a draft; asking is the user's.

## Flags to raise every time

- Opening a profile from the user's account can appear in the target's
  "who viewed your profile." Say so after the first profile visit.
- If research surfaced non-obvious intel (repost age, applicant count,
  org has nobody in the role's function), put it in the summary; it is
  usually worth more than the contact itself.
