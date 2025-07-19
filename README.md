# Career Track Analysis
SQL + Tableau Project

**1. Overview**
This project analyzes student enrollments and completion behavior across various career tracks using SQL for data extraction and Tableau for visualization. It focuses on the time taken by students to complete tracks and patterns of enrollment over months.

**🛠2. Tools Used**
MySQL for data querying and processing

Tableau for data visualization

**3. Dataset Description**
Two main tables were used:

career_track_info

Contains details about each career track.

career_track_student_enrollments

Contains student enrollment and completion details for each track.

Each table was loaded into the database by sql queries and the values were added.

**4. SQL Tasks and Queries**
1. Load Database
-- Run this to disable keys and speed up import
LOCK TABLES `career_track_info` WRITE;
/*!40000 ALTER TABLE `career_track_info` DISABLE KEYS */;

2. Main Dataset Extraction
SELECT 
  ROW_NUMBER() OVER(ORDER BY cse.student_id, cse.track_id) AS student_track_id,
  cse.student_id,
  cti.track_name,
  cse.date_enrolled,
  CASE 
    WHEN cse.date_completed IS NULL THEN 0 
    ELSE 1 
  END AS track_completed,
  DATEDIFF(cse.date_completed, cse.date_enrolled) AS days_for_completion,
  CASE 
    WHEN DATEDIFF(cse.date_completed, cse.date_enrolled) IS NULL THEN 'Not Completed'
    WHEN DATEDIFF(cse.date_completed, cse.date_enrolled) = 0 THEN 'Same day'
    WHEN DATEDIFF(cse.date_completed, cse.date_enrolled) BETWEEN 1 AND 7 THEN '1 to 7 days'
    WHEN DATEDIFF(cse.date_completed, cse.date_enrolled) BETWEEN 8 AND 30 THEN '8 to 30 days'
    WHEN DATEDIFF(cse.date_completed, cse.date_enrolled) BETWEEN 31 AND 60 THEN '31 to 60 days'
    WHEN DATEDIFF(cse.date_completed, cse.date_enrolled) BETWEEN 61 AND 90 THEN '61 to 90 days'
    WHEN DATEDIFF(cse.date_completed, cse.date_enrolled) BETWEEN 91 AND 365 THEN '91 to 365 days'
    ELSE '366+ days'
  END AS completion_bucket
FROM career_track_student_enrollments cse
JOIN career_track_info cti ON cse.track_id = cti.track_id;

3. Total Track Completions
SELECT COUNT(*) AS total_completions
FROM career_track_student_enrollments
WHERE date_completed IS NOT NULL;

4. Longest Completion Period
SELECT MAX(DATEDIFF(date_completed, date_enrolled)) AS max_days_for_completion
FROM career_track_student_enrollments
WHERE date_completed IS NOT NULL;

**5. Tableau Visualizations**
Used dual axis: Bar chart for enrollments and line chart for completion ratio.
<img width="1357" height="737" alt="image" src="https://github.com/user-attachments/assets/2a51c301-c87d-49d0-946e-6618c7b40083" />

Track Completion Bucket Distribution
<img width="1353" height="738" alt="image" src="https://github.com/user-attachments/assets/adff1ec0-bfef-47d1-840f-ee8f6cd8aeac" />

**6. Files Included**
Career Track Analysiss.sql – SQL script for dataset generation

career_track_completions.csv – Exported dataset for Tableau

Tableau workbook (twbx) – Visualizations


**7. Insights**
Majority of completions happen within 30 days of enrollment.

Very few students take more than a year to complete a track.

Some tracks have significantly more enrollments and completions than others.

Completion ratio varies slightly month to month.

**8. Questions Answered**
✅ How many total track completions are there?

✅ What is the longest time taken by any student to complete a track?

✅ How many students completed within specific time buckets (e.g., same day, 1–7 days, etc.)?

✅ What are the monthly trends of enrollments and completions?

✅ How many students enrolled in each track?

