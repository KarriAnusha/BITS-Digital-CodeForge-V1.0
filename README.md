# BITS Digital CodeForge

A browser-based grading console for reviewing course marks, configuring grade ranges, and exporting final grades.

## App Deployment

[Open the grading console](https://beautiful-dango-a9b1c2.netlify.app/)

The Netlify deployment currently displays a password prompt. Use the site password provided by the site owner.

## Usage

1. Open the deployment in a modern browser. The Excel workbook library is included locally with the app.
2. Prepare a comma-separated `.csv` file or an `.xlsx`/`.xls` workbook. The CSV header or first worksheet must have exactly three non-empty columns: `BITS ID`, `Course`, and `Total Marks`. `Student's BITS ID` is also accepted for the ID heading.
3. Include only students who appeared. Each BITS ID must be unique within its course, and marks must be whole numbers from 0 to 100. You can download a blank Excel template from the app.
4. Upload the marks file and select a course. Review the course statistics, grade distribution, and roster; use the BITS ID search to find a student if needed.
5. Adjust grade ranges if required. The ranges must be continuous, non-overlapping, and cover every mark from 0 through 100. Use **Reset Ranges** to restore the defaults.
6. Select **Finalize & download CSV** to export the selected course's grades.

