# Glossary

| Term | Definition |
| :-- | :-- |
| **Blacklist** | Trying to block specific words/characters. Not secure against SQLi. |
| **Concatenation** | Gluing strings together (e.g putting user input directly into a query). The root cause of this vulnerability. |
| **Database** | A structured collection of data (like our `users.db` file). |
| **Debug Mode (`debug=True`)** | A Flask setting for development that gives helpful errors. Never use in production. |
| **Flask** | A Python web framework used to make our website. |
| **Injection** | When untrusted input is interpreted as code instead of data. |
| **Localhost / 127.0.0.1** | Your own computer. The only place that this lab should ever be ran. |
| **ORM (Object Relational Mapper)** | A library that maps database tables to objects in code. Often uses parameterized queries by default. |
| **Parameterization / Prepared Statement** | Separating the SQL query structure from the data. The secure fix for SQLi. |
| **Payload** | Malicious input that is crafted to exploit a vulnerability. |
| **Placeholder (`?`)** | A symbol used in parameterized queries where data will be safely inserted. |
| **Query** | A request sent to a database (e.g `SELECT ... FROM ...`). |
| **SQL (Structured Query Language)** | The language used to communicate with databases. |
| **SQL Injection (SQLi)** | Injecting SQL code via user input due to string concatenation. |
| **String** | Text (e.g `"admin"`, `"password123"`). |
| **Terminal / Command Line** | A text based interface to run commands on your computer. |
| **Virtual Environment (`venv`)** | An isolated space to install packages for a specific project. |
| **Whitelist** | Only allowing specific, known good inputs. Can be okay for restriction, but isn't the fix for SQLi on its own. |
