# MovieProject
First ReactJS Project

<https://clientide.com/CPP?mode=test_taker#XVNNj9MwEP0rI3PZlaJsg1QWCtoDSIgDSHtDiKLIcaaNqWNHttNQqv53np1Ui9pLk3nz3rz5yFko17LYnMWn52exEQ8P372OTJJ2o1VRO0tKGsMt7Zwr6bNzFOWBA2lLcXL4i7xnHwowpG2pKWjq2CcBHchwCBQ7aakp6Ys8clKhwYNFUCU79g3IUGk4TswWtFkmsZADoVYfddCNYWpOtCbn6ZHgylkmoy0XFHiQXkZYRIJyfS9DSd9gksIIfsx2YMbCupcapP2cRnIX2acEMjLExU1q0hP_kf1goK53dHIjTUkkujyM3MSkYwffwxgDVdlztSqylhsjojS50cAS07qgxwJoSV9RxZyKG5FqtVplgdd4KOkHqoUuk501p2Va10mlvLnHYkZSN42X6sAwkrvMo3UTtyV9vAIKK5AmuAzDU6YiY2u39pW2yowt0wftQvQs-6cUTlV7qe3d_XlrCb8UCFH6WBDb9v0cDLHdbBSO4elpBtPDC4wm714494hexKUQv4Oz9RH94L5wdG_eNu-UKETkEEO6Rdwk-qob49QhBUDRrl7gn2dhZY-bFRU4LQfl9RBnJQR2WHHdwez8CoM6IdV2a6vVHMCGEFkXjwV2ttSt8y7r5dbEJvqRF2Re6C00ONSAnaqERtQ9Z9U1nO6t81wrOegojf4rZ28zCwPWQz11-MbCIBXXDe9SNvZa53O8Ji4qjZH2UKdDDzcIlvyfzhXsZVRd3edvWmDkoU43Ji6_Lpd_>


https://clientide.com/CPP?mode=test_taker#jVI9T8MwEP0rllkAVagLCErFwsLIgMTQVNbFuSRHnXNkO61ElP_OJWlFgYUssfw-7t6Te219gXrV6-fXV73SF8TWdQWqNfmYAkLzlHHGxEk1QHwJobILZWsI10rO-832qs9YyZfxoIeF_oiezR5DJM_id3efP1i90AljiuMYGUdcmdx5uxsvRELeHOFNrxkaWUe_oHNevfvgClEXGG2gNs2eM3YYMdUGXwVohFMCOVPLosJ4k_9OQe67pCI1rcMzYkyywT-iTsGEvFrZ0We9Vpmuv0dneryacOTCPWYcMHWB1XJsYhojsj9RxqSGuO2S2VOk3EnaElzEIySaH1gKnUCtl7WkoOXNUnjU4GR9K91V7AMaCy0lcPQJc0ezSlJRaw41iXMLFk2O5cgGLgyUCcOJeHTJHfDOOGKMvxBw7sznBDaQbG2a6QFpKSGaA6VaD9th-AI

Frontend
The frontend is a React app built with Vite. It follows a Pages and Components structure where pages are just wrappers and all the real logic lives inside the components.

Splash Screen
I built the home splash screen that shows up after logging in. It shows the Fraud Reissue Automation Tool title and the welcome message. At the start of the internship we did not have Bitbucket access yet so me and Joel were both working on this locally at the same time. Once we got access I merged our two versions together.

File: src/Components/LoggedBackground/LoggedBackground.jsx

Upload CSV Page
The Upload CSV page (src/Pages/UploadPage.jsx) puts together three components: UploadCSV, FileActivity, and Footer. My main contribution here was in FileActivity.

FileActivity.jsx is the Past Activity table that shows all previously uploaded files. I added the Count column to it which shows how many records were in each uploaded CSV. I also built out the sortable column headers so you can sort by Upload Date, Outcome, User, or Count.

Each row in the table is its own component called ActivityTableDynamic (src/Components/FileActivity/ActivityTableDynamic.jsx). It takes in props for the file name, size, upload date and time, outcome, the uploader's name and masked ID, and the record count.

Batch Reports Page
This was the biggest thing I worked on in the frontend. The Batch Reports page lets you pick a date range, hit Go, and see weekly reissue reports grouped by week. Each weekly report is expandable and shows a breakdown of every CSV file processed that week.

Files: src/Pages/Batch.jsx, src/Components/BatchReports/BatchReportHeader.jsx, src/Components/BatchReports/BatchReports.jsx, src/api/fetchDataApi.js

Inside BatchReports.jsx I tracked the from and to date inputs, the list of weeks from the backend, a search term for filtering, a loading flag to disable the Go button while the request is running, and an array of which weekly report rows are expanded. I stored expanded as an array so multiple rows can be open at the same time.

