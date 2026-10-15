# Guide for AI agents

This project finds and applies to LinkedIn jobs using Claude Code and the Playwright MCP browser.
There is no code to build. Everything is Markdown instructions plus one MCP server.

## Map
| Path | What it is |
| --- | --- |
| `.claude/commands/find-jobs.md` | The `/find-jobs` command |
| `.claude/commands/apply-jobs.md` | The `/apply-jobs` command |
| `.claude/agents/resume-analyzer.md` | Resume to `output/resume-profile.md` |
| `.claude/agents/job-search.md` | LinkedIn search to `output/<date>/jobs.txt` |
| `.claude/agents/job-applicator.md` | One job to one line in `output/<date>/applications.txt` |
| `my-files/` | The user's private files: resume and `my-details.md` |
| `output/` | Everything the agents write |
| `.mcp.json` | Starts the Playwright MCP server |

## Workflow
1. `/find-jobs`: check setup, check LinkedIn login, run resume-analyzer, run job-search.
2. `/apply-jobs`: pick unapplied jobs, ask the user once, check login, run job-applicator for each job.

## Rules
- The user logs in to LinkedIn by hand in the browser window. Never ask for, store or type a password.
- Use only facts from `my-files/my-details.md` and the resume. Never invent answers.
- Ask the user before submitting applications.
- Never commit anything in `my-files/` (except the example) or `output/`.
- Dates are YYYY-MM-DD in the user's local time.

## First time setup
If `my-files/my-details.md` is missing, copy `my-files/my-details.example.md` to it and help the user fill it in.
