# Lists — Richmond, VA lead data for HubSpot

## What this is
A **data repo**, not an application. It holds Richmond-area B2B lead lists (mostly
healthcare practices) and a prepared HubSpot import sheet. There is no app, no launcher,
no build step — nothing to run.

**⚠️ This repo is PUBLIC** (`github.com/psdwizzard/Lists`). Everything committed here is
crawlable and cacheable. Business names, domains, office addresses and main-line phone
numbers are fine — they're published on the companies' own sites. **Do not commit personal
email addresses, individual contacts, or anything from a private CRM field.** A personal
Gmail for the record owner was deliberately kept out of the sheet for this reason.

## Files
| File | What it is |
|---|---|
| `ITG_Richmond_Green_Yellow_HubSpot_Import.xlsx` | The deliverable. 80 companies, import-ready. |
| `leadme-hubspot-companies-2026-09-21 (3).csv` | Source export, 162 companies. The useful one. |
| `leadme-hubspot-companies-2026-09-21 (1).csv` / `(2).csv` | 8 and 26 rows — subsets of (3). |
| `docs/devblog.md` | Session log. Read this first to see where things stand. |

## The xlsx, in detail
**Tab 1 `HubSpot Import`** — one row per company, 13 columns:
`Company name`, `Company domain name`, `Company owner`, `ITG Priority`, `Source`,
`Street address`, `City`, `State/Region`, `Postal code`, `Country/Region`, `Phone number`,
`Address source`, `Description`.

- The six address columns and `Description` use **exact HubSpot property labels**, so the
  import wizard auto-maps them. Do not rename them.
- `ITG Priority`, `Source`, `Address source` are **not** HubSpot properties — create them
  as custom company properties or skip them during mapping.
- `Description` holds the *additional* office locations for multi-site orgs (29 of 80);
  the primary office lives in the address columns.

**Tab 2 `All Office Locations`** — 155 office rows across 52 companies. Reference only,
not for import. A HubSpot company record holds exactly one address, which is why
multi-site practices need this tab plus the `Description` column.

## How the address data was produced
Two sources, tracked per-row in `Address source`:
1. **`CSV export` (41 rows)** — parsed out of the big CSV's `Description` field, which
   embeds locations as `Office: <street>, <city>, <ST> <ZIP>; <label>; <phone>` lines.
2. **`Web research` (39 rows)** — fetched from each company's own site
   (`/locations/`, `/contact/`), falling back to search when the host blocked the fetch.

## Gotchas
- **The CSV's `Description` field contains literal newlines inside quoted fields.** Line-based
  tools (`sed`, `awk`, `grep -c` on rows) will corrupt or miscount it. Always use a real CSV
  parser (`python3 -c "import csv"` with `encoding='utf-8-sig'` — the files have a BOM).
- **HubSpot matches record owners by email, never by display name.** `Company owner` is
  currently the string `Lindsay Morris` and will import as empty. See devblog TODO 1.
- **There is no person/contact data in any source file.** Only `Industry`, `Specialty`,
  `Office`, `Source`, `Growth evidence`. Don't invent contacts to fill the gap.
- **Only 55 of the 80 xlsx companies appear in the CSV at all** (51 by domain + 4 by name);
  the other 25 came from elsewhere. Match on domain first, then normalised name.
- Address formats in the source vary a lot: comma-before-ZIP (`Richmond, VA, 23226`),
  spelled-out `Virginia`, and city glued to street with no comma. A strict regex gets ~20%.
  Some rows are literally `Address not verified ... Not specified` and should stay blank.
- Re-importing **overwrites** `Description` on existing records rather than appending.

## Tooling note
`openpyxl` was used to read/write the xlsx from a throwaway venv in the session scratchpad,
not in this repo — keep it that way, no `.venv` here. `.gitignore` covers `.claude/` and
`.DS_Store`.

## Current state
Complete and pushed. All 80 rows have a validated address and phone; both tabs are
consistent. The sheet is import-ready apart from the owner column. Open items — owner
resolution, absent contact data, two orgs with partial multi-site coverage, and four rows
worth eyeballing — are listed in `docs/devblog.md`.
