# Wrap Up, Ethics & Next Steps

## What we've accomplished
You did it! You went from building the lab, all the way to fixing the vulnerability. Let's recap: 

- **Understood the bug:** String concatenation allows user input to change SQL query structure.
- **Exploited it safely:** Saw how `--` and `OR '1'='1'` can bypass authentication in a local lab.
- **Tested incorrect fixes:** Proved that escaping, blacklisting, and whitelisting are not robust solutions.
- **Fixed it correctly:** Used parameterized queries to separate code and data, preventing SQLi.
- **Learned defense in depth:** Looked at hardening practices for the future.

## Ethics Reminder (Read again)
> [!WARNING]
> - **Only use this knowledge for good.** Find and fix vulnerabilities, don't exploit ones you don't have permission for. 
> - **Keep this local.** Do not host the vulnerable version online.
> - **Responsible disclosure:** If you ever find a vulnerability in a real system, report it through the correct, authorized channel.
> - **Follow the law:** Unauthorized testing of systems is illegal in most jurisdictions. 

## Cleanup (Optional)
If you're done with the lab, you can clean up. 

1. Stop the server with `Ctrl+C` 
2. Deactivate the virtual environment with `deactivate`
3. You can delete the entire `sqli-lab/` folder if you wish to remove it. The database (`instance/users.db`) and `__pycache__` will be deleted with it. 

## Next Steps To Learn More
If you want to expand your knowledge, these are all free and beginner friendly resources: 

| Resource | What it's good for |
| :-- | :-- |
| [OWASP SQL Injection](https://owasp.org/www-community/attacks/SQL_Injection) | The industry standard explanation and prevention methods. |
| [PortSwigger Web Security Academy](https://portswigger.net/web-security/sql-injection) | Interactive, in-browser labs that are incredibly well made. Start with their SQLi section! |
| [Flask Documentation](https://flask.palletsprojects.com/) | To learn more about Flask and expand this application. |
| [SQLite Documentation](https://www.sqlite.org/docs.html) | To learn more about the database that we used. |

## Final Challenge (Optional, but recommended)
Now that you know the fix, try to expand this lab yourself! 

- [ ] Add a `/fixed` route that explicitly uses parameterized queries (to have vulnerable and fixed side by side)
- [ ] Add password hashing using `werkzeug.security`
- [ ] Change the app to not display SQL errors to the user, only log them
- [ ] Add a new page that uses a `LIKE` query and fix it with parameterization (to see it works in different contexts)

You now have the foundation to do all of these safely! 

> [!SUCCESS]
> Great job completing this lab. Remember, the goal isn't to be able to exploit, it's to be able to build securely. 
