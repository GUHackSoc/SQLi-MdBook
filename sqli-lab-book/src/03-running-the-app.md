# Running The App For The First Time

Now that we've built everything, let's initialize the database and run the app for the first time.

## 1. Initialize the database
Make sure that you are still in the `sqli-lab/` directory and your virtual environment is activated.

```bash
python init_db.py
```

You should see the following output:

```text
[+] Database initialized at instance\users.db  (Windows)
[+] Database initialized at instance/users.db   (macOS/Linux)
[+] Users created: admin / test
```

> [!TIP]
> If `instance/users.db` appears in your file explorer now, that's correct! Do not share this file or commit it anywhere public.

## 2. Start the web server
Now run the app:

```bash
python app.py
```

You should see output similar to this:

```text
 * Running on http://127.0.0.1:5000
 * Debug mode: on
```

> [!IMPORTANT]
> While this is running, your terminal is *locked* into this server. Do not close this terminal yet. If you need to run other commands, open a *new* terminal in VS Code and activate your virtual environment again.

## 3. Open it in your browser
Open your web browser and go to [http://127.0.0.1:5000](http://127.0.0.1:5000).

You should see the "SQL Injection Demo" login page, complete with the payload examples at the bottom.

## 4. Test a normal login
Try the valid credentials:

- Username: `admin`
- Password: `supersecret123`

You should be greeted with a success message and the "Executed SQL Query" box will display the query that was ran.

Now try logging in with incorrect credentials (e.g `admin` and `wrongpassword`). You should see "Invalid username or password."

## Checkpoint 3
- [ ] `init_db.py` ran successfully and created the database
- [ ] The server is running on `http://127.0.0.1:5000`
- [ ] I can login with `admin` / `supersecret123` and get a success message
- [ ] I can see the executed SQL query in the query box

> [!NOTE]
> To stop the server at any time, go back to the terminal that it is running in and press `Ctrl + C`.
