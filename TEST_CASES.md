# Grading Console — Test Cases

**Application under test:** `index.html`  
**Test type:** Manual functional, validation, usability, accessibility, and deployment testing  
**Recommended browsers:** Latest Chrome or Microsoft Edge with an internet connection (the Excel reader is loaded from a CDN).

## Test data

Create a workbook named `valid-multi-course.xlsx` with a first worksheet called `Marks` and the following exact data. It makes boundary, analytics, search, and multi-course tests repeatable.

| BITS ID | Course | Total Marks |
|---|---|---:|
| `[unique Course A ID 1]` | Course A | 100 |
| `[unique Course A ID 2]` | Course A | 80 |
| `[unique Course A ID 3]` | Course A | 79 |
| `[unique Course A ID 4]` | Course A | 70 |
| `[unique Course A ID 5]` | Course A | 69 |
| `[unique Course A ID 6]` | Course A | 50 |
| `[unique Course A ID 7]` | Course A | 0 |
| `[unique Course B ID 1]` | Course B | 85 |
| `[unique Course B ID 2]` | Course B | 64 |

For negative tests, make a copy of this workbook and change only the field named in the test case.

## Test execution record

Use this small record with each case during testing.

| Status | Meaning |
|---|---|
| Pass | Actual result matches the expected result. |
| Fail | Actual result differs from the expected result; record the evidence. |
| Blocked | The test cannot run because of an environment or dependency issue. |
| Not run | The test has not yet been executed. |

## 1. Launch and upload

| ID | Scenario | Preconditions / test data | Steps | Expected result | Status / notes |
|---|---|---|---|---|---|
| TC-01 | Initial load | Open `index.html`. | 1. Load the page. | The upload panel is visible; Course is disabled; the empty state is shown; session time reads `00:00`. | |
| TC-02 | Download a blank template | Page loaded and internet connection available. | 1. Expand **Marks file requirements and template**. 2. Select **Download blank Excel template**. | An `.xlsx` file downloads with exactly `BITS ID`, `Course`, and `Total Marks` headings. | |
| TC-03 | Successful `.xlsx` upload | `valid-multi-course.xlsx`. | 1. Upload the workbook. | A success message appears; the browser file control shows the selected filename; Course is enabled and contains Course A and Course B exactly once. | |
| TC-04 | Successful legacy `.xls` upload | Save the valid sheet as `.xls`. | 1. Upload the `.xls` workbook. | The workbook is accepted with the same course and record results as TC-03. | |
| TC-05 | Unsupported file type | A `.csv`, `.txt`, or `.pdf` file. | 1. Upload the file. | A clear error states that an `.xlsx` or `.xls` file is required. No previous grading session remains active. | |
| TC-06 | Required column aliases | Workbook heading uses `Student's BITS ID` (or curly-apostrophe equivalent), `Course`, and `Total Marks`. | 1. Upload the workbook. | The upload succeeds; the accepted BITS ID alias behaves exactly like `BITS ID`. | |
| TC-07 | Missing required heading | Replace `Total Marks` with `Score`. | 1. Upload the workbook. | Upload is rejected with an explanation of the required headings. | |
| TC-08 | Extra non-empty column | Add a fourth heading and values, for example `Email`. | 1. Upload the workbook. | Upload is rejected because exactly three non-empty columns are required. | |
| TC-09 | Empty worksheet | Workbook contains only headings. | 1. Upload the workbook. | Upload is rejected with an “no student records” message. | |
| TC-10 | Missing student data | Make one row's BITS ID blank; repeat with Course blank. | 1. Upload each workbook. | Each upload is rejected and identifies the affected row and field. | |
| TC-11 | Invalid marks | Test one at a time: blank mark, `80.5`, `-1`, `101`, and `abc`. | 1. Upload each workbook. | Each upload is rejected; the message says marks must be whole numbers from 0 to 100. | |
| TC-12 | Duplicate student in one course | Duplicate any Course A student row. | 1. Upload the workbook. | Upload is rejected and identifies the duplicated BITS ID/course combination. | |
| TC-13 | Consecutive uploads replace old data | Upload `valid-multi-course.xlsx`, then upload a valid workbook containing only `Course C`. | 1. Select Course A after the first upload. 2. Upload the second workbook. | The active workspace closes; course search/ranges reset; only Course C appears in the selector—no duplicate or stale courses remain. | |
| TC-14 | Course selection does not require a lookup | Valid workbook uploaded; Student BITS ID field empty. | 1. Select Course A. | The workspace opens normally; the Student BITS ID lookup remains optional. | |

