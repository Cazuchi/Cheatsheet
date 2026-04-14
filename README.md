# **Cheatsheet**

### **Index**
* [BigQuery](#bigquery)
* [Docker](#docker)
* [Git](#git)
* [PowerShell](#powershell)
* [Python](#python)
* [SQL](#sql)
* [Miscellaneous](#miscellaneous)

### **BigQuery** 
* BigQuery timetravel. Bigquery stores snapshots of tables for 7 days. Use this to restore a BigQuery table to a previous snapshot: 
  ```sql
  CREATE OR REPLACE TABLE `your_project.your_dataset.your_table` AS
  SELECT * FROM `your_project.your_dataset.your_table`
  FOR SYSTEM_TIME AS OF TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 2 HOUR)
  ```

### **Docker**
*The affected docker is based on what directory you're in!*
* Spin up Docker:  
  `docker compose up -d`

* Spin down Docker:  
  `docker compose down`

* Spin down Docker AND delete volumes:  
  `docker compose down -v`

### **Git**
* Check current repo:  
  `git remote -v`

* Check current username:  
  `git config user.name`

* Set repo-specific username:  
  `git config user.name "username"`

* Check current e-mail:  
  `git config user.email`

* Set repo-specific e-mail:  
  `git config user.email "e-mail"`

* Just use git clone to initialize new repo instead of doing it in Powershell:  
  `git clone [repo link]`

* Check if local repo is up to date with online repo:  
  `git fetch` --> `git status`

### **PowerShell**
* Filter ls search results (replace "citydna" with query term):  
  `ls -Recurse -Filter "*citydna*.ipynb" | Sort-Object LastWriteTime -Descending | Select-Object Name, LastWriteTime`

* You can use `New-Item name.filetype` to create a new file, but you can also just try to access the file and PowerShell will create it if it doesn't exist:  
  `Code name.filetype`

* Copy a file, like if you want a temporary backup while refactoring (... xD):  
  `Copy-Item old_name.filetype new_name.filetype`

### **Python**
* Create virtual environment. The second "venv" is the name of the environment, which technically doesn't matter, but some programs (like VS Code) autodetect the environment IF it's named venv or .venv, so leave as default unless it's important to have a different name for the environment:  
  `python -m venv venv`

* When VS Code doesn't automatically register your python kernel in Jupyter Lab (shouldn't be a problem with [Project folder] > [venv folder] type folder structure, but just in case). The name in the command can be whatever. It doesn't actually matter, but display-name determines what's shown in VS Code.  
  `python -m ipykernel install --user --name=venv --display-name "Python (venv)"`

* For replacing values in a dataframe, use one of the following:  
  ```python
  df[col].map(DICTIONARY).fillna(VALUE) 
  """
  Make sure the dictionary's keys matches values in the column and the values are what you 
  want to replace them with. Fillna() is optional if you want to handle values that don't 
  match in a specific way. map() returns NaN values by default for missing matches.
  """
  ```

  OR

  ```python
  search_criteria = [
    dfm[col] == criteria1,
    dfm[col] == criteria2,
    dfm[col] == criteria3,
  ]

  replacement_values = [
    replacement_value1,
    replacement_value2,
    replacement_value3
  ]

  df[col] = np.select(search_criteria, replacement_values, default=dfm[col]) 
  """ 
  The "default" parameter specifies the value to return for rows that don't match any of the 
  specified criteria. Point back to the original column to keep the original values or replace 
  with a custom value. The first criteria maps to the first replacement value and so on.
  Like a key mapping to a value in a dictionary, except these are just two lists.
  """
  ```

### **SQL**
* Basic CTE structure. Pick and choose which function are needed (Placeholder. Will go through and add explanations later):  
  ```sql
  WITH cte_name AS (
    SELECT
        t1.column1,
        t1.column2,
        t2.column3,
        t1.column4 AS "Named Column",
        'literal string' AS "Static Value",
        SUM(t1.column5) AS total_value,
        AVG(t1.column5) AS avg_value,
        COUNT(t1.column5) AS count_value,
        ROW_NUMBER() OVER (PARTITION BY t1.column1 ORDER BY t1.column2) AS row_num,
        SUM(t1.column5) OVER (PARTITION BY t1.column1 ORDER BY t1.column2) AS running_total
    FROM table1 t1
    INNER JOIN table2 t2
        ON t1.column1 = t2.column1
    LEFT JOIN table3 t3
        ON t1.column1 = t3.column1
    WHERE t1.column1 = 'value'
        AND t1.column2 > 100
    GROUP BY
        t1.column1,
        t1.column2,
        t2.column3
    HAVING SUM(t1.column5) > 100
  )

  SELECT *
  FROM cte_name
  WHERE row_num = 1
  ORDER BY column1 ASC
  ```

### **Miscellaneous**
* Backtick for code blocks:  
  `Shift + the key to the left of backspace`