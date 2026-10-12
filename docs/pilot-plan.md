# Multi-Agent Research Pilot — Working Plan

Status: pre-PRD. Phases 0–1 agreed in principle. Last updated: 2026-10-12.

## Decisions
- Pilot on one country before scaling.
- Three agents for the pilot (not four):
  1. **Lead / extraction agent**: reads the input document, writes the research brief, extracts values from the sources, writes them into Excel.
  2. **Research agent**: searches the given links/domains to find the specific pages and documents that hold each field.
  3. **Review agent**: independent check that each value matches its cited source and is plausible.
- The user will supply one **input document** holding the Excel template to fill and the step-by-step sourcing procedure (which source supplies which field).
- Search is limited to the links the user gives. No open-web search in the pilot.
- Output: Excel.
- The user will supply a ground-truth set to score the pilot.

## Open questions
- Which pilot country?
- Research agent's job: find pages within the given sites (proposed), or also discover beyond them?
- Is the review agent's verdict written into the same workbook (proposed: a Review tab) or a separate file?
- Fix the success thresholds once ground truth is in hand (proposed: recall ≥80%, field accuracy ≥95%, 100% source-linked).

## Phase 0 — Define the test
1. Receive the input document (template + sourcing procedure) and the ground truth.
2. Lock the scope: country, dataset, time window.
3. Turn the sourcing procedure into a field → source → extraction-rule table.
4. Set success criteria.

## Phase 1 — Design
5. Agent contracts:
   - Lead → Research: a list of fields/records to find, each with its permitted links.
   - Research → Lead: candidate pages/documents, each with URL, exact quote and date retrieved.
   - Lead → Excel: one value per cell; each value has a source URL and quote in companion columns.
   - Review → Lead: accept / reject / query per value, with a reason. Max 2 rework loops, then escalate to a human.
6. Rules for conflicts, gaps and duplicates: a blank with a reason beats a guess. Conflicting sources are logged, not silently resolved.
7. Workbook layout: Data tab, Sources tab, Review tab, Run log tab.
8. Tooling: to be decided in the PRD.

## Pilot run 1 — Italy (2026-10-12)
Inputs: `Italy.docx` (sourcing procedure) and `Italy.xlsx` (template, 2021–23 filled, 2024–26 to fill).

**Blocked:** the session's network policy denies `download.terna.it`. All five source PDFs are on that host. No values extracted.

Field → source map (from Italy.docx):
| Table | 2024 | 2025 | 2026 |
|---|---|---|---|
| Installed capacity (MW) | Monthly Report Dec-24 | Annual Report 2025 ("Page 35"; link points to #page=165) | Monthly Report Jun-26: H1 additions Wind 328, Solar 3,093, Hydro 5 (pp. 14–15) |
| Generation & consumption (GWh) | Monthly Report Dec-24 | Monthly Report Dec-25 | Monthly Report Jun-26 (H1 only) |
| Line length (km) | Annual Report 2024 | Annual Report 2025 | = 2025 |
| Transformer capacity (MVA) | Annual Report 2024 | Annual Report 2025 | = 2025 |
| Substations (No.) | Annual Report 2024 | Annual Report 2025 | = 2025 |

Template issues to resolve:
1. Growth-rate rows mix units: rows 7/16/20 and 2021–23 use percent (0.2 = 0.2%), but E39 = 0.0013 and F51 = 0.0233 look like fractions. Growth rates for 2024–26 (E39:G39, E51:G51) are pre-filled without base values.
2. 2025 capacity source: the doc says "Page 35", but the link points to page 165.
3. 2026 capacity: the doc gives H1 additions for wind, solar and hydro only. Rule needed: is 2026 = 2025 + additions? What happens to thermoelectric?
4. Category mapping: Terna reports wind, solar, geothermal and bioenergy separately. The template puts geothermal and bioenergy under Thermoelectrics, and only solar + wind under Renewables.
