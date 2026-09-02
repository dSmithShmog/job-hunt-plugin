# Config template

Written to `~/.claude/job-hunt/config.md`. Keep it to keys the skills read;
comments are fine. Paths absolute, `~` allowed.

```markdown
# Job Hunt Config

workspace: ~/Documents/job-hunt
master_resume: ~/Documents/job-hunt/resume.yaml   # the file resume-tailor treats as master; never edited per-job
workspace_is_git: true                            # skills commit+push finalized work when true
```

Optional keys (add only if true for this user):

```markdown
resume_locked_sections: summary                   # comma list; sections that stay verbatim in every tailored variant (summary is locked even without this key)
```
