# Claude LinkedIn Automation

Find fresh LinkedIn jobs that match your resume and apply to them with Easy Apply, using Claude Code and a real browser.

- `/find-jobs` reads your resume, searches LinkedIn, and saves matching job links.
- `/apply-jobs` applies to those jobs with your details and logs every result.

You log in to LinkedIn yourself, once, in the browser window. Your password is never stored or typed by the AI.

## What you need
- [Claude Code](https://code.claude.com/docs/en/setup)
- [Node.js](https://nodejs.org) 18 or newer
- [Git](https://git-scm.com) (optional, you can download a ZIP instead)
- A LinkedIn account

## Quick setup with an AI agent

Open Claude Code (or any coding agent) in the folder where you want the project and paste this:

```text
Set up this project for me: https://github.com/spacesdrive/claude-linkedin-automation

1. Check that Git and Node.js 18 or newer are installed. If one is missing, install it
   (winget on Windows, Homebrew on macOS) and tell me what you installed.
2. Clone the repository and go into the claude-linkedin-automation folder.
3. Copy my-files/my-details.example.md to my-files/my-details.md. Ask me for each value
   and fill it in. Leave a value blank if I skip it.
4. Ask me where my resume is and copy it into my-files/. PDF works best.
5. Run "claude mcp list" and make sure the playwright server connects. On Windows, if it
   fails, run: claude mcp add playwright -s local -- cmd /c npx -y @playwright/mcp@latest
6. Do not commit anything. When done, tell me to start Claude Code in the
   claude-linkedin-automation folder and run /find-jobs.
```

## Manual setup

### Windows (PowerShell)

```powershell
# 1. Install the tools (skip any you already have)
winget install --id Git.Git -e
winget install --id OpenJS.NodeJS.LTS -e
irm https://claude.ai/install.ps1 | iex

# 2. Download the project
git clone https://github.com/spacesdrive/claude-linkedin-automation.git
cd claude-linkedin-automation

# 3. Create your details file, then open it and fill it in
Copy-Item my-files\my-details.example.md my-files\my-details.md
notepad my-files\my-details.md

# 4. Put your resume in my-files (change the path to yours)
Copy-Item "$HOME\Downloads\resume.pdf" my-files\

# 5. Start Claude Code
claude
```

No Git? Download the ZIP instead of step 2:

```powershell
Invoke-WebRequest https://github.com/spacesdrive/claude-linkedin-automation/archive/refs/heads/main.zip -OutFile repo.zip
Expand-Archive repo.zip -DestinationPath .
cd claude-linkedin-automation-main
```

### macOS (Terminal)

```bash
# 1. Install the tools (skip any you already have; needs Homebrew from https://brew.sh)
brew install git node
curl -fsSL https://claude.ai/install.sh | bash

# 2. Download the project
git clone https://github.com/spacesdrive/claude-linkedin-automation.git
cd claude-linkedin-automation

# 3. Create your details file, then open it and fill it in
cp my-files/my-details.example.md my-files/my-details.md
open -e my-files/my-details.md

# 4. Put your resume in my-files (change the path to yours)
cp ~/Downloads/resume.pdf my-files/

# 5. Start Claude Code
claude
```

No Git? Download the ZIP instead of step 2:

```bash
curl -L https://github.com/spacesdrive/claude-linkedin-automation/archive/refs/heads/main.zip -o repo.zip
unzip repo.zip
cd claude-linkedin-automation-main
```

## How to use

Start Claude Code inside the project folder. The first time, it asks you to approve the `playwright` MCP server and its browser tools. Say yes.

```text
/find-jobs
```

A browser window opens. If LinkedIn asks you to log in, do it in that window and reply `done`. The login is remembered for next time.
Claude reads your resume, searches LinkedIn, and saves the job links.

```text
/apply-jobs
```

Claude shows the jobs it will apply to and waits for your yes. Then it applies one by one and logs each result.

If a job asks something that is not in your details, Claude skips it and shows you the question. Add the answer under "Extra answers" in `my-files/my-details.md` and run `/apply-jobs` again.

You can also pass a resume path or a date:

```text
/find-jobs my-files/resume.pdf
/apply-jobs 2026-01-31
```

## How it works

```text
/find-jobs
  1. Check setup (my-details.md, resume, browser)
  2. Check LinkedIn login (you log in by hand if needed)
  3. resume-analyzer  ->  output/resume-profile.md
  4. job-search       ->  output/<date>/jobs.txt

/apply-jobs
  1. Pick jobs from jobs.txt that you have not applied to yet
  2. Ask you to confirm
  3. job-applicator, once per job  ->  output/<date>/applications.txt
```

## Folder structure

```text
claude-linkedin-automation/
├── .claude/
│   ├── commands/
│   │   ├── find-jobs.md          /find-jobs command
│   │   └── apply-jobs.md         /apply-jobs command
│   └── agents/
│       ├── resume-analyzer.md    resume to profile
│       ├── job-search.md         search LinkedIn, save links
│       └── job-applicator.md     apply to one job, log result
├── my-files/                     your private files (not shared on GitHub)
│   ├── my-details.example.md     template to copy
│   ├── my-details.md             your details (you create this)
│   └── resume.pdf                your resume (you add this)
├── output/                       created when you run the commands (not shared)
│   ├── resume-profile.md
│   └── 2026-01-31/
│       ├── jobs.txt              one job link per line
│       └── applications.txt      one line per application
├── .mcp.json                     starts the Playwright browser server
├── CLAUDE.md                     guide for AI agents
├── LICENSE
└── README.md
```

Each line in `applications.txt` looks like this:

```text
2026-01-31 14:05 | APPLIED | Company | Job title | https://www.linkedin.com/jobs/view/123/ | 
```

The status is `APPLIED`, `SKIPPED`, `NEEDS_INFO` or `FAILED`.

## Change the settings

Everything is set in `my-files/my-details.md`:

| Setting | Default |
| --- | --- |
| Job titles to search | Taken from your resume |
| Locations | None (LinkedIn decides) |
| Work type | any |
| Experience level | any |
| Posted within | 24 hours |
| Max jobs to collect per run | 25 |
| Max applications per run | 10 |
| Easy Apply only | yes |

To rebuild your resume profile after you change your resume, delete `output/resume-profile.md`.

## Troubleshooting

**The playwright server does not connect.** Run `claude mcp list`. On Windows, if it fails, run this in the project folder and restart Claude Code:

```powershell
claude mcp add playwright -s local -- cmd /c npx -y @playwright/mcp@latest
```

**The browser asks you to log in every time.** Close every other Claude Code session that uses this project. Only one browser can use the saved login at a time.

**Log out or switch LinkedIn accounts.** Delete the saved browser profile, then run `/find-jobs` again:

- Windows: `%USERPROFILE%\AppData\Local\ms-playwright\` (the folder that starts with `mcp-`)
- macOS: `~/Library/Caches/ms-playwright/` (the folder that starts with `mcp-`)

**Your resume is a Word file.** Save it as PDF and put the PDF in `my-files/`.

## Use it responsibly

LinkedIn's terms limit automated activity. Keep the per-run limits low, check the jobs before you confirm, and read `applications.txt` afterwards. You are responsible for every application sent from your account.

## License

MIT, see [LICENSE](LICENSE). Made by [spacesdrive](https://github.com/spacesdrive).