When you hit Go it calls loadReports() which hits the API and saves the response into state. There is also a filteredWeeks value using useMemo that filters the list based on whatever you typed in the search bar. It searches across the period dates, week labels, and individual file names.

The weekly reports render as collapsible cards. Each one has a summary row with the week totals and then per-file rows underneath when you expand it. Active record counts show in green and rejected counts show in red. The expand/collapse animation is a CSS max-height transition so no extra library needed.

View Reports Donut Chart
I implemented the donut chart on the View Reports page using Chart.js. It breaks down the weekly reissue data by card type like VISA, TJX, Lowes, and Amazon, and sits next to KPI cards showing total Cards Impacted and Clients Impacted.

Backend
The backend is a Spring Boot app under the package com.syf.fri.fetchdataapi. I built the weekly reports feature on a branch called feature/FetchWeeklyReports. It uses JdbcTemplate to run raw SQL queries against a MySQL database.

Endpoints
•	GET /api/upload-audits: returns all uploaded file records from the audit table
•	GET /api/upload-audits/{id}: returns one record by ID
•	GET /api/weeklyReports?from=YYYY-MM-DD&to=YYYY-MM-DD: the main one I built, returns weekly report data for a given date range

Database Tables
Three tables are involved. The audit table (frdrei_file_upld_audit_tbl) has every uploaded CSV file with the file name, who uploaded it, the status, record count, and reissue date. The filtered out table (frdrei_acts_filtered_out_tbl) has individual account records that got filtered out, which is where the rejected and duplicate counts come from. The inprocess table (frdrei_acts_inprocess_tbl) has account records in the reissue process and is used to get active record counts by date.

Models
I set up the model classes that the API responses map to. UploadAudit maps to the audit table with fields like fileName, uploadedBy, status, recordCount, and reissueDate. WeeklyReportsResponse is the top level wrapper that holds a list of WeeklyReportWeek objects. Each WeeklyReportWeek has a weekStart, weekEnd, a WeekTotals object, and a list of WeeklyReportFileRow objects. WeekTotals has the rolled up numbers for the week and WeeklyReportFileRow has the per-file breakdown.

Repositories
I wrote three repository classes that handle all the SQL.

UploadAuditRepository handles the audit table. The key method I added for weekly reports was findByReissueDateRange() which pulls all files where the reissue date falls in the selected range:

SELECT Internal_id, orig_file_nm, upld_by_name, upld_by_sso,
       stat, upld_dt, rec_cnt, reissue_dt
FROM frdrei_file_upld_audit_tbl
WHERE reissue_dt BETWEEN ? AND ?
ORDER BY reissue_dt DESC, upld_dt DESC

FilteredOutRepository queries the filtered out table to get rejected and duplicate counts per file. It takes a list of file names and builds a dynamic IN clause so it only needs one query instead of one per file:

SELECT file_nm,
  SUM(CASE WHEN reissue_filt_stat = 'DUPLICATE' THEN 1 ELSE 0 END) AS dup_cnt,
  SUM(CASE WHEN reissue_filt_stat IN ('INVALID','EXCLUDE','EXCLUDED')
      THEN 1 ELSE 0 END) AS rej_cnt
FROM frdrei_acts_filtered_out_tbl
WHERE file_nm IN (?, ?, ...)
GROUP BY file_nm

InprocessRepository queries the inprocess table to get active record counts grouped by reissue date. Active means the record status is not DUPLICATE or INVALID:

SELECT reissue_dt,
  SUM(CASE WHEN reissue_stat NOT IN ('DUPLICATE','INVALID')
      THEN 1 ELSE 0 END) AS active_cnt
FROM frdrei_acts_inprocess_tbl
WHERE reissue_dt BETWEEN ? AND ?
GROUP BY reissue_dt

Service Layer
WeeklyReportsServiceImpl is where all three repositories come together to build the response. When a request comes in it queries the audit table for all files in the date range, grabs the distinct file names and runs one batch query against the filtered out table to get rejected and duplicate counts, then runs a separate query against the inprocess table to get active counts by date.

It then groups the audit records by reissue date, sorts the weeks newest first, and builds each week using a private buildWeek() method. For each file in a week it sets the file name, status, and total record count, then looks up the rejected and duplicate counts from the earlier batch query. The week totals get computed by summing across all the file rows. The active record count comes from the inprocess results using the week start date as the lookup key.

One thing worth noting is that the inprocess table does not have a file name column, so active counts can only be computed at the week level and not per file. The per-file active record field is null and the frontend just shows a dash there.
