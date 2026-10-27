# Fixing It v2 (Parameterized Queries)

Now that we've proven why the incorrect fix failed, we'll apply the correct, robust fix. This is the standard that should be used in any application that talks to a database.

> [!IMPORTANT]
> The fix is to **stop concatenating user input into the query** and instead, **pass it separately** to the database driver to be safely parameterized.

## The Fix
We will be using `sqlite3`'s `?` placeholder for parameterized queries.

### Change `app.py`
Replace the entire vulnerable login logic with this correct version.

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

        # CORRECT: Use parameterized queries (prepared statements)
        # The SQL structure is fixed. User input is passed as data.
        query = "SELECT id, username FROM users WHERE username = ? AND password = ?"
        query_shown = query  # For learning purposes only, no user input is concatenated here

        try:
            # Pass the values as a tuple. The driver safely handles them.
            cur.execute(query, (username, password))
            user = cur.fetchone()
        except Exception as e:
            # For a lab this is okay to show, however see Chapter 9 for production advice
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

### What's different?
| Vulnerable | Correct |
| :-- | :-- |
| `query = f"... '{username}' ... '{password}' ..."` and `cur.execute(query)` | `query = "... ? ... ?"` and `cur.execute(query, (username, password))` |
| User input is glued into the SQL string. | User input is sent separately as parameters. The database treats it as data, not code. |
| Attackers can change the query's structure. | The query's structure is fixed. No matter what characters are put in, they cannot alter `WHERE`, add `OR`, or add comments to change logic. |

## Test the fix
1. Save `app.py`
2. Stop the server if it's running (`Ctrl+C`) and start it again (`python app.py`)
3. Go to [http://127.0.0.1:5000](http://127.0.0.1:5000)

### Test 1: Normal login still works
Login with `admin` / `supersecret123`. This should still succeed. The functionality is preserved!

### Test 2: The payloads now fail
Try `admin' --` with any password. It will fail with "Invalid username or password." 

Try `' OR '1'='1' --` with any password. It will fail.

### Observe the query box
The query box will now show:

```sql
SELECT id, username FROM users WHERE username = ? AND password = ?
```

Notice that there is *no* user input inside of it at all. The database is doing the substitution safely behind the scenes. This is correct!

### Bonus Test (Quotes are now safe!)
Try using a password with a single quote if you wanted to (for example, if you changed the database, but we can also just observe). With parameterization, quotes are treated as a normal character, not a string delimiter. This means our users can have safe, normal passwords without us breaking anything.


## Checkpoint 5
- [ ] I have replaced the vulnerable logic with parameterized queries
- [ ] Normal login (`admin`/`supersecret123`) still works
- [ ] `admin' --` no longer bypasses the login
- [ ] `' OR '1'='1' --` no longer bypasses the login
- [ ] The query shown is `... username = ? AND password = ?` with no concatenated user input
- [ ] I can explain why this prevents SQL Injection (data vs code)

> [!SUCCESS]
> Congratulations! You've fixed the root cause. This is the most important takeaway from this lab. Wherever user input meets a database query, you want to use parameterized queries (or a secure ORM that uses them under the hood).
