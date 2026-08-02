Hospital Operational Efficiency Analysis
Project Overview

This project analyzes operational inefficiencies in a multispecialty hospital and proposes solutions to reduce patient waiting time and improve workflow efficiency. The analysis was conducted in both Excel (pivot tables, charts) and SQL (aggregate queries, joins, window functions) to validate findings across tools.

Business Problem
High patient waiting time due to inefficient workflows
Manual registration causing delays
Limited visibility into staff availability
Data Analysis — Excel
Department vs Waiting Time
Manual vs Digital Registration Impact
Wait Time by Day
Staff Availability Analysis
Data Analysis — SQL

The same dataset (hospital dataset.xlsx) was loaded into SQLite and re-analyzed using SQL to validate the Excel findings independently.

Average wait time by department:

sql
SELECT department, AVG(wait_time_min) AS avg_wait_time
FROM patient_data
GROUP BY department;

Result: Cardiology ≈ 28.3 min · Radiology ≈ 44.6 min · Orthopedics ≈ 56.2 min

Manual vs. Digital registration impact:

sql
SELECT registration_type, AVG(wait_time_min) AS avg_wait_time
FROM patient_data
GROUP BY registration_type;

Result: Manual ≈ 50.1 min · Digital ≈ 26.4 min (≈47% reduction)

Departments exceeding a 40-minute average (HAVING):

sql
SELECT department, AVG(wait_time_min) AS avg_wait_time
FROM patient_data
GROUP BY department
HAVING AVG(wait_time_min) > 40;

Wait time by department lead (JOIN):

sql
SELECT d.head_doctor, AVG(p.wait_time_min) AS avg_wait_time
FROM patient_data p
JOIN department_lead d ON p.department = d.department
GROUP BY d.head_doctor;

Data quality check (LEFT JOIN):

sql
SELECT p.department, d.head_doctor
FROM patient_data p
LEFT JOIN department_lead d ON p.department = d.department
WHERE d.head_doctor IS NULL;

Result: empty set — confirms every department has a matching lead, so the join above is reliable.

Within-department ranking (window function):

sql
SELECT patient_id, department, wait_time_min,
       RANK() OVER (PARTITION BY department ORDER BY wait_time_min DESC) AS dept_rank
FROM patient_data;

Surfaces individual outlier patients within each department, not just department-level averages.

Full write-up with business-question framing for each query: see Hospital_SQL_Case_Study.pdf (upload this file to the repo alongside this README).

Key Insights
Orthopedics department has the highest waiting time (~56 min)
Digital registration reduces waiting time by ~47%
Process design — not staffing levels or patient volume — is the primary driver of wait time variation
Every department has a verified, unambiguous department-lead mapping (no missing joins)
Solution Recommendations
Digital patient registration system, expanded beyond Cardiology
Automated scheduling and queue management
Optimized staff allocation, prioritizing Orthopedics
Expected Impact
20–30% reduction in patient waiting time
Improved patient flow and operational efficiency
Tools Used
Microsoft Excel (Pivot Tables, Analysis)
SQL / SQLite (Aggregate queries, Joins, Window functions)
PowerPoint (Presentation)
Lucidchart (Process Diagrams)
