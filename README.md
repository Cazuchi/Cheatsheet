# **Cheatsheet**
Basic commands like df.groupby() in Python and similar are not included in this document, because this is (almost) exclusively meant as a reference guide for troubleshooting steps and gotchas for different scenarios. Function names and general syntax is a 2 second look-up with a LLM or Google.  
>[!NOTE]
>This cheatsheet uses html tags to add search functionality as an alternate way to navigating the cheatsheet by the index below. Not all entries have tags, but they follow the structure `Category/Sub-category` and these are the available tags at this moment in time:  
>
><kbd>BigQuery/Timetravel</kbd> <kbd>Git/SSH-access</kbd> <kbd>Git/Deleting-a-file-from-history</kbd>  <kbd>Git/Splitting-repos</kbd> <kbd>Git/Morning-routine</kbd>  
><kbd>GoogleIAM/Limiting-access</kbd>  <kbd>PowerBI/Evaluating-criteria-per-category</kbd> <kbd>PowerBI/Z-levels</kbd> <kbd>PowerBI/Slicer</kbd> <kbd>Venv/Management</kbd>  <kbd>JOIN-statements/SQL-Pandas</kbd>  
>
>Tags are mostly used for longer entries that are less easily scanable when looking for a specific entry.  

### **Index**
* [BigQuery](#bigquery)
* [Compute Engine VMs](#compute-engine-vms)
* [Crontabs](#crontabs)
* [Docker](#docker)
* [d-types](#d-types)
* [Fabric notebooks](#fabric-notebooks)
* [File extensions](#file-extensions)
* [GeoJSON](#geojson)
* [Git](#git)
* [GitHub](#github)
* [Google IAM](#google-iam)
* [Mapshaper](#mapshaper)
* [PowerBi](#powerbi)
* [PowerShell](#powershell)
* [PyO3 extenstions](#pyo3-extensions)  
* [Python](#python)
* [Rust](#rust)  
* [SQL](#sql)
* [VS Code](#vs-code)
* [Miscellaneous](#miscellaneous)

### **BigQuery** 
* <kbd>BigQuery/Timetravel</kbd> BigQuery timetravel. Bigquery stores snapshots of tables for 7 days. Use this to restore a BigQuery table to a previous snapshot: 
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
> * Use the `pwd` command in Debian to check the current directory.  
>  
> * Use the `ls` command to see files in the current directory. Also works in PowerShell on Windows.  
>  
> * Use `crontab -l` to print the content of the crontab in the terminal.  
>  
> * Use `timedatectl` to print the VMs current time. Make sure to check if the VM is in the same timezone as you, because that will affect when it triggers. Especially if you're trying to test if the crontab runs successfully.  
>  
> * Add `>> /home/headoffice/CityDNA/cron.log 2>&1` in the crontab with the relevant path to have crontab print outputs from your script (print statements and errors) to a cron.log file that you can monitor. Otherwise crontab apparently defaults to e-mailing log entries to you, which won't work on a new VM without the e-mail service set up.  
>  
> * Use `cat /home/headoffice/CityDNA/cron.log` to print the current content of the cron.log file.  
> * Use `tail -f /home/headoffice/CityDNA/cron.log` to start monitoring the log file. Changes will be printed to the terminal. Use `Ctrl+C` to cancel the monitoring.  
> * Add `-u` between the venv and script paths in crontab to unbuffer your script. This just means that it'll print printouts to the cron.log file immediately. Otherwise Python buffers print statements in a 8kb file and only prints them to the log file once that buffer is filled up.  

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

### **Fabric notebooks**
* Fabric notebooks connect to a lakehouse. Under `Explorer` in the left hand side, choose `Data items` --> `Add data items` and choose the lakehouse that you want the notebook to be connected to.  
  This means that you **DO NOT** need lakehouse references when running spark commands.  

* Fabric notebooks require spark dataframes to interact with lakehouse tables.  
  Use `df = spark.createDataFrame(df)` to convert a pandas dataframe to a spark dataframe.  
  Use `df = df.toPandas()` to convert a spark dataframe to a pandas dataframe.  

* To create a new table in the connected lakehouse, use `df.write.mode('overwrite').saveAsTable('[TABLE NAME]')`. 
  If you're overwritting an existing table and the scema has changedm use `df.write.mode('overwrite').option('overwriteSchema', 'true').saveAsTable('[TABLE NAME]')` because spark, by default, tries to match the two schemas when overwritting an existing table with a new one.  

* To retrieve an existing table, use `df = spark.table('[TABLE NAME]')`. **Remember** that this will be a spark table. Convert to a pandas one if desired.  

* To merge new data into an existing table, overwriting existing values and adding new ones, use:  
  ```sql   
  df.createOrReplaceTempView("staging") --This assumes that you have a **SPARK** dataframe. Otherwise convert it first.  
  spark.sql("""
  MERGE INTO [INSERT EXISTING TABLE NAME] AS prod
  USING staging
  ON prod.[INSERT VARIABLE TO MATCH ON] = staging.[INSERT VARIABLE TO MATCH ON]
  WHEN MATCHED THEN UPDATE SET *
  WHEN NOT MATCHED THEN INSERT *
  """)
  ```   

* Spark uses backticks for column names instead of "" used in PostgreSQL, so creating a new table from an existing one will look like this in Spark:  
  ```sql  
  CREATE OR REPLACE TABLE copenhagen_card_surveyxact_respondents AS  
  SELECT `responde`, `s_152`, `s_368`, `s_3`, `card_len`, `order_id`, `passtoke`, `kids_inc`  
  FROM copenhagen_card_surveyxact_dataset   
  ```  

* Fabric notebooks comes with packages like Pandas and Requests preinstalled, but use `%pip install [PACKAGE NAME]` to install other packages.  

### **File extensions**
* Turn on file extensions in windows explorer's visual settings. You can change a file's extension without impacting the file at all. As in, you can take a .sav file, for instance. rename it to .sav.backup and Windows won't know how to open it, which can help prevent confusion if you need to have multiple files stored with similar names. Change it back to .sav at any time and it'll function as a normal .sav file.  

### **GeoJSON**  
* **Creating geographic polygons and measuring their area in km²**  
  Start by going to [geojson.io](https://geojson.io/) and drawing your polygons.  

  In the feature editor at the bottom center of the screen, open the editor and add a property called "Name". This will add it to all polygons that you create. Fill it out for all polygons.  

  When you have drawn all of the polygons, export the .geojson file without truncating the coordinates.  

  Upload the geojson file to [mapshaper](https://mapshaper.org/) and run this command in the console, which will add a property with an estimated size of the area in km²: `each 'area_km2=$.area / 1e6'`  

  Download that file and run it through this Python script to get a simple overview of each area's name and size:  
  ```python  
  import json  
  
  with open(r"C:\[path]\POIs with area size.json") as f:  
    data = json.load(f)  

  for i in data['features']:  
    print(f"Name of area: {i['properties']['Name']}, size of area: {round(i['properties']['area_km2'], 2)} km²")  
  ```  

  This will create a printout similar to the following, which can then be customized however you want to:  

  ```  
  Name of area: Stroeget, size of area: 0.04 km²  
  Name of area: Kongens Have, size of area: 0.12 km²  
  Name of area: Kongens Nytorv, size of area: 0.01 km²  
  Name of area: Nyhavn, size of area: 0.01 km²  
  Name of area: Bella_center, size of area: 0.06 km²  
  Name of area: Norreport_station, size of area: 0.01 km²  
  Name of area: Hovedbanegaarden, size of area: 0.02 km²  
  Name of area: Noerrebro_station, size of area: 0.0 km²  
  Name of area: Copenhagen_South_station, size of area: 0.02 km²  
  Name of area: Copenhagen_airport_station, size of area: 0.01 km²  
  Name of area: Botanical_Garden, size of area: 0.09 km²  
  Name of area: Helsingor_Station, size of area: 0.01 km²  
  Name of area: Helsingor_Center_og_Stengade, size of area: 0.02 km²  
  Name of area: Louisiana_Museum, size of area: 0.03 km²  
  Name of area: Roskilde_station, size of area: 0.02 km²  
  ```  

### **Git**
* <kbd>Git/SSH-access</kbd> **SETTING UP SSH access to GitHub** (easier for switching between accounts than HTTPS authentication):  
  
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
* <kbd>Git/Deleting-a-file-from-history</kbd> **Removing a file from your GitHub repo's history**  
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

* <kbd>Git/Splitting-repos</kbd> Extracting elements from a GitHub repo into a new repo
  Filter-repo can also be used if you want to extract elements from one repo and use them to build a new repo, like if you want to split a single repo in to multiple repos responsible for their own functionality.  
  Make sure you have filter-repo installed: `pip install git-filter-repo`.  
  Start by cloning the repo you want to extract elements from: `git clone [repo]`.  
  
  The structure of the command is `git filter-repo --path folder/ --path README.md --path-glob "*.py" --path-regex "^src/.*\.py$"`.  
  Every element reference starts with `--path` and these can be chained.  
  `--path folder/` copies a given folder.  
  `--path README.md` copies a specific file - in this case the readme.md.  
  `--path-glob "*.py"` is a pattern matching function - in this case it matches all .py files.  
  `--path-regex "^scripts/.*\.py$"` allows for regex searches, in this case all .py files in the scripts folder.  

  This deletes all unspecified files in the local repo rather than copying them to a new repo, so once this is done, change the name of the folder, point to a new repo with `git remote add origin repo` and push to that repo.  

* <kbd>Git/Morning-routine</kbd> MAKE THIS A HABIT every morning!  
  `git remote -v` check the repo  
  `git branch` check what branch I'm on. `git checkout [branch]` to switch  
  `git fetch` to get the newest version from github  
  `git status` check if the local repo is up to date  
  `git pull` if it isn't  

* If you forgot to pull the newest version of the remote repo before applying changes, you can use the `git stash` command to fix it.  
  `git stash` saves your changes, but removes them from the file.  
  Then run `git pull` to get up to date with the remote repo.  
  Then run `git stash pop` to reapply the saved changes and push them to the remote repo as normal.  

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

### **GitHub**
* GitHub has a set of highlights that you can use in markdown files to highlight specific parts. All of them require `>` infront of each line - that is what tells GitHub what range of your text should be highlighted with the given highlight syntax. **These have to be on the top-level of the markdown text. As in, they cannot be nested under a bulletpoint.**  

  The syntax is the following **without** the space after `>`:  
  `> [tag]` (!CAUTION, !WARNING, !IMPORTANT, !TIP or !NOTE)  
  `> text`

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

* Github supports HTML tags to an extend, which are used in this cheatsheet as my search tags (example <kbd>PowerBI/Slicer</kbd>). The structure is `<kbd>` followed by your text followed by `<\kbd>`.  

### **Google IAM**
> [!IMPORTANT]  
> As a general rule-of-thumb, aim to minimize permissions. So any given user or service account only has access to the specific functions or services that they need. Not out of lack of trust for the user (maybe sometimes depending on experience), but out of concern for if someone else manages to gain access to a given account. If that account's permissions are minimized, the potential damage is also minimized. 

* <kbd>GoogleIAM/Limiting-access</kbd> Limiting service account access to secrets:  
  Add `Secret Manager Secret Accessor` permission to your service account.  
  Go to Secret Manager in GCP, click on the secret and copy the path at the top with a structure like `projects/[project number]/secrets/[secret name]`.  
  Click `Add IAM condition` and add a condition for `name`, `starts with` and the secret path. `starts with` because the actually endpoint you're hitting when querying the secret is likely the secret path with `/version/latest` appended to it, to ensure you're getting the most up-to-date secret.

* <kbd>GoogleIAM/Limiting-access</kbd> Limiting Compute Engine VMs access to GCP:
  When setting up the VM, attach a service account with only the permissions needed for the VM to take the actions that you want, for instance:  
  `BigQuery User permissions` to allow for the creation of dataset and tables. `BigQuery Users` have limited access to existing datasets and tables, but `ownership level` access to datasets and tables that it creates itself.  
  `Secret Manager Secret Accessor permissions` to allow for the retrieval of, say, API keys or similar.  
  Enable access to **ALL** GCP APIs for the VM. The VM will still only have the permissions equal to the permissions of the service account that you attach, but needs access to the same APIs in the VM settings. You're technically setting the permissions twice, which is tedious, but necessary. In the end, the VMs actual permissions are controlled by the attached Service Account, so choose "All APIs" in the VM settings is fine.  

* Using `BigQuery User` level permissions for service accounts automatically grants `ownership level` access to the service account for datasets and tables that it creates itself, while limiting access to datasets and tables created by other users or service accounts.  

### **Mapshaper**
* Run `each 'area_km2=$.area / 1e6'` in the console to add a calculate property to each area with the estimated size in km2.  

* Run `-filter 'navn == "København" || navn == "Frederiksberg" || navn == "Dragør" || navn == "Tårnby"'` to extract a subset of the geographic areas in the uploaded file. `navn == "København"` should match the area's property.  

  Run `-dissolve` to combine the extracted areas into a single area.  

  Upload the file to geojson.io and clean up any small artifacts that might be in the dissolved boundary. Download and use the file.  

### **PowerBi**
* To get dynamic labels to be in the vertical center of card visuals, go to `format` --> `callout` --> `padding`, turn on individual padding and **ONLY** add padding on the bottom, like 10-15 pixels. Maybe add 5 pixels of padding on the left side too, so push it in a bit.  

* <kbd>PowerBI/Z-levels</kbd> **Check the damn z-levels on visuals!**  
  When editing interactions between elements on a page, if a visual isn't responding to changing interactions like you'd expect, check if the buttons belong to the correct visualization.  

  If you have, say, a matrix on z-level 1 and a textbox on z-level 2 above it, the buttons that appear for editing interactions can look like they belong to the matrix, but they will actually belong to the textbox and the matrix won't react to changing the settings.  

  Move the matrix to a higher z-level than the textbox around it or move the textbox out of the way temporarily and the buttons for choosing interactions for the matrix will appear.. jesus ..  

  If you hover over the edit interactions buttons on a visual it'll add a faint highlight to the visual that they belong to, which can help identify issues too.  

* <kbd>PowerBI/Slicer</kbd> Variables in synced slicers across pages are **PERMANENT**. In the sense that if you copy/paste your slicers to a new page, choose to sync them to other pages and then change what variable is shown in a given slicer, it will change the variable in that slicer on the other pages too, which is going to break visuals on the other pages. You **CAN** delete a slicer on the new page and it won't affect that same slicer on other pages. Then just create a new slicer with the new variable.  

* <kbd>PowerBI/Evaluating-criteria-per-category</kbd> When creating measures that are supposed to evaluate criteria on a per-category basis, make sure to "loop" over the categories in the measure. As an **example** this would be a way to evaluate criteria per destination in a measure (from a tourism dataset):  
  ```sql
  VAR return_value =
    SUMX(
        VALUES('Monthly_data'[Destination]),
        ...
  ```
  This evaluates whatever criteria you want for each unique `destination` in the `Monthly_data` table.  

* **Alternative to SELECTEDVALUE() for parameter tables.**  
  SELECTEDVALUE() can be used to apply a filter context when you are not working with a star schema where tables are connected through relationships. If a star schema is not possible, SELECTEDVALUE() + FILTER()  solves the issue of filtering a datatable inside of a measure by simply grabbing the chosen value in a slicer.  

  The more performant way is to use TREATAS() tho, like this:  
  ```sql   
  TREATAS(
    VALUES('TableX'[COLUMN]),  
    'TableY'[COLUMN]  
  )  
  ```
  VALUES() grabs whatever unique values are left in the column AFTER the filter context is applied and then filter the parametertable by that list of unigue values.  
    
  This is both more performant and allows for easy filtering based on slicers that allow for both single and multiple selections.    

### **PowerShell**
* Create a folder with `mkdir [folder name]`.  
  Delete a folder with `remove-item -Recurse -Force [folder name]`.  
  Check items in a directory with `ls`.  
  Move into a directory with `cd [folder name]`.  
  Move out of a directory with `cd ..`.  

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

### **PyO3 extensions**
* **INITIAL SETUP**  
  So for a project where you want to code and test in Rust and then eventually turn it into a PyO3 extension for Python, it makes sense to have a dual folder approach.
  Create your `.venv`.  
  `pip install maturin`.

  **THIS IS THE FOLDER STRUCTURE:**  
  * Main folder  
    * cargo.toml --create this manually  
    * .venv --Python virtual environment

    * Pure Rust folder  
    * cargo.toml --run `cargo init --lib`  

    * PyO3 folder
    * cargo.toml --run `maturn init`  
    * pyproject.toml --run `maturn init`  

  Use `cargo add [crate] --features [feature#1],[feature#2]` to add dependencies (called Crates) to your cargo.toml in each subfolder depending on what's needed.  

  The **TOP LEVEL** cargo.toml should look like this:  
  ```
  [workspace]
  members = ["[SUB FOLDER #1]", "[SUB FOLDER #2]"]
  resolver = "2"
  ```

### **Python**
* **TLDR for requests**  
  Requests is NOT a standard library. Pip install it.  

  **Authentication**  
  * Basic authentication is just `auth=("[USERNAME]", "[PASSWORD]")` in the requests call, but some APIs require you to grab a token from an auth endpoint first and sending that instead, in which case it's just a parameter in the url, like shown under GET requests below.  
  * Doing the above just tells the requests library to put the information inside the authorization header, which is the equivalent of defining the header yourself.  
  
  **GET requests**  
  * Used for retrieving data.  
  * Postman documentation will often include empty payload and headers variables. Ignore them. They are generally NOT used in GET requests.  
  * Parameters go in the url, with the structure usually being `endpoint?param#1&param#2` etc., but structure params as a dictionary and reference it as `params=params` in the requests call and the requests library will automatically format them correctly into the url before sending the request.  
  
  **POST requests**
  * Used for sending data.  
  * DOES use payloads and headers to send data to the server.  
  * Payload is either formatted as json (`json=payload` inside the request function call) or form-encoded (`data=payload`). Depends on what the API expects.  

  **Sidenote on consuming API responses**
  * `import io` with `pd.read_csv(io.StringIO(result.text))` wraps a raw string from an API response variable named `result` in a file-like object that Pandas can iterate over. It basically mimicks reading a file from disc, but with an API response instead.  
  * Other packages (not pandas) might expect a file-like object from bytes instead of a string, which can be done using `io.BytesIO(result.content)` instead.  
  * File-like just means it functions the same way a file read from the filesystem, with the same usable function-calls, like `.read()`, `.seek()` and `.tell()`. 
  * XML was created by the devil to torture programmers. Use xmltodict to navigate it (or ElementTree if you're absolutely forced to, lol).  
    * Sidenote, xmltodict returns a dict when tags only appear once and a list when tags appear multiple times. Still better than ElementTree for exploration, but worth checking for.  

* <kbd>Venv/Management</kbd> Create virtual environment. The second ".venv" is the name of the environment, which technically doesn't matter, but some programs (like VS Code) autodetect the environment IF it's named .venv, so leave as default unless it's important to have a different name for the environment:  
  `python -m venv .venv`

* <kbd>Venv/Management</kbd> Check the path to the ACTIVE virtual environment to confirm which is being used (like when you have multiple venvs called "venv"):  
  `$env:VIRTUAL_ENV`  

* <kbd>Venv/Management</kbd> Dump all installed packages to a requirements.txt files (includes specific version numbers):  
  `pip freeze > requirements.txt`.  

  If you DON'T want pinned versions, use pipreqs instead or run this in PowerShell to strip version numbers:  
  `pip freeze | ForEach-Object { $_ -replace '[><=!~].*', '' } | Out-File requirements.txt`.  

* <kbd>Venv/Management</kbd> Install all packages from a requirements.txt file (-r specifies that you want pip to read from a file. The filename doesn't matter - the structure of the file does. Requirements.txt is the norm tho'):  
  `pip install -r requirements.txt`

* <kbd>Venv/Management</kbd> When VS Code doesn't automatically register your python kernel in Jupyter Lab (shouldn't be a problem with [Project folder] > [venv folder] type folder structure, but just in case). The name in the command can be whatever. It doesn't actually matter, but display-name determines what's shown in VS Code.  
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

  OR  

  `df.replace(mapping_dict)` IF mapping_dict has the structure `{column_name : {original_value1 : replacement_value1, original_value2 : replacement_value2, ...}}`. df.replace just leaves the original value if no match is found, which is handy.  

* **SETS** and **FROZENSETS**
  So sets are useful for a lot of lookup operations, but normal sets are mutable, meaning they can be changed at will. Frozensets are immutable. Once you define them, they can no longer be changed and can therefore also be used as dictionary keys, for instance.  

### **Rust**
* To iteratively test cargo scripts, run `cargo test -- --nocapture` in the directory of the crate that you want to test (this is **important** if you're in a multi-crate project!).  
  The first `--` is essentially a wall. Everything before the wall is arguments send to the cargo compiler. Everything after the wall is sent to the test binary running the test.  
  By default Rust captures all outputs when a test is successful, meaning you see no output regardless of stdout (println!) and stderr (dbg!) functioncalls in the code. `--nocapture` cancels that behaviour and send stdout and stderr messages to the terminal, so you can see them.  

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

* <kbd>JOIN-statements/SQL-Pandas</kbd> JOIN statements - THIS APPLIES TO PANDAS AS WELL. The DIRECTION of the join statement specifies which table is protected. LEFT and RIGHT joins respectively keep all of the rows in the LEFT or RIGHT table, but drops any row from the other table without a match. INNER only keeps rows that match ACROSS BOTH TABLES. FULL OUTER JOIN (just "outer" in Pandas) protects BOTH tables and includes rows that DO NOT have a match across the two tables. In practice, RIGHT joins are pointless - just memorize the LEFT join function and switch the order of the tables depending on which needs protection.

### **VS Code**
* <kbd>Venv/Management</kbd> VS Code's autodetecting of interpreters for Jupyter notebooks is incredibly unreliable.  
  Use `Ctrl+Shift+P` to jump to the search bar, search for `Python: Select Interpreter` and manually navigate to the Python.exe inside `.\.venv\Scripts\`.  
  That's the only way I've found to consistently select an interpreter.  

* VS Code be default activates a venv when you launch it, but it's literally never the correct one, so `ctrl+shift+p` search for `Open user settings (JSON)` and paste in `"python.terminal.activateEnvironment": false`. That'll stop VS Code from auto-activating any venv on launch.  

### **Miscellaneous**
* Backtick for code blocks:  
  `Shift + the key to the left of backspace`