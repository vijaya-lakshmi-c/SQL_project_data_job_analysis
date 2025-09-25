# Introduction
📊 Dive into the data job market ! Focusing on data analyst roles, this project explores 💰 top_paying jobs, 🔥 in-demand skills, and 📈 where high demand meets high salary in data analytics.  

🔎 SQL queries ? Check them out here : [project_sql_folder](/project_sql/)

# Background
This project was created to help navigate the data analyst job market by identifying the highest-paying and most in-demand skills, making it easier for others to find the best job opportunities.

Data hails from [Data source](https://www.lukebarousse.com/sql). 
The dataset used is packed with insights on job titles, salaries, locations, and essential skills.

### The goal of my SQL queries was to answer the following questions:
1. What are the top_paying data analyst jobs ?
2. What skills are required for these top-paying jobs ?
3. What skills are most in demand for data analysts ?
4. Which skills are associated with higher salaries ? 
5. What are the most optimal skills to learn ?

# Tools I Used
For my in-depth analysis of the data analyst job market, I utilized several essential tools:

- **SQL:** Served as the foundation of my analysis, enabling me to query data effectively and extract meaningful insights.
- **PostgreSQL:** The database management system used for storing and managing job posting data efficiently.
- **Visual Studio Code:** My primary environment for writing and executing SQL queries, as well as managing database interactions.
- **Git & GitHub:** Used for version control and sharing code, facilitating collaboration and keeping track of project progress. 

# The Analysis
Each query in this project was designed to investigate specific aspects of the data analyst job market. Here's how I approached each question:

### 1. Top paying Data Analyst Jobs
To identify the highest-paying roles, I filtered data analyst positions by avergae yearly salary and location, focusing on remote jobs. This query hilights the high paying opportunities in the field.

```sql 
SELECT
    job_postings_fact.job_id,
    job_postings_fact.job_title,
    job_postings_fact.job_location,
    job_postings_fact.job_schedule_type,
    job_postings_fact.salary_year_avg,
    job_postings_fact.job_posted_date,
    company_dim.name AS company_name
FROM 
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_postings_fact.job_title_short = 'Data Analyst' AND 
    job_postings_fact.job_location = 'Anywhere' AND
    job_postings_fact.salary_year_avg IS NOT NULL
ORDER BY
    job_postings_fact.salary_year_avg DESC
LIMIT 10
```

Here's the breakdown of the top data analyst jobs in 2023:
- High Salary Potential: Top 10 data analyst roles range from $184K to $650K, showing strong earning opportunities.
- Diverse Employers: Companies like SmartAsset, Meta, and AT&T offer top salaries, reflecting demand across industries.
- Varied Job Titles: Roles range from Data Analyst to Director of Analytics, highlighting the field’s wide specialization.

2. Skills for Top Paying Jobs
To identify the skills required for top-paying roles, I joined the job postings with the skills dataset, revealing what employers prioritize in high-compensation positions.

```sql
WITH top_paying_jobs AS (
    SELECT
        job_postings_fact.job_id,
        job_postings_fact.job_title,
        job_postings_fact.salary_year_avg,
        company_dim.name AS company_name
    FROM 
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_postings_fact.job_title_short = 'Data Analyst' AND 
        job_postings_fact.job_location = 'Anywhere' AND
        job_postings_fact.salary_year_avg IS NOT NULL
    ORDER BY
        job_postings_fact.salary_year_avg DESC
    LIMIT 10
)

SELECT 
    top_paying_jobs.*,
    skills_dim.skills 
FROM 
    top_paying_jobs
INNER JOIN skills_job_dim ON skills_job_dim.job_id = top_paying_jobs.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id =skills_dim.skill_id
ORDER BY
    top_paying_jobs.salary_year_avg DESC
```
Here's the breakdown of the most demanded skills for the top 10 highest paying data analyst jobs in 2023:

- SQL is leading with a bold count of 8.
- Python follows closely with a bold count of 7.
- Tableau is also highly sought after, with a bold count of 6. 
- Other skills like R, Snowflake, Pandas, and Excel show varying degrees of demand.

3. In-Demand Skills for Data Analysts
This query helped identify the skills most frequently requested in job postings, directing focus to areas with high demand.

```sql
SELECT 
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_skill_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON skills_job_dim.job_id = job_postings_fact.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id =skills_dim.skill_id 
WHERE
    job_postings_fact.job_title_short = 'Data Analyst' AND
    job_postings_fact.job_work_from_home = TRUE
GROUP BY
    skills_dim.skills
ORDER BY
    demand_skill_count DESC
LIMIT 5
```
Most In-Demand Data Analyst Skills in 2023

- SQL and Excel: Still core to the role, highlighting the importance of strong foundations in data querying and spreadsheet analysis.
- Python, Tableau, Power BI: Key tools for programming and visualization, reflecting the growing need for technical skills in data storytelling and decision-making.


| Skill      | Demand count |
|------------|-------------------------------|
| SQL        | 7,291                         |
| Excel      | 4,611                         |
| Python     | 4,330                         |
| Tableau    | 3,745                         |
| Power BI   | 2,609                         |

Table of the demand for the top 5 skills in data analyst job postings

4. Skills Based on Salary
Exploring the average salaries associated with different skills revealed which skills are the highest paying.

```sql

SELECT 
    skills_dim.skills,
    ROUND(AVG(job_postings_fact.salary_year_avg),0) AS average_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON skills_job_dim.job_id = job_postings_fact.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id =skills_dim.skill_id 
WHERE
    job_postings_fact.job_title_short = 'Data Analyst' AND
    job_postings_fact.salary_year_avg IS NOT NULL
GROUP BY
    skills_dim.skills
ORDER BY
    average_salary DESC
LIMIT 25
```

Here's a breakdown of the results for top paying skills for Data Analysts:
- **Big Data & ML Tools:** Skills in PySpark, Couchbase, DataRobot, and Python libraries like Pandas and NumPy are highly valued for their role in advanced data processing and predictive modeling.
- **Dev & Deployment Tools:** Proficiency in GitLab, Kubernetes, and Airflow shows strong earning potential, highlighting the value of automation and pipeline management.
- **Cloud Platforms:** Experience with Elasticsearch, Databricks, and GCP reflects the demand for cloud-based analytics skills that significantly boost salaries.


| Skill       | Average Salary (USD) |
|-------------|----------------------|
| SVN         | $400,000             |
| Solidity    | $179,000             |
| Couchbase   | $160,515             |
| DataRobot   | $155,486             |
| Golang      | $155,000             |
| MXNet       | $149,000             |
| dplyr       | $147,633             |
| VMware      | $147,500             |
| Terraform   | $146,734             |
| Twilio      | $138,500             |

Table of the average salary for the top 10 paying skills for data analysts

5. Most Optimal Skills to Learn
By combining demand and salary data, this query identified skills that are both highly sought after and well-paid helping prioritize strategic areas for skill development.

```sql
SELECT
    skills_dim.skiLls,
    COUNT(skills_job_dim.job_id) AS demand_skill_count,
    ROUND(AVG(job_postings_fact.salary_year_avg),0) AS avg_salary
FROM
    job_postings_fact
INNER JOIN skills_job_dim ON skills_job_dim.job_id = job_postings_fact.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id 
WHERE
    job_postings_fact.salary_year_avg IS NOT NULL AND
    job_postings_fact.job_title_short = 'Data Analyst' AND
    job_postings_fact.job_work_from_home = TRUE
GROUP BY
    skills_dim.skiLls
HAVING
    COUNT(skills_job_dim.job_id) > 10
ORDER BY 
    avg_salary DESC,
    demand_skill_count
LIMIT 25
```


| Skill       | Demand Count | Average Salary (USD) |
|-------------|--------------|----------------------|
| Go          | 27           | $115,320             |
| Confluence  | 11           | $114,210             |
| Hadoop      | 22           | $113,193             |
| Snowflake   | 37           | $112,948             |
| Azure       | 34           | $111,225             |
| BigQuery    | 13           | $109,654             |
| AWS         | 32           | $108,317             |
| Java        | 17           | $106,906             |
| SSIS        | 12           | $106,683             |
| Jira        | 20           | $104,918             |

Table of the most optimal skills for data analyst sorted by salary

Here's a breakdown of the most optimal skills for Data Analysts in 2023:
- Programming Languages: Python (demand: 236) and R (148) are highly sought after, with average salaries around $101K, showing strong value and widespread availability.
- Cloud Technologies: Skills like Snowflake, Azure, AWS, and BigQuery are in demand with solid salaries, highlighting the rise of cloud and big data tools.
- BI & Visualization: Tableau (230) and Looker (49) emphasize the importance of data visualization and business intelligence, with salaries near $100K.
- Database Skills: Oracle, SQL Server, and NoSQL remain essential, with salaries between $97K and $104K, underscoring the need for data management expertise.

# What I Learned
On this SQL journey, I’ve leveled up with some powerful skills:

- 🧩 Advanced Query Building: Confidently joining multiple tables and crafting WITH clauses to create efficient temporary tables.
- 📊 Mastering Aggregations: Using GROUP BY along with COUNT(), AVG(), and other functions to summarize and analyze data effectively.
- 💡 Turning Data into Insights: Applying SQL to solve real-world problems by writing precise, insightful queries that deliver actionable answers.

# Conclusion
## Insights
From the analysis, several general insights emerged:
1. **Top-Paying Remote Jobs:** Data analyst roles offer salaries up to $650,000.
2. **Key Skills for High Pay:** Advanced SQL proficiency is essential for top salaries.
3. **Most In-Demand Skill:** SQL dominates demand in the job market, making it crucial for job seekers.
4. **High-Salary Niche Skills:** Specialized skills like SVN and Solidity command premium salaries.
5. **Optimal Skill for Market Value:** SQL ranks highest in both demand and salary, making it a top skill to maximize value.

## Final thoughts
This project improved my SQL skills and revealed key data analyst job market trends. Focusing on high-demand, well-paid skills can help job seekers stand out. It also highlights the importance of ongoing learning in data analytics.
