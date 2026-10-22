# career-ops -- India market mode (`modes/in/`)

English-language modes with India market rules, written for **B2B marketing
candidates**: founding marketer, head of marketing, product marketing, demand
generation, corporate communications, events, content.

`modes/hi/` is the Hindi-language India mode for engineering roles. Use
`modes/in/` when you want English output, marketing archetypes, CV variants,
and Indian portal handling (Naukri, IIM Jobs, LinkedIn, Instahyre, Cutshort,
Wellfound, foundit).

## What it adds

| File | Based on | Role |
|------|----------|------|
| `_shared.md` | `modes/_shared.md` | Marketing archetypes, CV variant routing, fit thresholds, CTC / notice / ESOP rules, scam signals |
| `offer.md` | `modes/oferta.md` | Full A-H evaluation with India overrides (Block A, D, G, H, report header) |
| `apply.md` | `modes/apply.md` | Form assistant for Naukri / IIM Jobs / LinkedIn / Indian ATSs. Never submits |
| `pipeline.md` | `modes/pipeline.md` | URL inbox with Indian portal notes and a "Ready to apply" queue |

Plus `templates/portals.india-marketing.example.yml` (scanner config).

## Setup

1. **Activate the mode** in `config/profile.yml`:

   ```yaml
   language:
     output: en
     modes_dir: modes/in
   ```

2. **CVs.** Put your base CV in `cv.md`. Put role-specific versions in
   `cvs/` and register them:

   ```yaml
   cv:
     variants:
       generalist: cv.md
       pmm: cvs/pmm.md
       comms: cvs/comms.md
       events: cvs/events.md
     variant_routing:
       "Founding / Head of Marketing": generalist
       "Demand Generation / Growth": generalist
       "Content / Thought Leadership": generalist
       "Product Marketing": pmm
       "Corporate Communications / PR / Brand": comms
       "Events / Field Marketing": events
   ```

3. **India keys** in `config/profile.yml`:

   ```yaml
   compensation:
     currency: "INR"
     target_range: "₹X-Y LPA fixed"
     minimum: "₹Z LPA fixed"
     current_ctc_lpa: null     # fill in; forms ask for it
     expected_ctc_lpa: null    # fill in; forms ask for it
   cover_letter:
     notice_period_days: 60
   india:
     notice_status: "not serving"   # serving | not serving | negotiable
     total_experience_years: null
     relevant_experience_years: null
     preferred_cities: ["Mumbai", "Pune", "Bengaluru"]
     open_to_relocate: true
   ```

   Any key left `null` makes the apply mode ask you instead of guessing.

4. **Scanner.** `cp templates/portals.india-marketing.example.yml portals.yml`,
   then grow `tracked_companies` from a company list:

   ```bash
   node discover-ats.mjs --in config/india-companies.yml --summary   # preview
   node discover-ats.mjs --in config/india-companies.yml --write     # add hits
   npm run verify:portals
   ```

   `config/india-companies.yml` is `companies: [{name: ...}, ...]`.

5. **Job-alert emails (Naukri, IIM Jobs, LinkedIn, Instahyre).** These portals
   block scrapers, and their terms forbid automated access. Their job-alert
   emails are the clean way in:
   - Create alerts on each portal for your target titles and cities.
   - In Gmail, add a filter: `from:(naukri.com OR iimjobs.com OR linkedin.com OR instahyre.com) subject:(job OR jobs OR alert)` → apply label `Job Leads`.
   - Configure the gmail plugin (`plugins/gmail/skill.md`) with that label,
     and run `node plugins.mjs run gmail`.
   - Check the first run by hand: alert emails carry footer and tracking
     links too, and the link formats of each portal change. Delete
     non-job lines from `data/pipeline.md` if any slip through.

## Daily run

In your AI CLI, from the repo root:

1. `scan` — runs `search_queries` and `tracked_companies`.
2. `node plugins.mjs run gmail` — pulls job-alert links.
3. `pipeline` — liveness → triage → full evaluation → report, tracker row,
   tailored CV PDF for each role at or above `auto_pdf_score_threshold`, and
   a "Ready to apply" list.
4. For each role you pick: `apply` — fills answers from your profile and
   the routed CV. **You review and press Submit.** Then confirm, and the
   tracker moves to Applied.

The tracker is `data/applications.md` (`node tracker.mjs` to browse,
`npm run dashboard` if you built the Go TUI).

## Why the final Submit stays with you

career-ops never submits applications (see `ARCHITECTURE.md`,
"Human-in-the-loop"). On LinkedIn and Naukri, automated applying also breaks
the portal's user agreement and can get your account restricted, which costs
far more than the click it saves. Everything up to that click is automated.

## Reference lexicon

| Term | Meaning |
|------|---------|
| CTC | Cost to Company: fixed + variable + employer PF + gratuity + benefits (+ ESOP value at some startups) |
| LPA | Lakhs per annum. 1 lakh = ₹1,00,000; 1 crore = 100 lakh |
| In-hand | Monthly take-home after tax, employee PF, professional tax |
| Notice period | Contractual exit time, commonly 30-90 days; buyout sometimes possible |
| ESOP | Employee stock options; usually 4-year vesting, 1-year cliff |
| BGV | Background verification |
| WFO | Work from office |
