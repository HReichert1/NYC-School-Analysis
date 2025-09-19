# The tasks for the 2nd day have to be performed in a Jupyter notebook using Python
## 1. Downloaded the dataset
## 2. Opened a new Jupyter Notebook and 
- loaded the dataset using pandas
- cleaned the column names (lowercase, replaced spaces with _, removed special characters)
```
df.columns = df.columns.str.lower()

df.columns = df.columns.str.replace(" ", "_")

df.columns = [re.sub(r'[^A-Za-z0-9_]+', '', col) for col in df.columns] # Remove special characters

df.head()
```

## 3. Filtered the dataset to include only schools located in Brooklyn
```
filtered_df = df[df["borough"].str.contains("Brooklyn", case=False, na=False)]
filtered_df.head()
```
## 4. Answered the questions:
### 4.1 How many total schools are in Brooklyn?
```
unique_school_count = filtered_df["dbn"].nunique()
print(unique_school_count)
```
- 121 total schools
### 4.2 How many schools in Brooklyn offer Grade 9 entry? 
```
grade9_schools = filtered_df[
    (filtered_df["grade_span_min"] <= 9) &
    (filtered_df["grade_span_max"] >= 9)
]

# Count unique schools  
unique_school_count = grade9_schools["dbn"].nunique()
print(unique_school_count)

print(grade9_schools[["dbn", "grade_span_min", "grade_span_max"]].head(10))
```
- 121 schools offer Grade 9 entry
## 5. Grouped and summarized
### 5.1 Counted schools per borough
```
school_counts = df.groupby('borough')['dbn'].nunique()
print(school_counts)
```
### 5.2 Calculated average number of students per borough
```
school_average = df.groupby('borough')['total_students'].mean()
print(school_average)
```
### 5.3 Summarized "grade_span_max" grouped by borough
```
school_stat_grade = df.groupby('borough')['grade_span_max'].describe()
print(school_stat_grade)
```
## 6. Created visualization
- bar chart: number of schools per borough
```
import matplotlib.pyplot as plt  # import matplotlib for visualization

school_counts = df.groupby('borough')['dbn'].nunique()

# Create bar chart
plt.figure(figsize=(8,5))
school_counts.plot(kind='bar', color='green', edgecolor='black')
plt.title("Number of Schools per Borough")
plt.xlabel("Borough")
plt.ylabel("Number of Schools")
plt.xticks(rotation=45)
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.show()
```


## 7. Wrote a summary of key insights
- all schools in Brooklyn offer grade 9 entry
- Staten Island has the lowest number of schools, but the highest average of students
- Brooklyn has the highest number of schools
