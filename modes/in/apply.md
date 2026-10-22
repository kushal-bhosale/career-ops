# Mode: apply — Live Application Assistant (India market)

> Apply `voice-dna.md` (if present) to free-text answers and cover-letter fields — full guardrail, conversational voice included (Tier 1 + 2). See `_writing.md` → Voice DNA.

Interactive mode for when the candidate is filling out an Indian application
form (Naukri, IIM Jobs, LinkedIn Easy Apply, Instahyre, Cutshort, Wellfound,
or a company ATS such as Lever, Greenhouse, Ashby, Darwinbox, Keka, Zoho
Recruit, Freshteam). Reads what is on the screen, loads the previous
evaluation context, and generates answers for each form question.

## Base workflow

Follow `modes/apply.md` verbatim, including its Scope section. Run
`node apply-scope.mjs root` and `node apply-scope.mjs launch-options`. Do not
read `DevToolsActivePort` or attach to an existing browser. **Do not submit
the form.** On LinkedIn and Naukri, do not drive the Apply / Easy Apply flow
with automation at all: generate the answers for the candidate to paste, and
let the candidate click. The steps below are India-specific field guidance
for the GENERATE step.

## Which CV to upload

Use the CV variant recorded in the matched report (`**CV variant:**`). If the
report has none, run the routing in `modes/in/_shared.md` and say which file
you picked and why. Point the candidate to the tailored PDF in `output/`
when one exists.

## India-specific fields

- **Current CTC / Expected CTC:** from `config/profile.yml`
  (`compensation.current_ctc_lpa`, `compensation.expected_ctc_lpa`), in the
  unit the form asks (₹ LPA, ₹/year, or ₹/month; convert explicitly and show
  the conversion in Notes). Missing → `Ask candidate`, never a guess. A
  "fixed + variable" split field takes the split from the profile only.
- **Notice period:** pick the dropdown option closest to
  `cover_letter.notice_period_days` without understating it (portals bucket
  it as days/months, plus a "serving notice" option). If
  `india.notice_status` is `serving`, give the last working day from the
  profile.
- **Total experience (years/months):** `india.total_experience_years`.
  **Relevant experience:** `india.relevant_experience_years`. Never compute.
- **Current location / Preferred location / Relocation:** from `location`,
  `india.preferred_cities`, `india.open_to_relocate`.
- **Highest qualification / College / Year:** from `cv.md` Education, exactly
  as written there.
- **Key skills (Naukri tag field):** only skills present in `cv.md` or the
  routed variant's skills section.
- **IIM Jobs screening questions** are often free text ("Describe a launch you
  led"). Answer from CV bullets, cite the bullet in Notes, keep to the limit.
- **"How did you hear about us?":** the candidate's actual source.
- **Diversity / disability / gender fields:** never auto-answer.
- **Cover letter / "Why this role":** benefit to the employer in the first
  line, one concrete proof point from the CV, under 150 words unless the
  form asks for more.

## Output format

Follow `modes/apply.md`'s output format verbatim and add the India header line:

```text
## Responses for [Company] — [Role]

Based on: Report #NNN | Score: X.X/5 | Archetype: [type] | Market: India | CV: [variant] | Portal: [Naukri|IIM Jobs|LinkedIn|ATS name]

---

### 1. [Exact form question]
> [Response ready for copy-paste, or "Ask candidate: ..." if the field needs confirmation]
Length: [used/allowed characters or words, or "limit unknown"]

Repeat the response and length lines for every remaining question.

---

Notes:
- [CTC/notice values used and their profile keys]
- [Customization points the candidate should double-check]
- Final step: review, then press Submit yourself.
```

## Application Answers

Persist exactly as `modes/apply.md` specifies: update the matched report with
an additive `## Application Answers` section; on confirmed submission,
refresh it from `filled` to `submitted`.

## After applying

Only when the candidate confirms they submitted, follow `modes/apply.md` →
Step 9 (Post-apply): status via `set-status.mjs`, the follow-up seed via
`followup-seed.mjs`, the `## Application Answers` refresh, and the
`contacto` suggestion (a LinkedIn note to the hiring manager or founder is
common and effective at Indian startups).
