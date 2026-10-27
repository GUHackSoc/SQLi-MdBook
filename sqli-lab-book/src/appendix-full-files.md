# Full File Reference

For your convenience, here are all of the files referenced throughout the book in one place. Use this to verify that your files match exactly. 

> [!NOTE]
> All of these are for the *vulnerable* version that we built in chapter 4. Remember to replace `app.py` with the fixed version from chapter 8 after testing! 

## `sqli-lab/requirements.txt`
```txt
Flask==3.0.3
gunicorn==23.0.0
```

## `sqli-lab/.gitignore` (Recommended)
```txt
__pycache__/
*.pyc
*.db
instance/
.env
venv/
.venv/
```

## `sqli-lab/init_db.py`
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

cur.execute("DELETE FROM users")

cur.execute("INSERT OR IGNORE INTO users (username, password) VALUES (?, ?)", ("admin", "supersecret123"))
cur.execute("INSERT OR IGNORE INTO users (username, password) VALUES (?, ?)", ("test", "password123"))

conn.commit()
conn.close()

print(f"[+] Database initialized at {DB_PATH}")
print("[+] Users created: admin / test")
```

## `sqli-lab/app.py` (Vulnerable - For learning only)
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
    app.run(debug=True)
```

## `sqli-lab/app.py` (Fixed - Correct)
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

        query = "SELECT id, username FROM users WHERE username = ? AND password = ?"
        query_shown = query

        try:
            cur.execute(query, (username, password))
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
    app.run(debug=True)
```

## `sqli-lab/templates/login.html`
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
                <li><code>admin' --</code> in username, anything in password</li>
                <li><code>' OR '1'='1' --</code> in username, anything in password</li>
                <li><code>' OR 1=1 LIMIT 1 --</code> similar effect</li>
            </ul>
            <p>Watch the "Executed SQL Query" block to see how your input changes it.</p>
        </section>
    </main>
</body>
</html>
```

## `sqli-lab/static/css/style.css`
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
    border: 1px solid var(--border);
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
