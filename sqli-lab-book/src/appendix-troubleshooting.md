# Troubleshooting

| Issue | Possible Cause | Fix |
| :-- | :-- | :-- |
| `python` or `py` not found | Python not installed or not added to PATH (Windows) | Reinstall Python and check `Add python.exe to PATH`. Try `py` on Windows, `python3` on macOS/Linux. |
| Can't activate venv on Windows (script execution disabled) | PowerShell's execution policy is blocking scripts | Open PowerShell as your user and run: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`. Then try activating again. Only do this for your user account. |
| `ModuleNotFoundError: No module named 'flask'` | Virtual environment not activated, or packages not installed | Activate your venv (`(.venv)`) and run `pip install -r requirements.txt` again. |
| Port 5000 already in use | Something else is running on port 5000 | Close the other program, or run Flask on a different port: `flask run --port 5001`. Remember to go to the new port in your browser. |
| Database errors | Corrupted or missing database | Delete `instance/users.db` and run `python init_db.py` again. |
| Page doesn't load / connection refused | Server not running | Make sure the terminal with `python app.py` is still running and hasn't crashed. Check for errors in that terminal. |
| Changes aren't showing up | Browser cache or server not restarted | Hard refresh your browser (`Ctrl+Shift+R` or `Cmd+Shift+R`). If you changed `app.py`, restart the server with `Ctrl+C` and `python app.py` again. |
| Quotes being weird when copying payloads | Smart quotes from some documents | Always type the payloads exactly as shown in the book. Avoid copying from rich text documents, copy from the code blocks. | 
