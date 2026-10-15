---
name: job-search
description: Searches LinkedIn Jobs in the playwright browser and saves matching job links to output/<date>/jobs.txt. Needs a logged in LinkedIn session.
---

You search LinkedIn Jobs and save the links of matching jobs.

Input: today's date as YYYY-MM-DD.
Read first: `my-files/my-details.md` (the Job search section) and `output/resume-profile.md`.

## 1. Decide what to search
- Job titles: "Job titles to search" from my-details. If blank, use "Job titles" under Suggested searches in the resume profile.
- Locations: "Locations" from my-details. If blank, search without a location.
- Filters: work type, experience level, posted within and Easy Apply only, all from my-details.
- Limit: "Max jobs to collect per run" from my-details (25 if blank).

## 2. Build the search links
Do not click LinkedIn's filter buttons. Open a search link with the filters in it. This is faster and keeps working when LinkedIn changes its layout.

```
https://www.linkedin.com/jobs/search/?keywords=<title>&location=<location>&f_TPR=<posted>&f_WT=<work type>&f_E=<level>&f_AL=true&sortBy=DD
```

| Setting | Parameter | Values |
| --- | --- | --- |
| Posted within | f_TPR | 24 hours = r86400, week = r604800, month = r2592000 |
| Work type | f_WT | on-site = 1, remote = 2, hybrid = 3 |
| Experience level | f_E | internship = 1, entry = 2, associate = 3, mid-senior = 4, director = 5, executive = 6 |
| Easy Apply only | f_AL | true (leave it out when the setting is no) |
| Next page | start | 25, 50, 75 and so on |

- Encode spaces as %20 and join several values with %2C, for example `f_WT=2%2C3`.
- Leave out any parameter whose setting is blank or "any".
- If the location is "Remote", leave out `location` and use `f_WT=2`.

## 3. Collect jobs
For each job title and location:
1. Open the search link and wait 3 seconds.
2. If there are no results and the time filter is 24 hours, try once more with `f_TPR=r604800`.
3. Run this with browser_evaluate. It scrolls the job list so every card loads, then returns the jobs on the page:

```js
async () => {
  const first = document.querySelector('[data-occludable-job-id], [data-job-id]');
  let list = first;
  while (list && list.scrollHeight <= list.clientHeight + 10) list = list.parentElement;
  for (let i = 0; i < 12; i++) {
    (list || window).scrollBy(0, 600);
    await new Promise(r => setTimeout(r, 400));
  }
  const jobs = new Map();
  document.querySelectorAll('[data-occludable-job-id], [data-job-id]').forEach(card => {
    const id = card.getAttribute('data-occludable-job-id') || card.getAttribute('data-job-id');
    const text = card.innerText.split('\n').map(s => s.trim()).filter(Boolean);
    if (id && /^\d+$/.test(id)) jobs.set(id, { title: text[0] || '', company: text[1] || '' });
  });
  document.querySelectorAll('a[href*="/jobs/view/"]').forEach(a => {
    const id = (a.href.match(/\/jobs\/view\/(\d+)/) || [])[1];
    if (id && !jobs.has(id)) jobs.set(id, { title: a.innerText.trim(), company: '' });
  });
  return [...jobs].map(([id, j]) => ({ url: `https://www.linkedin.com/jobs/view/${id}/`, ...j }));
}
```

4. If that returns nothing, take a snapshot and read the job links from it instead.
5. Drop a job when:
   - its title contains any of the "Words to exclude" from my-details,
   - its link is already in `output/<date>/jobs.txt`, or
   - its link appears in any `output/*/applications.txt`.
6. Go to the next page (`start=25`, then `50`, and so on) until you reach the limit or a page adds no new jobs.
7. Wait 2 to 3 seconds between page loads.

## 4. Save
Create `output/<date>/` if needed and append the new links to `output/<date>/jobs.txt`, one link per line, with nothing else on the line.

## 5. Reply
Reply with the number of new jobs saved, the search links you used, and a list of the new jobs as `title | company | link`.
