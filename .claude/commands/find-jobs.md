---
description: Find fresh LinkedIn jobs that match your resume and save their links
argument-hint: [resume path, optional]
---

Find LinkedIn jobs for the user and save the links. Do the steps in order and give the user a one line update after each step.

Resume path: $ARGUMENTS
If no path was given, use the first PDF, TXT or MD file in `my-files/` that is not a my-details file.

## Step 1: Check setup
- `my-files/my-details.md` must exist. If it does not, copy `my-files/my-details.example.md` to `my-files/my-details.md`, ask the user to fill it in, and stop.
- A resume file must exist. If none is found, ask the user to put their resume (PDF works best) in `my-files/` and stop.
- The playwright browser tools must be available. If they are not, tell the user to run `claude mcp list` and read the Troubleshooting part of README.md, then stop.

## Step 2: Check LinkedIn login
- Open https://www.linkedin.com/feed/ in the playwright browser and take a snapshot.
- If the feed is showing, continue.
- If a login or sign up page is showing, open https://www.linkedin.com/login and tell the user: "Please log in to LinkedIn in the browser window that just opened, then reply done." Wait for the reply, then check again.
- Never ask for, read or type the user's password.

## Step 3: Build the resume profile
Use the `resume-analyzer` agent. Give it the resume path.
It writes `output/resume-profile.md`, or reuses it if it already exists.

## Step 4: Search and collect jobs
Use the `job-search` agent. Give it today's date as YYYY-MM-DD.
It saves new job links to `output/<date>/jobs.txt`.

## Step 5: Report
Tell the user how many new jobs were saved, show up to 10 of them as `title | company | link`, and end with: "Run /apply-jobs to apply to these jobs."
