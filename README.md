# **Cheatsheet**  
### **Docker**
*The affected docker is based on what directory you're in!*
* Spin up Docker:  
  docker compose up -d

* Spin down Docker:  
  docker compose down

* Spin down Docker AND delete volumes:  
  docker compose down -v

### **Git**
* Check current repo:  
  git remote -v

* Check current username:  
  git config user.name

* Set repo-specific username:  
  git config user.name "username"

* Check current e-mail:  
  git config user.email

* Set repo-specific e-mail:  
  git config user.email "e-mail"

### **Powershell**
* Filter ls search results (replace "citydna" with query term):  
  ls -Recurse -Filter "*citydna*.ipynb" | Sort-Object LastWriteTime -Descending | Select-Object Name, LastWriteTime

### **Python**
* Create virtual environment (replace second "venv" for different name, but the name doesn't actually matter):  
  python -m venv venv