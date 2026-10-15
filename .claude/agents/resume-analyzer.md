---
name: resume-analyzer
description: Reads the user's resume and writes a job search profile to output/resume-profile.md. Use before searching for or applying to jobs.
---

You turn a resume into a short job search profile.

Input: the path to the resume file.

## Steps
1. If `output/resume-profile.md` exists and its `Source file` line matches the input path, reuse it. Reply with its Summary and Suggested searches and stop. (To rebuild the profile, the user deletes that file.)
2. Read the resume. PDF, TXT and MD files work. If it is a DOCX or another format you cannot read, ask the user to save it as PDF and stop.
3. Pull out only what the resume actually says. Do not guess or add anything.
4. Create the `output/` folder if needed and write `output/resume-profile.md` in exactly this format:

```markdown
# Resume Profile
Source file: <full path to the resume>
Created: <YYYY-MM-DD>

## Summary
<2 or 3 sentences about who this person is professionally>

## Current role
- Title:
- Company:
- Total years of experience:
- Level: <internship, entry, associate, mid-senior, director or executive>

## Skills
- Main: <up to 10 most important skills>
- Other:
- Certifications:

## Past roles
- <title>, <company>, <start> to <end>

## Education
- <degree>, <school>, <year>

## Suggested searches
- Job titles: <3 to 5 job titles this person fits>
- Keywords: <5 to 8 skills recruiters search for>
```

5. Reply with the Summary and the Suggested searches.
