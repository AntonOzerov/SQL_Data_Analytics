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

## 1. What are the top-paying data analyst jobs?

In order to answer this question I filtered the data by the job title (Data Analyst) and job location (Remote) after joining two tables that together contain all the necessary info.

### SQL Query
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

## 2. What skills are required for these top-paying jobs?

To find this out I used a tempopary table (CTE) to extract info about the top paying jobs and then joined it with the table containing skills. Afterwards, I created a visualisation in Jupyter Notebook using Python and Matplotlib library for visuals.

### SQL Query
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
INNER JOIN skills_dim ON  skills_dim.skill_id = skills_job_dim.skill_id
```
### Python code for visualization
```
plt.figure(figsize=(10, 6))
bars = plt.barh(top_10_paid_skills['skills'], top_10_paid_skills['count'], color='skyblue')
plt.xlabel('Count of skills', fontsize=12)
plt.title('Top 10 skills for top paying jobs', fontsize=14, pad=20)
plt.gca().invert_yaxis()  # Highest count at the top

# Add black count labels to bars
for bar in bars:
    width = bar.get_width()
    plt.text(width + 0.1,            # X-position: width + small offset
             bar.get_y() + bar.get_height()/2,  # Y-position: center of bar
             f'{int(width)}',        # Text format
             va='center',            # Vertical alignment
             ha='left',              # Horizontal alignment
             color='black',          # Set text color to black
             fontsize=10)

plt.tight_layout()
plt.show()
```
<img width="1081" alt="Screenshot 2025-01-31 at 11 54 20" src="https://github.com/user-attachments/assets/77c61b4a-e4c2-4f02-b957-e515072dbeae" />


## 3. What skills are most in demand for data analysts?

For this question I displayed skills from one table, joined it with another table and counted top 10 skills simultaneously filtering the data for only Data Analyst remone positions.


### SQL Query
 ```
SELECT 
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_dim.skill_id = skills_job_dim.skill_id
WHERE 
job_title_short = 'Data Analyst' AND job_location = 'Anywhere'
GROUP BY skills
ORDER BY demand_count DESC
LIMIT 10;
```

<img width="305" alt="Screenshot 2025-01-31 at 12 41 16" src="https://github.com/user-attachments/assets/e0ba7437-cc05-472c-bc2e-27961f2254f2" />

Data insights:

1. The data reveals that SQL, Excel and Python are the most popular essential tools for Data Analysts used for Data Collection, Preprocessing, Exploration
2. Tableau and Power BI are teh most popular tools for the next step of data analysis - Data interpretation and visualization and Storytelling

## 4. Which skills are associated with higher salaries?

This question required selecting skills, average salary, joining several tables to then filter Data Analyst remote jobs.

### SQL Query 

```
SELECT 
    skills,
    ROUND(AVG(salary_year_avg),0) AS avg_salary_USD,
    ROW_NUMBER() OVER(ORDER BY ROUND(AVG(salary_year_avg),0) DESC) as rating
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_dim.skill_id = skills_job_dim.skill_id
WHERE 
job_title_short = 'Data Analyst' AND
salary_year_avg IS NOT NULL
AND job_work_from_home = TRUE
GROUP BY skills
ORDER BY avg_salary_USD DESC
LIMIT 25;
```

<img width="452" alt="Screenshot 2025-01-31 at 13 50 54" src="https://github.com/user-attachments/assets/282f8510-ee38-4cc3-9e41-5baa5b45b28d" />


Here are the insights that the data reveals:


- PySpark Leads – PySpark, a big data processing tool, offers the highest average salary at $208,172, indicating that expertise in distributed computing is highly valued.
- Bitbucket in Second Place – Bitbucket, a Git repository management tool, has a surprisingly high average salary of $189,155, suggesting demand for DevOps-related version control expertise.
- Couchbase & Watson – Both Couchbase (a NoSQL database) and Watson (IBM's AI platform) are tied at $160,515, highlighting the importance of database and AI-driven solutions.
- AI & Machine Learning – DataRobot ($155,486) (automated ML) and Watson showcase the strong demand for AI/ML capabilities in data analytics.
- Development & Collaboration Tools – GitLab ($154,500) and Jupyter ($152,777) are tools essential for coding and collaboration in data science, reflecting their importance in the field.
- Programming & Data Manipulation – Swift ($153,750) (though more common in mobile dev) and Pandas ($151,821) show that Python-based data manipulation and development are well-compensated.
- Elasticsearch – Ranking 10th at $145,000, it emphasizes the value of search and indexing capabilities in data-related fields.


## 5. What are the most optimal skills to learn?

This question is answered by combining info from previous queries on  top skill demand and top average salaries. I created two temporary tables (CTE) which I then joined and extracted info on the most popular skills and their respective average salaries to ultimately display a comprehensive report featuring most popular and lucrative skills to acquire for a Data Analyst.

### SQL Query 

```
WITH skills_demand AS
(
    SELECT 
        skills_dim.skill_id,
        LOWER(skills_dim.skills) AS skills,  -- Standardize to lowercase
        COUNT(DISTINCT skills_job_dim.job_id) AS demand_count  -- Ensure unique job postings
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_dim.skill_id = skills_job_dim.skill_id
    WHERE 
        job_title_short = 'Data Analyst' 
        AND salary_year_avg IS NOT NULL 
        AND job_work_from_home = TRUE
    GROUP BY skills_dim.skill_id, LOWER(skills_dim.skills)  -- Grouping by standardized skill name
),

