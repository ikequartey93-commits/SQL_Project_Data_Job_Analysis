[README,md](https://github.com/user-attachments/files/34858482/README.md)
# Introduction
Dive into the data job market focusing on data analyst roles,this project explores top paying jobs to help job seekers prioritize which skills to learn and better understand the data-job market.

SQL queries ? Check them here: [project_sql folder](/project_sql/)

# Background
This project organizes and analyzes job-posting data to explore questions such as:

1. What are the top-paying jobs for Data Analysts?
2. What are the skills required for these top-paying roles?
3. What are the most in-demand skills for Data Analysts?
4. What are the top skills based on salary for Data Analysts?
5. What are the most optimal skills to learn?

# Tools I Used
- **SQL**: The foundation of my analysis, enabling detailed data exploration and query-building.
- **PostgreSQL**: A database management system for efficiently organizing and querying the dataset.
- **Visual Studio Code**: For writing and executing SQL scripts.
- **Git & GitHub**: storing and documenting the project
# The Analysis
Here is how I tackled each aspect of the analysis:
### *1. What are the top-paying jobs for Data Analysts?*
To identify the highest-paying roles.
```sql
SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM
     job_postings_fact    
LEFT JOIN company_dim ON job_postings_fact.company_id =company_dim.company_id
WHERE 
    job_title_short ='Data Analyst' AND
    job_location ='Anywhere' AND
    salary_year_avg IS NOT NULL
    ORDER BY
    salary_year_avg DESC
    LIMIT 10
```
### *2. What are the skills required for these top-paying roles?*
This query builds upon the top-paying Data Analyst jobs identified
```sql
WITH top_paying_jobs AS (
    SELECT
        job_postings_fact.job_id,
        job_postings_fact.job_title,
        job_postings_fact.salary_year_avg,
        company_dim.name as company_name
    FROM
        job_postings_fact
        LEFT JOIN company_dim ON company_dim.company_id = job_postings_fact.company_id
    WHERE
        job_postings_fact.job_title_short = 'Data Analyst' AND
        job_postings_fact.salary_year_avg IS NOT NULL AND
        job_postings_fact.job_location = 'Anywhere'
    ORDER BY
        salary_year_avg DESC
    LIMIT 10
)
SELECT
    top_paying_jobs.*,
    skills_dim.skills
FROM
    top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    top_paying_jobs.salary_year_avg DESC;
```
### *3. What are the most in-demand skills for Data Analysts?*
```sql
SELECT
    skills_dim.skills AS skill_name,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM
    job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_postings_fact.job_title_short = 'Data Analyst'
GROUP BY 
    skills_dim.skills
ORDER BY
    demand_count DESC
LIMIT 5;
```
The table below lists the top 5 most in-demand skills for Data Analyst positions
| Skill Name | Demand Count |
|------------|--------------|
| SQL        | 92,628       |
| Excel      | 67,031       |
| Python     | 57,326       |
| Tableau    | 46,554       |
| Power BI   | 39,468       |
I  then applied a filter to focus exclusively on remote jobs, and here are the results:

| Skill Name | Demand Count |
|------------|--------------|
| SQL        | 7291         |
| Excel      | 4611         |
| Python     | 4330         |
| Tableau    | 3745         |
| Power BI   | 2609         |
### *4. What are the top skills based on salary for Data Analysts?*
```sql
SELECT
    skills_dim.skills,
    ROUND(AVG(salary_year_avg)) AS avg_salary,
    COUNT(salary_year_avg)
FROM
    job_postings_fact
INNER JOIN skills_job_dim ON skills_job_dim.job_id = job_postings_fact.job_id
INNER JOIN skills_dim ON skills_dim.skill_id = skills_job_dim.skill_id
WHERE
    job_postings_fact.job_title_short = 'Data Analyst' 
    AND job_postings_fact.salary_year_avg IS NOT NULL
    AND job_postings_fact.job_work_from_home = TRUE
GROUP BY
    skills_dim.skills
HAVING
    COUNT(salary_year_avg) > 3
ORDER BY
    avg_salary DESC
LIMIT 25;
```
### *5. What are the most optimal skills to learn?*
SELECT
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) as demand_count,
    ROUND(AVG(job_postings_fact.salary_year_avg), 2) AS avg_salary
FROM
    job_postings_fact
INNER JOIN skills_job_dim ON skills_job_dim.job_id = job_postings_fact.job_id
INNER JOIN skills_dim ON skills_dim.skill_id = skills_job_dim.skill_id
WHERE
    job_postings_fact.job_title_short = 'Data Analyst' 
    AND job_postings_fact.salary_year_avg IS NOT NULL
GROUP BY
    skills_dim.skill_id
HAVING
    COUNT(skills_job_dim.job_id) > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25;
# What I Learned
Through this project, I deepened my understanding of using SQL to analyze real-world datasets
# Skills Practiced
This project allowed me to practice and enhance the following skills:

- **SQL Proficiency:** Writing advanced queries using JOIN, GROUP BY, HAVING, and ORDER BY.
# Conclusion
This analysis highlights the critical skills and tools necessary for success as a Data Analyst. SQL remains the most in-demand and universally valued skill, while tools like Tableau, Power BI, and Python are crucial for handling data visualization and advanced analysis.
