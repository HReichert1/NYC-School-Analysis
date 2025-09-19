# Day 3 of the projects focused on SQL queries to be run within Python to explore real-world education data. A connection to a PostgreSQL database was established and SQL queries were created to analyze school-level insights.
## 1. A connection to the the training database was set up using Pyhton
```
conn = psycopg2.connect(
    dbname="neondb",
    user="neondb_owner",
    password="npg_CeS9fJg2azZD",
    host="ep-falling-glitter-a5m0j5gk-pooler.us-east-2.aws.neon.tech",
    port="5432",
    sslmode="require"
)
cur = conn.cursor()
```
## 2. In a new Jupyter Notebook pandas.read_sql() was used to fetch to query
```
query = "SELECT * FROM nyc_schools.high_school_directory LIMIT 5;"
df = pd.read_sql(query, conn)
df.head()
```
## 3. Answers were provided:
### 3.1. How many schools are there in each borough?
```
query = """
SELECT borough, COUNT(distinct dbn) AS school_count
FROM nyc_schools.high_school_directory
GROUP BY borough;
"""
df_result = pd.read_sql(query, conn)
df_result
```
### 3.2 What is the average % of English Language Learners per borough?

```
query = """
with data_joined as (
select dire.borough, dire.dbn, demo.ell_num, demo.ell_percent 
from nyc_schools.school_demographics as demo
left join nyc_schools.high_school_directory as dire on demo.dbn = dire.dbn
)

select borough, avg(ell_percent) as avg_ell
from data_joined
group by borough;
"""
df_result = pd.read_sql(query, conn)
df_result
```

### 3.3 Using the data from the school demographics and high school director, a query was written to find 
###           the top 3 shools for each borough with the highest percentage of special education students
```

query = """
 with data_joined as (
select dire.borough, dire.dbn, demo.sped_percent, dire.school_name, demo.schoolyear 
from nyc_schools.school_demographics as demo
left join nyc_schools.high_school_directory as dire on demo.dbn = dire.dbn
)

SELECT borough, dbn, school_name, schoolyear, sped_percent  
FROM (
    SELECT *,
           ROW_NUMBER() OVER (
               PARTITION BY borough
               ORDER BY sped_percent DESC
           ) AS rn
    FROM data_joined         
) ranked
WHERE rn <= 3
ORDER BY borough, sped_percent DESC;
"""
df_result = pd.read_sql(query, conn)
df_result
```

## 4. A short insights summary was created  
- for joins of the 2 datasets school_demographics and high_school_directory (required for the last 2 questions) only dbn for Manhattan is available in both datasets 
- Staten Island has the lowest number of schools 
- Brooklyn has the highest number of schools
- for the language learners the variety across schools is quite large, overall average is therefore quite small
- school_demographics contains multiple rows for the same school (assume one row for each schoolyear)

