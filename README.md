# sqlite_database_operations

# Datasets Explored

I have imported the nyp and sbu import pricing database into a Pandas Dataframe. 
nyp contained columns such as: 'code__cpt_drg_', 'description', 'rev_code', 'inpatient_outpatient','gross_charges', 'discounted_cash_price', etc.
sbu contained columns such as: 'Code', 'Description', 'Type', 'Package/Line_Level', 'Gross charge','Discounted cash price', 'De-identified min contracted rate' etc. 

These columns were listed through codes ```print(nyp.columns)``` and ```print(sbu.columns)```

# Data Exploration and Analysis

The first part was to do basic exploratory analysis using Python, focusing on data distribution, missing values, and basic statistics.

1. ```nyp.describe()``` and ```sbu.describe()``` was used to find the statistics for numerical columns
2. ```print(nyp.columns)`` and ```print(sbu.columns)``` was used to find the columns lists
3. ```nyp['Gross Charges'].value_counts()``` and ```sbu['Gross charge'].value_counts()``` was used to find the frequency counts of each of the unique field in the database
4. ```nyp.isnull().sum()``` and ```sbu.isnull().sum()``` was used to check for missing values in the databases
5. ```nyp.columns = nyp.columns.str.strip()``` and ```sbu.columns = sbu.columns.str.strip()``` was used to remove leading and trailing white space from a column
6. Clean column names such as removing white spaces and special characters
 ```def clean_column_names(df):
    def clean_name(name):
        cleaned_name = re.sub(r'[^a-zA-Z0-9]', '_', name)
        return cleaned_name.lower()
    df.columns = [clean_name(col) for col in df.columns]
    return df
nyp_clean = clean_column_names(nyp_clean)```





# SQLite Database Operations Replication

1. importing packages: ```from sqlalchemy import create_engine``` and ```import sqlite3```
2. Creating a local database named health.db ```conn = sqlite3.connect('health.db')``` and ```c = conn.cursor()```  
3. Creating a table named data
   ```c.execute('''
    CREATE TABLE IF NOT EXISTS data (
        patient_name TEXT,
        gender TEXT,
        diagnosis TEXT,
        price REAL
    )
''')

conn.commit()```
4. Confirm that the table named data has been created within health.db
```c.execute('''
  SELECT name
  FROM sqlite_master
  WHERE type='table';
  ''')

c.fetchall()

#for value in c.fetchall():
#print(value)```

5. Inserting data:
```#inserting data
c.execute('''
    INSERT INTO data (patient_name, gender, diagnosis, price)
    VALUES
    ('Jacob Smith', 'Male', 'Heart Failure', 1000),
    ('Jasmine Smith', 'Female', 'Lung Cancer', 2000)
''')

conn.commit()```

6. Confirm that data has been added to the table
```c.execute('''
  SELECT * FROM data;
''')

print(c.fetchall())```


7.  Create an engine to connect to our sqlite DB: 
```engine = create_engine('sqlite:///health.db')```

8. Display the data in chart:
```data = pd.read_sql("select * from data;", conn)
data```

9. Use nyp dataset to put in SQLite database. Dataset is uploaded to "nyp1"
```df = pd.read_json('https://raw.githubusercontent.com/hantswilliams/HHA_504_2023/main/WK3/data/nyp/133957095_NewYorkPresbyterianHospital_standardcharges.json')
conn = sqlite3.connect('health.db')     # connect to the SQLite database```
df.to_sql('nyp1', conn, if_exists='replace')```

10.  Running the query and showing results:
```query = """
    SELECT *
    FROM nyp1
    WHERE "Rev Code" = '0260'
    LIMIT 5;
"""

response = pd.read_sql(query, conn)       #execute query in "response"

conn.close()

response```








