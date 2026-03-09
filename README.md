# **Cheatsheet**  
### **Useful commands that I'm never going to memorize**

### **Docker**
*The affected docker is based on what directory you're in!*
* Spin up Docker:
  docker compose up -d

* Spin down Docker:
  docker compose down

* Spin down Docker AND delete volumes:
  docker compose down -v

### **Powershell**
* Filter ls search results (replace "citydna" with query term):  
  ls -Recurse -Filter "*citydna*.ipynb" | Sort-Object LastWriteTime -Descending | Select-Object Name, LastWriteTime

### **Python**
* Create virtual environment (replace second "venv" for different name, but the name doesn't actually matter):
  python -m venv venv