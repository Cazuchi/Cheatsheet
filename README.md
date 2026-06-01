# **Cheatsheet**

### **Index**
* [BigQuery](#bigquery)
* [Compute Engine VMs](#compute-engine-vms)
* [Crontabs](#crontabs)
* [Docker](#docker)
* [d-types](#d-types)
* [File extensions](#file-extensions)
* [Git](#git)
* [GitHub](#github)
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
  Actual lifesaver. Replace `INTERVAL 2 HOUR` with a time delta long enough to where you are sure that the original table existed.

### **Compute Engine VMs**
* GCP's compute engine VMs with Debian don't come with Git or Python venv preinstalled, but do come with Python preinstalled. Run the following to install Git and Python venv:  
  `sudo apt update && sudo apt install git`.  
  `sudo apt install python3-venv`.  
  Doublecheck Git and Python installations with `git --version` and `python3 --version`.

* The venv activation command is different on Linux too, compared to Windows. Use this:  
  `source venv/bin/activate`  

  For reference, this deactivates the currently active venv:  
  `deactivate`

  This deletes the venv folder:  
  `rm -rf venv`.  

  This creates a new venv:  
  `python -m venv venv`.  

* Debian doesn't come with Python 3.12.0 which can cause issues with pinned package numbers in a requirements.txt. Use this to strip the pins from a requirements.txt in this case and test if the script works without pinned packages versions:  
  `sed -i 's/[><=!~].*//' requirements.txt`  

  As a TEST, you can run it without the -i parameter first, which won't change the file and will instead just print the output in the terminal, so you can visually verify the change before commiting to saving it:  
  `sed 's/[><=!~].*//' requirements.txt`  

  To verify the content of requirements.txt afterwards:  
  `cat requirements.txt`  

### **Crontabs**
> [!TIP]  
> Use the `pwd` command in Debian to check the current directory.  
> Use the `ls` command to see files in the current directory. Also works in PowerShell on Windows.  

* Crontabs are used to schedule tasks, like running a script, on linux systems. Run `crontab -e` to edit the crontab file.  

  Crontab uses the format * * * * * to schedule tasks with the stars representing minutes, hours, day of month, month and day of week, respectively. Leaving a parameter as a * just means there's no rule for it, so the following would be the equivalent of running a script on the 10th, 20th and 28th of every month at 4 am:  
  `0 4 10,20,28 * *`.  
  The crontab will run on the VM's schedule, which, for Google Compute Engine, defaults to UTC, which is 2 hours behind Copenhagen time. So the above example would run at 6 am Copenhagen time. Check the VM timezone with `timedatectl`.  
  Crontab parameters can have multiple values seperated by a comma. All of `10,20,28` in the above example is related to the day of the month that the task runs on because the values are all together, seperated by a comma.  

  Python scripts will need the venv to run, so the format for actually running a script is `[time] [venv path to python.exe] [python script]`, like in this example:  
  `0 4 10,20,28 * * /home/mha/CityDNA/venv/bin/python3 /home/mha/CityDNA/main.py`.  

  **The NANO editor in Debian is weird.. Ctrl+X --> Y --> Enter to save and exit.**  
  Print out the crontab content with `crontab -l` to doublecheck.  

### **Docker**
*The affected docker is based on what directory you're in!*
* Spin up Docker:  
  `docker compose up -d`

* Spin down Docker:  
  `docker compose down`

* Spin down Docker AND delete volumes:  
  `docker compose down -v`

### **d-types**
* Excel doesn't remember d-types. Use Parquet instead! Especially for intermediate datasets while testing.  
  `df.to_parquet([FILENAME OR RAW PATH], index=False)` instead of `df.to_excel([FILENAME OR RAW PATH], index=False)`  
  `pd.read_parquet()` instead of `pd.read_excel()`  
  Unless it absolutely has to be used in excel afterwards.  

### **File extensions**
* Turn on file extensions in windows explorer's visual settings. You can change a file's extension without impacting the file at all. As in, you can take a .sav file, for instance. rename it to .sav.backup and Windows won't know how to open it, which can help prevent confusion if you need to have multiple files stored with similar names. Change it back to .sav at any time and it'll function as a normal .sav file.  

### **Git**
* **SETTING UP SSH access to GitHub** (easier for switching between accounts than HTTPS authentication):  
  
  Start by creating an SSH key in PowerShell:  
  `ssh-keygen -t ed25519 -C "email@email.com" -f "$HOME\.ssh\id_ed25519_personal"`.  
  
  Windows has a config file for SSH connections where we need to specify an alias and what host the key should be used to connect to:  
  ```  
  Host github-personal  
    HostName github.com  
    User git  
    IdentityFile C:\Users\mha\.ssh\id_ed25519_personal  
  ```  
  
  Then add the public SSH key to GitHub in the settings menu. Use `cat ~/.ssh/id_ed25519_personal.pub` to get the key. Copy and paste the entire return (3 elements) into GitHub SSH settings on the site.  
  
  Test the connection to GitHub with `ssh -T git@github-personal`.  
  
  Existing HTTPS connections to repos can be switched over to SSH using `git remote set-url origin git@github-personal:Cazuchi/reponame.git`.  
  
  New repos can have their SSH connection established using `git remote add origin git@github-personal:Cazuchi/reponame.git`.  
  
  Use `git remote -v` to double check that the project folder is connected to the correct GitHub repo.  
  
  Git doesn't use Windows' OpenSSH by default and might not read the SSH config initially. Use `git config --global core.sshCommand "C:/Windows/System32/OpenSSH/ssh.exe"` to ensure that it reads the correct SSH config file and can connect to GitHub. This only needs to be done once. It's a global setting that's saved afterwards.  

> [!CAUTION]
> Note: **The following is DANGEROUS. It WILL destroy your repo if you do it wrong**, but can be used to remove a file from a GitHub repo and the repo's entire history. 

> [!IMPORTANT]  
> The important thing is to remember `--invert-paths` in the first filter-repo command. That determines whether you're deleting a single file (the desired outcome) or everything in the repo EXCEPT that file.  

> [!NOTE]  
> This will REMOVE any changes you have made to the local repo, but haven't pushed yet. Keep this in mind, because if you're doing this to exclude a filetype, for instance, and the .gitignore addition hasn't been pushed before doing this, you're going to be repeating this entire process again. Ask me how I know, lol...  
* **Removing a file from your GitHub repo's history**  
  It's a pip install, so navigate to the local repo folder, activate the venv and run `pip install git-filter-repo`.  
  This example uses the filename `move-data-bq.ipynb` as an example.  
  The filter-repo package heavily encourages you to start from a freshly cloned local repo, because the file technically still exists in your local repo history until garbage cleanup, which takes 30 days unless you do it manually or modify the settings.  
  
  **THIS IS THE DANGEROUS PART!** If you don't include `--invert-paths` filter-repo will remove anything BUT the file you specify. `--invert-paths` inverts that to remove ONLY the specified file, but forgetting that parameter WILL DELETE most of your repo.  
  Run `git filter-repo --path move-data-bq.ipynb --invert-paths` to remove the file from the repo and the repo's history. This will DELETE the file, so make a backup, if you need to keep it. If you're not starting from a freshly cloned repo it will refuse to run the command. Add `--force` at the end to run it anyway.
  If you run this on a local repo that isn't a fresh clone, it'll like be unable to delete some objects related to the file's history, because windows locks them. Just reply `n` to each. There will be alot, potentially. Git garbage cleanup will remove them eventually. 

  Use `git log --oneline --all -- move-data-bq.ipynb` to check if the removal was successful. This should return nothing.     

  Add the file to your .gitignore, either as the full filename or `*.ipynb` if you don't want any of those filetypes included in the future.  

  As a safety feature, filter-repo removes your git origin. Add it back with `git remote add origin [repo]`.  

  `git push origin --force --all` to push the updated history to your GitHub repo. `--force` is needed because GitHub will refuse to push otherwise since your local history doesn't align with the GitHub repo's history.  

* MAKE THIS A HABIT every morning!  
  `git remote -v` check the repo  
  `git branch` check what branch I'm on. `git checkout [branch]` to switch  
  `git fetch` to get the newest version from github  
  `git status` check if the local repo is up to date  
  `git pull` if it isn't  

* Check current repo:  
  `git remote -v`

* Check current username:  
  `git config user.name`

* Set repo-specific username:  
  `git config user.name "username"`

* Set global username:  
  `git config --global user.name "username"`

* Check current e-mail:  
  `git config user.email`

* Set repo-specific e-mail:  
  `git config user.email "e-mail"`

* Set global e-mail:  
  `git config --global user.email "e-mail"`

* Just use git clone to initialize new repo instead of doing it in Powershell:  
  `git clone [repo link]`

* Check if local repo is up to date with online repo:  
  `git fetch` --> `git status`

### GitHub
GitHub has a set of highlights that you can use in markdown files to highlight specific parts. All of them require `>` infront of each line - that is what tells GitHub what range of your text should be highlighted with the given highlight syntax. **These have to be on the top-level of the markdown text. As in, they cannot be nested under a bulletpoint.**  

Caution:  
> [!CAUTION]  
> Use this to highlight critical notes, like commands that will irreversibly damage your project if implemented incorrectly or carelessly.  

Warnings:  
> [!WARNING]  
> Similar to the above, but for less dangerous commands.  

Important:  
> [!IMPORTANT]  
> For important notes.  

Tips:
> [!TIP]  
> For useful tips.  

Notes:  
> [!NOTE]  
> For regular notes.  

### **PowerShell**
* IF PowerShell won't save the $PROFILE, it's because you need to create it first manually. Run this to create it:  
  `New-Item -ItemType Directory -Force -Path (Split-Path $PROFILE)`

* Sublime Text can be used to open textfiles from PowerShell, but the program has to be added to the environmental variables first. In windows, hit the windows key, search for environmental variables and add the path to the Sublime text folder as a new line in the PATH variable.

* By DEFAULT PowerShell doesn't allow for the execution of scripts in the terminal. Run this to fix it (slightly more lenient script policy, standard for programming):  
  `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`

* To make PowerShell open in a given directory by default open the $PROFILE in a texteditor:  
  `subl $PROFILE`  
  Then add this line:  
  `Set-Location *path*`  

* Add pastel yellow timestamp and baby blue venv tag color in the PowerShell output:  
  `subl $PROFILE` to open the PowerShell settings. `New-Item -ItemType Directory -Force -Path (Split-Path $PROFILE)` to create it first, if the settings file doesn't exist, then:  
  ```powershell
  function prompt {
    $venv = if ($env:VIRTUAL_ENV) { "`e[38;5;153m($(Split-Path $env:VIRTUAL_ENV -Leaf))`e[0m " } else { "" }
    "[`e[38;5;229m$(Get-Date -Format 'HH:mm:ss')`e[0m] $venv PS $($executionContext.SessionState.Path.CurrentLocation)> "
    }
  ```   
  `. $PROFILE` to load the new PowerShell settings without having to restart the terminal.  

* Filter ls search results (replace "citydna" with query term):  
  `ls -Recurse -Filter "*citydna*.ipynb" | Sort-Object LastWriteTime -Descending | Select-Object Name, LastWriteTime`

* You can use `New-Item name.filetype` to create a new file, but you can also just try to access the file and PowerShell will create it if it doesn't exist:  
  `Code name.filetype`

* Copy a file, like if you want a temporary backup while refactoring (... xD):  
  `Copy-Item old_name.filetype new_name.filetype`

### **Python**
* Create virtual environment. The second "venv" is the name of the environment, which technically doesn't matter, but some programs (like VS Code) autodetect the environment IF it's named venv or .venv, so leave as default unless it's important to have a different name for the environment:  
  `python -m venv venv`

* Check the path to the ACTIVE virtual environment to confirm which is being used (like when you have multiple venvs called "venv"):  
  `$env:VIRTUAL_ENV`  

* Dump all installed packages to a requirements.txt files (includes specific version numbers):  
  `pip freeze > requirements.txt`.  

  If you DON'T want pinned versions, use pipreqs instead or run this in PowerShell to strip version numbers:  
  `pip freeze | ForEach-Object { $_ -replace '[><=!~].*', '' } | Out-File requirements.txt`.  

* Install all packages from a requirements.txt file (-r specifies that you want pip to read from a file. The filename doesn't matter - the structure of the file does. Requirements.txt is the norm tho'):  
  `pip install -r requirements.txt`

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
* Basic CTE structure. Pick and choose which function are needed (Placeholder. Will go through and add explanations later). See my [F1 Ergast project](https://github.com/Cazuchi/F1-ergast-data-SQL-project/blob/main/Analysis.sql) for implemetation examples:  
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

* JOIN statements - THIS APPLIES TO PANDAS AS WELL. The DIRECTION of the join statement specifies which table is protected. LEFT and RIGHT joins respectively keep all of the rows in the LEFT or RIGHT table, but drops any row from the other table without a match. INNER only keeps rows that match ACROSS BOTH TABLES. FULL OUTER JOIN (just "outer" in Pandas) protects BOTH tables and includes rows that DO NOT have a match across the two tables. In practice, RIGHT joins are pointless - just memorize the LEFT join function and switch the order of the tables depending on which needs protection.

### **Miscellaneous**
* Backtick for code blocks:  
  `Shift + the key to the left of backspace`