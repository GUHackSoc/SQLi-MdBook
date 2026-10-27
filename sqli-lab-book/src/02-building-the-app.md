# Building The Lab Application

Now for the fun part, building our app.

## 1. Create the project structure
In your terminal, create the folders and files for our project.

Create the following folder structure:

```text
    sqli-lab/
    ├── instance/     (Will hold our database file)
    ├── static/
    │   └── css/      (Styles for our page)
    └── templates/    (HTML for our page)
```

## 2. Create a Virtual Environment
=== "Windows (PowerShell)"
```powershell
python -m venv .venv
```

=== "macOS/Linux (Terminal)"
```bash
python3 -m venv .venv
```

This creates a `.venv` folder. This is our "clean room".

## 3. Activate the Virtual Environment
We must activate it every time we want to run this project in a new terminal.

=== "Windows (PowerShell)"
```powershell
.\.venv\Scripts\activate
```

=== "macOS/Linux (Terminal)"
```bash
source .venv/bin/activate
```

When activated, you should see `(.venv)` at the beginning of your terminal line. This means it is working!

> [!TIP]
> If you get an error on Windows about script execution being disabled, see the [Troubleshooting](./appendix-troubleshooting.md) section for the fix.

## 4. Create the requirements
Create a file named `requirements.txt` inside of `sqli-lab/`. Copy and paste this into it.

```txt
Flask==3.0.3
gunicorn==23.0.0
```

What is this? This tells Python which packages to install. Flask is the web framework that we are using to make our website.

Install them with the following command:

```bash
pip install -r requirements.txt
```

You should see output showing Flask and its dependencies being installed.


## 5. Create the files

> [!NOTE]
> Read the comments in the code! They are extremely important for this lab. We have marked the vulnerable parts very explicitly.

### `init_db.py`
Create this file in `sqli-lab/`. This creates our database and adds test users to it.

```python
import sqlite3
import os

DB_PATH = os.path.join("instance", "users.db")
os.makedirs("instance", exist_ok=True)

conn = sqlite3.connect(DB_PATH)
cur = conn.cursor()

cur.execute("""
CREATE TABLE IF NOT EXISTS users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    username TEXT NOT NULL UNIQUE,
    password TEXT NOT NULL
)
""")

# Clear old data for repeatable demos
cur.execute("DELETE FROM users")

# Seed a known user
cur.execute("INSERT OR IGNORE INTO users (username, password) VALUES (?, ?)", ("admin", "supersecret123"))
cur.execute("INSERT OR IGNORE INTO users (username, password) VALUES (?, ?)", ("test", "password123"))

conn.commit()
conn.close()

print(f"[+] Database initialized at {DB_PATH}")
print("[+] Users created: admin / test")
```

*Explanation:* This creates a table called `users` with `id`, `username` and `password`. It creates two test accounts for us to use.


### `app.py`
Create this file in `sqli-lab/`. This is our web application.

```python
import os
import sqlite3
from flask import Flask, render_template, request

app = Flask(__name__)
DB_PATH = os.path.join("instance", "users.db")

@app.route("/", methods=["GET", "POST"])
def login():
    error = None
    query_shown = None

    if request.method == "POST":
        username = request.form.get("username", "")
        password = request.form.get("password", "")

        conn = sqlite3.connect(DB_PATH)
        cur = conn.cursor()

        query = f"SELECT id, username FROM users WHERE username = '{username}' AND password = '{password}'"
        query_shown = query

        try:
            cur.execute(query)
            user = cur.fetchone()
        except Exception as e:
            error = f"SQL Error: {e}"
            user = None
        finally:
            conn.close()

        if user:
            return render_template("login.html", success=f"Logged in as {user[1]} (id={user[0]})", query=query_shown)
        else:
            if not error:
                error = "Invalid username or password."
            return render_template("login.html", error=error, query=query_shown)

    return render_template("login.html")

if __name__ == "__main__":
    # Only for local testing
    app.run(debug=True)
```
### `templates/login.html`
Create this file in `sqli-lab/templates/`.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SQLi Demo - Login</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='css/style.css') }}">
</head>
<body>
    <main class="container">
        <header>
            <h1>SQL Injection Demo</h1>
            <p class="warning">FOR EDUCATIONAL USE ONLY – Run locally in a lab environment.</p>
        </header>

        <section class="card">
            <h2>Login</h2>
            <form method="POST" action="/">
                <label for="username">Username</label>
                <input type="text" id="username" name="username" autocomplete="off" required>

                <label for="password">Password</label>
                <input type="text" id="password" name="password" autocomplete="off" required>

                <button type="submit">Login</button>
            </form>

            {% if error %}
                <div class="alert error">{{ error }}</div>
            {% endif %}

            {% if success %}
                <div class="alert success">{{ success }}</div>
            {% endif %}

            {% if query %}
                <details class="query-box" open>
                    <summary>Executed SQL Query (shown for learning)</summary>
                    <code>{{ query }}</code>
                </details>
            {% endif %}
        </section>

        <section class="card">
            <h2>Try these payloads (Only on this local lab)</h2>
            <ul>
                <li><code>admin' --</code> in username, anything in password (comment out password check)</li>
                <li><code>' OR '1'='1' --</code> in username, anything in password (always true)</li>
                <li><code>' OR 1=1 LIMIT 1 --</code> similar effect</li>
            </ul>
            <p>Watch the "Executed SQL Query" block to see how your input changes it.</p>
        </section>
    </main>
