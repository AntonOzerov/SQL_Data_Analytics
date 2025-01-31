# SQL_Data_Analytics


## Introduction 

This project was aimed at researching the job market using SQL queries to find out what jobs offer the highest salaries, what skills are in demand and which skills account for the most lucrative jobs on the market.

In order to find out the systemize the research I came up with the following questions:

1. What are the top-paying data analyst jobs?
2. What skills are required for these top-paying jobs?
3. What skills are most in demand for data analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn?

## The tools used in the project

1. SQL - the language that allowed me to query the database to discover findings about job market
2. PostgreSQL - database management system 
3. Visual Studio Code - code editor used for database management and for executing SQL queries
4. Jupyter notebook - Integrated Developer Environment for creting visualisations in this project

## The Analysis

1. What are the top-paying data analyst jobs?

In order to answer this question I filtered the data by the job title (Data Analyst) and job location (Remote) after joining two tables that together contain all the necessary info.

```
SELECT 
    job_id,
    job_title_short,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name as company_name
    
FROM 
    job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    
WHERE 
    job_title_short = 'Data Analyst' AND
    job_location = 'Anywhere' AND salary_year_avg IS NOT NULL
    
ORDER BY 
    salary_year_avg DESC
    
LIMIT 10
```
<img width="1118" alt="Screenshot 2025-01-30 at 20 21 28" src="https://github.com/user-attachments/assets/68ee70cd-fd14-48f8-a6f7-e0aae9d2bc74" />

The data reveals that remote data analyst positions offer very competitive salaries ranging from 184 000 to 650 000 USD. 

2. What skills are required for these top-paying jobs?

```WITH top_paying_jobs AS 

(SELECT 
    job_id,
    job_title,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name as company_name
FROM 
    job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE 
    job_title_short = 'Data Analyst' AND
    job_location = 'Anywhere' AND salary_year_avg IS NOT NULL
ORDER BY 
    salary_year_avg DESC
LIMIT 10)

SELECT top_paying_jobs.*,
skills
From top_paying_jobs INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON  skills_dim.skill_id = skills_job_dim.skill_id```

<img width="1013" alt="Screenshot 2025-01-30 at 16 10 59" src="https://github.com/user-attachments/assets/0034a840-21ba-4ca1-863f-9ad022f38aec" />
   

