# Bug Fix Log

| # | Bug / Issue Identified | How You Reproduced It | Root Cause | Fix Implemented | How You Tested the Fix |
|---|---|---|---|---|---|
| 1 | Courses duplicated or became stale after another workbook was uploaded. | Upload a workbook, then upload the same or a different workbook. | The original code appended options to the existing list and did not clear the active course state. | A successful upload now atomically replaces records, resets the course selector and grading view, then adds a sorted unique course list. | Loaded two different workbooks consecutively and confirmed the selector contains only courses from the latest file, once each. |
| 2 | A blank or invalid course caused incorrect analytics (`undefined`, `NaN`) and an empty-chart failure path. | Select a course with no valid records, or load malformed marks. | Statistics were calculated on an empty array; values were assumed to be numbers. | Workbook rows are validated before being accepted, and analytics show an explicit empty state rather than calculating empty-array values. | Checked empty data and malformed workbook scenarios; the UI reports a useful error and no invalid statistics appear. |
| 3 | Minimum and maximum labels were reversed. | Load a course and compare the smallest/largest mark against the cards. | The HTML placed `id="max"` under “Min” and `id="min"` under “Max”. | Rebuilt the statistics cards with explicit `minimum` and `average` element IDs. | Used marks 42, 65, and 88; the minimum displays 42 and the average displays 65.00. |
| 4 | Valid one-mark grade ranges were rejected. | Set a grade to a single integer range, for example A = 100–100, while keeping all ranges continuous. | Validation used `min >= max`, although inclusive ranges may validly have equal bounds. | Validation now rejects only `min > max`; it also verifies full 0–100 coverage and continuity. | Tested a continuous policy containing 100–100, verified export enables; tested a gap, overlap, and uncovered 0/100, verified export disables. |
| 5 | Marks could be left ungraded despite the range checker passing. | Change A's maximum below 100 or E's minimum above 0 while preserving adjacent boundaries. | The old checker only compared each grade to the one above it. | Added policy-end validation: A must end at 100 and E must begin at 0. | Set A maximum to 99 and E minimum to 1 separately; each produces a clear error and prevents export. |
| 6 | Upload guidance said `.xlsx`, while the file picker accepted only `.xls`; files and columns were not fully validated. | Attempt to select an `.xlsx`, a workbook with extra columns, or non-integer/out-of-range marks. | File accept rules and parser trusted the first sheet without validating its schema and values. | The picker accepts `.xlsx` and `.xls`; the importer requires the three prescribed columns, unique course/ID records, and integer marks 0–100 before replacing data. | Tested both extensions and invalid headers, duplicate IDs, blank values, decimals, -1, and 101. Invalid files leave no stale grading session. |
| 7 | The timer stayed at 00:00 until its first interval and showed inconsistent elapsed time after repeated downloads. | Open the app or download, modify a range, and download again. | The display was updated only in `setInterval`; download stopped the interval but later elapsed time continued to be calculated. | Timer updates immediately when a course is selected and is frozen consistently at the first finalized export. | Selected a course and verified an immediate 00:00 display; exported, waited, and verified the completion time remains stable. |
| 8 | CSV values could break columns or be interpreted as spreadsheet formulas. | Use an ID or course containing a quote, comma, or a leading `=`, `+`, `-`, or `@`. | Export concatenated raw strings directly into CSV. | CSV cells are quoted and escaped, and formula-leading text is made literal before download. Blob URLs are also released after use. | Inspected exported CSV for comma/quote escaping and formula-safe values; opened it in a spreadsheet to verify columns remain intact. |
| 9 | The pinned spreadsheet parser has a known vulnerability when processing a crafted workbook (open security issue). | Review the loaded SheetJS version and the advisory for CVE-2023-30533. | The app loads `xlsx@0.18.5`; versions before 0.19.3 are listed as affected by prototype pollution. | Not fixed yet. Upgrade to a maintained, patched spreadsheet library and verify workbook compatibility before processing untrusted files. | Advisory checked against the pinned version; no patched upgrade or remediation test has been completed. |

## Enhancements added

1. A validated upload workflow with clear errors, accepted header alias for “Student's BITS ID”, and a downloadable Excel template.
2. A responsive, accessible analytics view: correct statistics, a high-DPI histogram, live grade counts and percentages, and clear screen-reader descriptions.
3. A live student-review table with synchronized Student BITS ID search, so graders can spot-check individual grades before export.
4. Safer grade-range controls that show the active range, preserve lower adjacent maxima when a minimum moves, and clearly explain why export is unavailable.
5. A mobile-friendly layout, visible keyboard focus, semantic labels/live messages, and an export filename based on the selected course.
6. A README with workbook requirements, usage steps, the deployment URL, its current password-gate status, and the open spreadsheet-library security advisory.

## Manual browser smoke test

1. Open `index.html` in a browser with internet access (the Excel reader is loaded from a CDN).
2. Download the blank template, add a few valid rows in one or more courses, and upload it.
3. Optionally enter a Student BITS ID, choose a course, verify the overview/statistics/roster, modify ranges, restore defaults, then download CSV.
4. Repeat with the invalid cases listed in the log to verify the application keeps the previous session cleared and explains the problem.

## Deployment

The app is static and is published at [https://beautiful-dango-a9b1c2.netlify.app/](https://beautiful-dango-a9b1c2.netlify.app/). The current Netlify deployment displays a password prompt; access requires the site password from its owner. See `README.md` for usage instructions. The deployed app still loads the vulnerable `xlsx@0.18.5` dependency noted in issue 9; avoid using it with untrusted workbooks until that dependency is upgraded.
