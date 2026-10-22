# Mode: pipeline — URL Inbox (India market)

Processes posting URLs accumulated in `data/pipeline.md`. The candidate adds
URLs whenever (or the scan and job-alert ingest add them), then runs the
pipeline to process them in one batch.

## Base workflow

Follow `modes/pipeline.md` verbatim. Find pending items with the bounded
search `grep -n -m 20 '^- \[ \] ' data/pipeline.md`, batch after batch until
none remain → reserve report numbers → extract each JD (Playwright →
WebFetch → WebSearch) → triage → full A-H evaluation via `modes/in/offer.md`
→ report + tracker → move to Processed → summary table. The notes below are
India-specific.

- **Optional — CLI extractor (`scan.extractor: cli` in `config/profile.yml`):**
  run `node browser-extract.mjs <url>` (`--mode jd`) instead — compact
  `{ "url", "title", "text" }`, fewer tokens (portal-dependent). **Fall back
  silently** to Playwright / WebFetch on error or absence.

## Ready-to-apply queue

After the summary table, list every role scoring at or above
`auto_pdf_score_threshold` as a numbered "Ready to apply" list: company,
role, score, CV variant, employer-confirmed yes/no, and the apply URL. This
is a queue for the candidate. Do not open the apply flows yourself.

## India portal notes

- **Naukri** (naukri.com): highest volume; many listings are posted by
  placement consultancies — treat undisclosed employers as agency-mediated.
  Full JDs usually render without login; the apply flow needs login. Job
  alert emails are the best feed (see `modes/in/README.md`).
- **IIM Jobs** (iimjobs.com): management/marketing roles, many with a CTC
  range; recruiter names are often shown. Detail pages may need login → if
  blocked, mark `- [!]` and ask the candidate to paste the JD.
- **LinkedIn** (linkedin.com/jobs/view/…): aggregator-style listing. Confirm
  at the employer's careers page before evaluating. Never automate Easy Apply.
- **Instahyre / Cutshort / Wellfound / foundit:** startup-heavy; verify the
  company and funding claims before weighting them.
- **Company ATS** (Lever, Greenhouse incl. `job-boards.eu.greenhouse.io`,
  Ashby, Darwinbox, Keka, Zoho Recruit, Freshteam): the employer's own
  posting — preferred as the canonical URL.

## JD extraction quirks

- **Darwinbox / Keka / Zoho Recruit:** SPAs — use Playwright
  (`browser_navigate` + `browser_snapshot`); WebFetch returns a shell.
- **Naukri:** heavy client rendering; Playwright first. A "job not found"
  page means expired.
- **PDF postings:** read directly with the Read tool, never WebFetch.

## Pipeline format

Same as `modes/pipeline.md`:

```markdown
## Pending
- [ ] https://www.iimjobs.com/j/example-123
- [ ] https://jobs.lever.co/company/abc | Company | Head of Marketing
- [!] https://www.naukri.com/job-listings-x -- Error: login required

## Processed
- [x] #014 | https://jobs.lever.co/company/abc | Company | Head of Marketing | 4.2/5 | PDF yes
```
