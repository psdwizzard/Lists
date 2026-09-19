# Dev Blog

## 2026-09-19 — Published lead lists; built out the ITG HubSpot import sheet

**What changed:**
- Created the public repo `psdwizzard/Lists` and pushed the three HubSpot company CSV
  exports (8 / 26 / 162 rows — the smaller two are subsets of the large one).
- Added `ITG_Richmond_Green_Yellow_HubSpot_Import.xlsx` (80 companies, 36 GREEN / 44 YELLOW).
- Extended that sheet with HubSpot-standard address columns: `Street address`, `City`,
  `State/Region`, `Postal code`, `Country/Region`, `Phone number`.
- Filled **41** addresses by parsing the `Office:` lines buried in the big CSV's
  `Description` field, and the remaining **39** by web research (company sites first,
  search as fallback for hosts that returned 403/404). Coverage is now 80/80.
- Added `Address source` (`CSV export` vs `Web research`) so the researched half is
  spot-checkable.
- Added a `Description` column carrying the extra offices for multi-site orgs (29 of 80),
  and rebuilt the `All Office Locations` tab from the merged set — now 155 office rows
  across 52 companies.

**State:**
- All 80 rows have a complete, validated address + phone. No blanks.
- Both tabs are consistent with each other (tab 2 was rebuilt, not appended to).
- Everything committed and pushed to `main`.

**Where we left off:**
The sheet is import-ready *except* for the owner field — see TODO 1. Nothing is blocked.

**Open / TODO:**
1. **`Company owner` is the display name `Lindsay Morris`, which HubSpot will not match.**
   HubSpot resolves owners by *email*, so this column imports as empty. Either swap in her
   HubSpot seat login email (NOT a personal Gmail) or — recommended — leave it and
   bulk-assign in the UI after import (Companies → filter to the new records → Assign owner).
2. **No contact/person data exists anywhere in the source.** The CSV holds only `Industry`,
   `Specialty`, `Office`, `Source`, `Growth evidence` — zero names, emails, or titles.
   Adding contacts requires a new source; do not synthesise them.
3. `ITG Priority` and `Source` are not HubSpot properties — create them as custom company
   properties in the import wizard or skip them. Same for `Address source`.
4. Incomplete multi-site coverage: **Virginia Family Dentistry** lists 17 offices but only
   10 were exposed on its locations page; **Virginia Cancer Institute** names 7 sites but
   publishes an address only for the business office. Both need individual location-page
   fetches (~15 requests) if full coverage matters.
5. Spot-check before import: **Circle Auto Recycling** (Richmond vs Prince George site is
   ambiguous), **New Life for Adults and Youth** (PO Box only — won't geocode),
   **Lakeside Health & Rehabilitation** / **Lakeside Senior Living** (same address, two
   records — confirm they shouldn't merge), **Insight Physicians** (live domain is
   `insightphysicianspc.com`, not the `insightphysicians.com` in the sheet).
6. If any of these companies already exist in HubSpot, this import **overwrites**
   `Description` rather than appending. Dedupe on domain first if the portal isn't empty.
