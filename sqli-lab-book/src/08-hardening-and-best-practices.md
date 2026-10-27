# Hardening & Best Practices

Parameterization fixes SQL Injection. However, a real login system needs more layers of defense. Here are beginner-friendly best practices to be aware of for future projects. 

> [!NOTE]
> We won't be implementing all of these into this lab (as to not overcomplicate it), however it's critical to know about them. 

| Practice | Why? | Beginner Example |
| :-- | :-- | :-- |
| **Hash passwords** | If your database ever gets leaked, plain text passwords are exposed to everyone. | Use `werkzeug.security.generate_password_hash()` and `check_password_hash()` when storing and verifying passwords. |
| **Don't leak SQL errors** | Showing `SQL Error: ...` to users can reveal information about your database structure (table names, schema, etc) to attackers. | Log the error to your server console, and show a generic error to the user (e.g "Something went wrong. Please try again."). |
| **Use the principle of least privilege** | The database user that your app connects with should only be able to do what it *needs* to do. | If your app only needs to read users for login, don't give it permission to `DELETE`, `DROP TABLE`, or create users. |
| **Rate limiting** | Prevents brute force attacks where someone tries to guess passwords over and over again. | Tools like `Flask-Limiter` can limit how many login attempts a user/IP can make within a time window. |
| **Use ORMs** | Object Relational Mappers (like SQLAlchemy) use parameterized queries by default and make it much harder to accidentally concatenate. | Great "next step" once you're more comfortable with Python and SQL. |
| **Consider MFA** | Multi-Factor Authentication adds another layer of security even if a password is guessed or leaked. | Beyond the scope of this lab, but incredibly important. | 

## Quick Exercise
Look at our fixed `app.py`. Find the line where we do `error = f"SQL Error: {e}"`. 

> [!WARNING]
> For a production application, change this to log the error server-side (e.g `print(e)` or a proper logger) and return something like `error = "An unexpected error occurred."` to the user. 

You don't have to change it for this local lab if you want to keep debugging easy, however it's good to be aware of this. 


## Checkpoint 6
- [ ] I know why plain text passwords are dangerous
- [ ] I know why exposing SQL errors is dangerous
- [ ] I know that parameterization isn't the *only* thing to do, it's the fix for *SQL Injection*
