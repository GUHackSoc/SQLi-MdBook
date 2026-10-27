# Fixing It v1

For every attempt, make the change to `app.py`, restart the server (`Ctrl+C` and `python app.py` again), test your payloads, and document the result.

## Attempt 1: Blacklist dangerous keywords (`OR`, `--`, `;`, `UNION`)

*Idea:* If I block the words that are used in the payloads, they can't work.

### Change `app.py` (Revert attempt 1 first, or just change it)
Revert the `replace` lines first. Now add a check:

```python
        username = request.form.get("username", "")
        password = request.form.get("password", "")

        # INCORRECT FIX - DEMO ONLY
        blacklist = ["OR", "or", "UNION", "union", "--", ";", "#", "/*", "*/"]
        combined = username + password
        for word in blacklist:
            if word in combined:
                error = "Invalid input detected!"
                return render_template("login.html", error=error)
        
        conn = sqlite3.connect(DB_PATH)
        cur = conn.cursor()

        query = f"SELECT id, username FROM users WHERE username = '{username}' AND password = '{password}'"
        ...
```

### Test it
Try `' OR '1'='1' --` again.

You will get "Invalid input detected!" for this specific payload. However, there are ways to bypass this.

### Any ideas on how we might get around this?
Take some time, think about what exactly is being blocked and what might not be.
Maybe this fix isn't as safe as we first thought?

Wait here until we are all ready to move forward.