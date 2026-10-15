---
description: Apply to the LinkedIn jobs saved by /find-jobs
argument-hint: [date YYYY-MM-DD, optional]
---

Apply to saved LinkedIn jobs. Do the steps in order and give the user a one line update after each job.

Date: $ARGUMENTS
If no date was given, use the newest folder in `output/` that has a `jobs.txt`.

## Step 1: Check setup
- `output/<date>/jobs.txt` must exist. If it does not, tell the user to run /find-jobs first and stop.
- `my-files/my-details.md` and `output/resume-profile.md` must exist. If one is missing, tell the user to run /find-jobs first and stop.

## Step 2: Pick the jobs
- Read the links in `output/<date>/jobs.txt`.
- Remove every link that already appears in any `output/*/applications.txt`.
- Keep the first N links, where N is "Max applications per run" in `my-files/my-details.md` (10 if blank).
- If no links are left, tell the user there is nothing new to apply to and stop.

## Step 3: Ask once before applying
Show the list and ask: "Apply to these N jobs? (yes or no)". Continue only if the user says yes.

## Step 4: Check LinkedIn login
- Open https://www.linkedin.com/feed/ in the playwright browser and take a snapshot.
- If a login page is showing, open https://www.linkedin.com/login and ask the user to log in in that window and reply done. Wait, then check again.
- Never ask for, read or type the user's password.

## Step 5: Apply
For each link, one at a time, use the `job-applicator` agent. Give it the job link, the full path of the resume file named in `output/resume-profile.md`, and the date.
After each job, show the user the line the agent logged.

## Step 6: Report
- Show how many jobs were APPLIED, SKIPPED, NEEDS_INFO and FAILED.
- List every NEEDS_INFO question and tell the user: "Add these answers under Extra answers in my-files/my-details.md, then run /apply-jobs again."
