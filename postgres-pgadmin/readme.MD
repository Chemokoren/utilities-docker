🧠 What’s going on

PostgreSQL (the database) is running on port 6543 (host side).

pgAdmin (the web UI) is running on port 5055 (host side).

So:

🔹 http://127.0.0.1:6543
 → ❌ won’t work (that’s the raw database port, not HTTP)
🔹 http://127.0.0.1:5055
 → ✅ will work (that’s pgAdmin’s web dashboard)

🚀 Correct way to access your setup
1️⃣ PostgreSQL (database)

This is not a web page, so you won’t open it in a browser.
You access it via a database client (like psql, pgAdmin, or your app).

Example:

psql -h 127.0.0.1 -p 6543 -U app_user -d app_db

2️⃣ pgAdmin (browser UI)

Open your browser and go to:

👉 http://127.0.0.1:5055

Then log in with:

Field	Value
Email	admin@domain.com
Password	admin123
3️⃣ Register your PostgreSQL server inside pgAdmin

Once logged in:

Click “Add New Server”

In the General tab → name it (e.g. Local Postgres)

In the Connection tab:

Host name/address: postgres
(this is the internal container name within Docker)

Port: 5432

Username: app_user

Password: strong_password_here

Save

Now you’ll see your database inside pgAdmin.