## 2. Course selection, analytics, and roster

| ID | Scenario | Preconditions / test data | Steps | Expected result | Status / notes |
|---|---|---|---|---|---|
| TC-15 | Select a valid course | Complete TC-03. | 1. Select Course A. | The workspace opens, grade cards use default ranges, and the timer starts at `00:00` without waiting a second. | |
| TC-16 | Course A statistics | Course A selected from the prescribed test workbook. | 1. Inspect the marks overview. | Maximum = `100`; Minimum = `0`; Average = `64.00`; Median = `70`. | |
| TC-17 | Default grade distribution | Course A selected with defaults. | 1. Inspect the distribution table. | A=`2`, A-=`2`, B=`1`, B-=`1`, C=`0`, C-=`0`, D=`0`, E=`1`; shares total 100%. | |
| TC-18 | Histogram reflects marks | Course A selected. | 1. Inspect the chart and its description. | A visible histogram is drawn, the mean marker is present, and its accessible description names Course A, 7 marks, and mean 64.00. | |
| TC-19 | Course switching | Course A selected and a BITS ID search entered. | 1. Select Course B. | The search clears; default ranges are rebuilt; Course B shows 2 students; session timer restarts. | |
| TC-20 | Search by full BITS ID | Course A selected. | 1. Search for the unique ID assigned to the Course A student with mark 70. | One row remains, showing mark 70 and grade A-. | |
| TC-21 | Partial and unsuccessful search | Course A selected. | 1. Search `A00`. 2. Search `NO-MATCH`. | Partial search returns all matching records; no-match search displays a clear “No BITS IDs match” row. | |

## 3. Grade policy and validation

| ID | Scenario | Preconditions / test data | Steps | Expected result | Status / notes |
|---|---|---|---|---|---|
| TC-22 | Verify default policy | A course is selected. | 1. Inspect all eight grade cards. | Defaults are A 80–100, A- 70–79, B 60–69, B- 50–59, C 40–49, C- 30–39, D 20–29, E 0–19. Export is enabled. | |
| TC-23 | Minimum-boundary cascade | Course A selected with defaults. | 1. Change A minimum from 80 to 85. | A- maximum changes to 84; lower maximums retain continuous boundaries; the policy remains valid and distribution/roster update. | |
| TC-24 | Detect a gap | Defaults restored. | 1. Change A- maximum from 79 to 78. | A range error says gaps or overlaps are not allowed; export is disabled; distribution counts and roster grades show unavailable values. | |
| TC-25 | Detect an overlap | Defaults restored. | 1. Change B maximum from 69 to 70. | A range error says gaps or overlaps are not allowed and export is disabled. | |
| TC-26 | Protect top coverage | Defaults restored. | 1. Change A maximum from 100 to 99. | Error says A must end at 100; export is disabled. | |
| TC-27 | Protect bottom coverage | Defaults restored. | 1. Change E minimum from 0 to 1. | Error says E must start at 0; export is disabled. | |
| TC-28 | Reject reversed range | Defaults restored. | 1. Change C minimum to 51 while C maximum remains 49. | Error says the minimum cannot be higher than the maximum; export is disabled. | |
| TC-29 | Grade-boundary dropdown limits | Defaults restored. | 1. Open any Min/Max dropdown. | Only whole-number choices from 0 through 100 are available; a fractional or blank boundary cannot be selected through the UI. | |
| TC-30 | Allow a single-mark range | Enter this continuous policy: A 100–100; A- 90–99; B 80–89; B- 70–79; C 60–69; C- 40–59; D 20–39; E 0–19. | 1. Enter all boundaries. | The policy is valid and export enables, confirming that an inclusive one-mark range is allowed. | |
| TC-31 | Restore defaults | Create any invalid custom policy. | 1. Select **Restore defaults**. | All eight defaults return; error clears; export enables; analytics and roster return to default grades. | |

## 4. CSV export

