# Day 4 task was to integrate a new dataset into an existing PostgreSQL database. It was required to understand the dataset structure, identify relevant columns, clean messy data and load it efficiently

## 1. the CSV file was loaded to a Jupyter Notebook and its structure was reviewed
## 2. the data was cleaned using Python
### 2.1 Duplicates were removed
```
new_df=new_df.drop_duplicates()
new_df 
```
### 2.2 Inconsistent formatting was removed
```
cols_to_convert = ["sat_critical_reading_avg_score", "sat_math_avg_score", "sat_writing_avg_score"]
new_df["num_of_sat_test_takers"] = new_df["num_of_sat_test_takers"].apply(pd.to_numeric, errors='coerce').astype('Int64')
new_df[cols_to_convert] = new_df[cols_to_convert].apply(pd.to_numeric, errors='coerce')

new_df.info()

new_df["pct_students_tested"] = new_df["pct_students_tested"].str.rstrip('%').astype('float')
new_df.describe()

```
### 2.3 Outliers were removed
```
cols_to_convert = [ "sat_critical_reading_avg_score", "sat_math_avg_score", "sat_writing_avg_score"]
for col in cols_to_convert: 
    new_df.loc[(new_df[col] < 200) | (new_df[col] > 800), col] = np.nan
new_df.describe()
```

### 2.4 Headers were normalized
```
new_df= df.copy()
new_df.columns = new_df.columns.str.replace(" ", "_")
new_df.columns = new_df.columns.str.lower()
new_df.columns = [re.sub(r'[^A-Za-z0-9_]+', '', col) for col in new_df.columns] # Remove special characters
new_df
```

### 2.5 Irrelevant columns were dropped

cols_to_drop = ["sat_critical_readng_avg_score", "contact_extension","internal_school_id"]
```
new_df = new_df.drop(cols_to_drop, axis=1)
new_df.info()
```
## 3 A Python script was written to append the data
```
DATABASE_URL = (
    "postgresql+psycopg2://neondb_owner:npg_CeS9fJg2azZD"
    "@ep-falling-glitter-a5m0j5gk-pooler.us-east-2.aws.neon.tech:5432/neondb"
    "?sslmode=require"
)

# Create engine and establish connection
engine = create_engine(DATABASE_URL)

new_df.to_sql(
    name='heike_reichert_sat_results',       
    con=engine,     
    schema='nyc_schools',
    if_exists='replace',    
    index=False            
)
```