</body>
</html>
```

### `static/css/style.css`
Create this file in `sqli-lab/static/css/`.

```css
:root {
    --bg: #0f1026;
    --card: #1d1f33;
    --text: #f4f4f9;
    --subtle: #b8b8d1;
    --error: #ff5f5f;
    --success: #3fb984;
    --border: #2f315c;
}

* {
    box-sizing: border-box;
    font-family: system-ui, -apple-system, BlinkMacSystemFont, sans-serif;
}

body {
    margin: 0;
    background: radial-gradient(circle at top, #2a2d5a, var(--bg));
    color: var(--text);
    line-height: 1.6;
}

.container {
    max-width: 720px;
    margin: 0 auto;
    padding: 2rem 1.5rem;
    display: flex;
    flex-direction: column;
    gap: 1.5rem;
}

header {
    text-align: center;
}

header h1 {
    margin-bottom: 0.25rem;
}

.warning {
    color: #ffb86c;
    margin: 0;
}

.card {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 12px;
    box-shadow: 0 40px 60px -40px rgba(63, 185, 132, 0.25);
    padding: 2rem;
    display: flex;
    flex-direction: column;
    gap: 1rem;
}

.card h2 {
    margin: 0;
}

form {
    display: flex;
    flex-direction: column;
    gap: 0.8rem;
}

label {
    font-weight: 600;
}

input {
    padding: 0.7rem 0.9rem;
    border-radius: 8px;
    border: 1px solid transparent;
    background: rgba(15, 16, 38, 0.8);
    color: var(--text);
}

input:focus {
    outline: none;
    border-color: rgba(63, 185, 132, 0.8);
}

button {
    margin-top: 0.5rem;
    padding: 0.8rem;
    border-radius: 999px;
    border: none;
    background: linear-gradient(90deg, #3fb984, #22d3ee);
    color: var(--bg);
    font-weight: 700;
    cursor: pointer;
}

.alert {
    padding: 0.8rem 1rem;
    border-radius: 8px;
    border: 1px solid var(--border);
}

.error {
    background: rgba(255, 95, 95, 0.15);
    color: var(--error);
}

.success {
    background: rgba(63, 185, 132, 0.18);
    color: var(--success);
}

.query-box {
    background: rgba(15, 16, 38, 0.8);
    border-radius: 8px;
    border: input border 1px solid var(--border);
    padding: 1rem;
}

.query-box code {
    display: block;
    margin-top: 0.8rem;
    white-space: pre-wrap;
    word-break: break-word;
    color: var(--subtle);
}

ul {
    margin: 0;
    display: flex;
    flex-direction: column;
    gap: 0.6rem;
}

code {
    font-family: "Cascadia Code", "Fira Code", monospace;
}
```

## Checkpoint 2
- [ ] All files have been created in `sqli-lab/`
- [ ] I have activated my virtual environment (`(.venv)`)
- [ ] `pip install -r requirements.txt` has completed without errors
- [ ] I can see all of the files inside of VS Code