| ID | Scenario | Preconditions / test data | Steps | Expected result | Status / notes |
|---|---|---|---|---|---|
| TC-32 | Export unavailable when policy invalid | Use TC-24, TC-25, TC-26, TC-27, or TC-28. | 1. Inspect/export. | **Finalize & download CSV** is disabled; no file can be downloaded. | |
| TC-33 | Export does not require a Student ID lookup | Select a valid course; leave Student BITS ID blank. | 1. Select **Finalize & download CSV**. | The download starts and includes every student in the selected course. | |
| TC-34 | Valid export contents | Course A selected with defaults. | 1. Download the CSV. 2. Open it in a text editor or spreadsheet. | Filename includes the course; metadata contains course/session time; headers are BITS ID, Total Marks, Grade; 7 Course A records appear with the grade mapping in TC-17. | |
| TC-35 | Export only the selected course | Course B selected. | 1. Download the CSV. | Only the two Course B student IDs appear; no Course A student appears. | |
| TC-36 | CSV quotation and formula safety | Valid workbook with an ID such as `=TEST` or `+TEST`; also test a comma/quote in the course name. | 1. Upload/select/export. 2. Inspect raw CSV. | CSV remains structurally valid: quotes/commas are escaped and formula-leading text is prefixed as literal rather than executing in spreadsheet software. | |
| TC-37 | Repeat export timer behavior | Export a valid course; wait at least 10 seconds; export again. | 1. Compare the displayed session time after both downloads. | The session time remains frozen at the first finalization time and does not keep growing after completion. | |

## 5. Usability, accessibility, and responsive checks

| ID | Scenario | Preconditions / test data | Steps | Expected result | Status / notes |
|---|---|---|---|---|---|
| TC-38 | Keyboard-only workflow | Use a desktop browser; do not use a mouse after loading. | 1. Tab through controls. 2. Upload/select using keyboard. 3. Edit a range and export. | All controls are reachable in a logical order, show a visible focus indicator, and work with Enter/Space where applicable. | |
| TC-39 | Screen-reader labels/live feedback | Use a screen reader if available. | 1. Navigate form controls and trigger an upload or range error. | Controls have understandable labels; upload/range/export messages are announced; chart has a meaningful text description. | |
| TC-40 | Narrow-screen layout | Use browser device emulation at 320 px and 375 px wide. | 1. Open workflow and scroll through all panels. | No horizontal page overflow; cards stack cleanly; inputs/buttons remain usable; roster can scroll within its table container. | |
| TC-41 | Reduced-motion preference | Enable operating-system/browser reduced motion. | 1. Reload and edit ranges. | The application remains usable and does not depend on motion to convey any result. | |
| TC-42 | Cross-browser smoke test | Latest Chrome and Edge. | 1. Repeat TC-01, TC-03, TC-15, TC-22, and TC-34 in each browser. | Core upload, grading, analytics, and CSV export work consistently. | |

## 6. Published-site checks

| ID | Scenario | Preconditions / test data | Steps | Expected result | Status / notes |
|---|---|---|---|---|---|
| TC-43 | Published URL loads | Use the deployed Netlify URL. | 1. Open the link in a private/incognito window. | The app loads over HTTPS and has the same initial state as TC-01. If the anonymous site has not been claimed, enter its temporary password first. | |
| TC-44 | Vendored spreadsheet dependency | Project files include `xlsx.full.min.js`; internet access is disabled. | 1. Open `index.html`. 2. Download the blank template. 3. Upload a valid `.xlsx` workbook. | The local SheetJS 0.20.3 build loads without a CDN request; template generation and workbook import work offline. | |
| TC-45 | Shareability after claim | Netlify site has been claimed and made public, if required by account settings. | 1. Open the URL on another device/network. | The app is available without local files, temporary credentials, or a development server. | |

## Exit criteria

The app is ready to submit when:

- Every critical test (TC-01 to TC-37 and TC-43 to TC-45) passes.
- The vendored spreadsheet library passes TC-44 without a network connection.
- Invalid workbooks never replace a previously valid session with corrupt data.
- A final CSV has been inspected and contains correct grades for every selected-course student.
- The deployed link is claimed and publicly accessible, if a password-free submission link is required.