average_salary AS
(
    SELECT 
        skills_job_dim.skill_id,
        LOWER(skills_dim.skills) AS skills, -- Standardize to lowercase
        ROUND(AVG(salary_year_avg), 0) AS avg_salary
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_dim.skill_id = skills_job_dim.skill_id
    WHERE 
        job_title_short = 'Data Analyst' 
        AND salary_year_avg IS NOT NULL 
        AND job_work_from_home = TRUE
    GROUP BY skills_job_dim.skill_id, LOWER(skills_dim.skills)
)

SELECT 
    MIN(skills_demand.skill_id) AS skill_id,  -- Use MIN to avoid duplicate skill IDs
    skills_demand.skills,
    SUM(skills_demand.demand_count) AS demand_count,  -- Aggregate duplicate counts
    AVG(average_salary.avg_salary) AS avg_salary  -- Average salary if duplicated
FROM skills_demand
INNER JOIN average_salary 
    ON skills_demand.skills = average_salary.skills  -- Join by skill name instead of ID
GROUP BY skills_demand.skills
HAVING SUM(skills_demand.demand_count) > 10
ORDER BY demand_count DESC
LIMIT 10;
```

<img width="534" alt="Screenshot 2025-01-31 at 14 41 40" src="https://github.com/user-attachments/assets/61a09092-a8c3-41d3-be47-ee7dde11cfc8" />


Here are the insights that the data reveals:


1. SQL (Most In-Demand - 398 Jobs | Avg. Salary: $97,237)
SQL (Structured Query Language) is the foundation of data analysis, allowing analysts to retrieve, manipulate, and manage large datasets stored in relational databases.
Every data-driven company needs SQL, making it the most sought-after skill.
2. Excel (256 Jobs | Avg. Salary: $87,288)
Despite the rise of more advanced tools, Excel remains crucial for data manipulation, reporting, and quick analysis.
It is widely used in financial analysis, business intelligence, and data visualization.
3. SAS (63 Jobs | Avg. Salary: $98,902)
SAS is a specialized tool used in banking, healthcare, and government sectors for advanced statistical analysis and predictive modeling.
While Python and R have gained popularity, SAS is still in demand for legacy enterprise systems.
4. Python (236 Jobs | Avg. Salary: $101,397)
Python is essential for automation, machine learning, data wrangling, and advanced analytics.
Libraries like Pandas, NumPy, and Scikit-learn make it the top programming language for data professionals.
5. Tableau (230 Jobs | Avg. Salary: $99,288)
Tableau is one of the most popular data visualization tools used in business intelligence and analytics.
It enables non-technical users to create interactive dashboards and derive insights from data.
6. R (148 Jobs | Avg. Salary: $100,499)
R is particularly valuable in statistical computing and data science, often used in research, finance, and healthcare.
It excels in data visualization (ggplot2), statistical modeling, and machine learning.
7. Power BI (110 Jobs | Avg. Salary: $97,431)
Power BI is Microsoft’s leading BI tool, allowing users to create dynamic dashboards and reports.
It integrates well with SQL, Excel, and Azure, making it a go-to tool for businesses using Microsoft ecosystems.
8.  PowerPoint (58 Jobs | Avg. Salary: $88,701)
Data analysts need to communicate insights effectively, making PowerPoint a valuable skill for presentation and storytelling.
Many organizations rely on PowerPoint for reporting and business strategy discussions.
9. Looker (49 Jobs | Highest Avg. Salary: $103,795)
Looker is a modern data exploration and business intelligence platform, part of Google Cloud.
Its high salary reflects its growing adoption in data-driven decision-making companies.
10. Word (48 Jobs | Highest Avg. Salary: $82,576)
Wordprocessing skills are fundamental in most office environments. While not a high-demand specialized skill, proficiency in Word is still necessary for creating documents and reports.

### Conclusion

The skills listed are all valuable to learn, each offering unique benefits in terms of career opportunities, earning potential, and personal development. The specific choice of which skill to focus on depends on your career interests, aptitude, and goals. However, investing time and effort in developing proficiency in any of these areas is likely to yield a positive return in the long run.
