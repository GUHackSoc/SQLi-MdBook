# Cheat Sheet

## Commands
| Command | Purpose |
| :-- | :-- |
| `py -m venv .venv` (Win) / `python3 -m venv .venv` (Mac/Linux) | Create virtual environment |
| `.\.venv\Scripts\activate` (Win) / `source .venv/bin/activate` (Mac/Linux) | Activate virtual environment |
| `pip install -r requirements.txt` | Install dependencies |
| `python init_db.py` | Initialize database |
| `python app.py` | Run the web server |
| `Ctrl+C` | Stop the server |
| `deactivate` | Deactivate virtual environment |

## Payloads (Only for this local lab)
| Payload | Username Field | Password Field | Result |
| :-- | :-- | :-- | :-- |
| Normal | `admin` | `supersecret123` | Success (fixed) |
| Comment Out | `admin' --` | `anything` | Bypasses (vulnerable only) |
| Always True | `' OR '1'='1' --` | `anything` | Bypases (vulnerable only) |

## Key Takeaway
```python
# VULNERABLE
query = f"SELECT ... WHERE username = '{username}' ..."
cur.execute(query)

# SECURE
query = "SELECT ... WHERE username = ? AND password = ?"
cur.execute(query, (username, password))
```

**Rule of thumb:** If you ever find yourself using f-strings or string concatenation to build SQL with user input, stop and use parameterized queries instead. 
