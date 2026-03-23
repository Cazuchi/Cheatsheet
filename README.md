# **Cheatsheet**

### **Index**
* [Docker](#docker)
* [Git](#git)
* [PowerShell](#powershell)
* [Python](#python)
* [Miscellaneous](#miscellaneous)

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

### **Miscellaneous**
* Backtick for code blocks:  
  `Shift + the key to the left of backspace`