# **Cheatsheet**  
### **Useful commands that I'm never going to remember**

### **Powershell**
* Filter ls search results (replace "citydna" with query term):  
  **ls -Recurse -Filter "*citydna*.ipynb" | Sort-Object LastWriteTime -Descending | Select-Object Name, LastWriteTime**