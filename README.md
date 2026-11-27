<<<<<<< HEAD
# FA Workforce employee portal

A Next.js employee sign-in and registration app backed by PostgreSQL. Employee IDs use the format `FA` plus six digits, such as `FA123456`.

## What is included

- Employee signup with employee name, employee ID, and password. Public signup always creates the `employee` role.
- Responsive auth and dashboard UI styled with Tailwind CSS v4 utilities.
- Password login sends both roles to the shared `/dashboard` route.
- Signed, HTTP-only session cookies with a configurable timeout (default: 60 seconds), calculated with date-fns.
- Authentication checks in `proxy.ts` protect the shared dashboard; the server page reads the current database role before deciding which dashboard content to render.
- The shared dashboard shows employees their own account details and admins the employee directory and role totals.
- PostgreSQL `employees` table with database constraints for employee ID format and allowed roles.
- A private command for creating or updating an admin account. Role is never accepted from public signup or login input.

## PostgreSQL setup on Windows

The database and the `employees` table are separate: first create a database in PostgreSQL, then run this app's script to create the table inside it.

1. Install PostgreSQL and make sure its Windows service is running. You can use pgAdmin or SQL Shell (`psql`).
2. Copy the example environment file from PowerShell in this project folder:

   ```powershell
   Copy-Item .env.example .env
   ```

3. Edit `.env` and set the connection string. The database name at the end of the URL must be the database you create in the next step:

   ```dotenv
   DATABASE_URL=postgresql://postgres:YOUR_PASSWORD@localhost:5432/employee_portal
   SESSION_SECRET=replace-with-a-random-secret-at-least-32-characters-long
   ADMIN_EMPLOYEE_ID=FA000001
   ADMIN_EMPLOYEE_NAME=Portal Administrator
   ADMIN_PASSWORD=replace-with-a-strong-password
   ```

   If the PostgreSQL password contains URL-reserved characters such as `@`, `:`, `/`, `#`, or `%`, URL-encode the password in `DATABASE_URL`.

4. Create the database named `employee_portal` (or use the name you put in `DATABASE_URL`):

   - **pgAdmin:** right-click **Databases** → **Create** → **Database…**, enter the database name, then save.
   - **SQL Shell:** connect to the maintenance database named `postgres`, then run:

     ```sql
     CREATE DATABASE employee_portal;
     ```

5. In PowerShell, install the packages and create the table and role index:

   ```powershell
   npm install
   npm run db:init
   ```

   `npm run db:init` reads `database/schema.sql` and connects using `DATABASE_URL` from `.env`.

6. Create the first admin account, then start the app:

   ```powershell
   npm run admin:create
   npm run dev
   ```

Open `http://localhost:3000`. Employees can create their own employee accounts from the sign-in screen. Admin accounts are provisioned with the command above.

## Session timeout

Set `SESSION_TIMEOUT_SECONDS` in `.env` to control how long a login stays valid. The default is `60` seconds; for example, use `900` for 15 minutes or `28800` for 8 hours. Restart the Next.js server after changing `.env`.

At login or signup, `date-fns` calculates the expiry date. The app uses that date for both the signed JWT expiry and the browser cookie expiry. The server rejects the JWT after it expires. Logout clears the cookie immediately by setting `Max-Age=0` and an expiry date in the past. This is a fixed lifetime from login, not a sliding inactivity timer.

### Verify the table

From PowerShell, list the tables and inspect employee records (this query does not show password hashes):

```powershell
psql -h localhost -p 5432 -U postgres -d employee_portal -c "\dt"
psql -h localhost -p 5432 -U postgres -d employee_portal -c "SELECT employee_id, employee_name, role, created_at FROM employees;"
```

Replace `employee_portal` and `postgres` with your database and PostgreSQL user if you chose different names.

### Connection errors

- **`3D000` / database does not exist:** create the database whose name is at the end of `DATABASE_URL`, or correct the URL to match the database you created. This is the current connection error in this workspace.
- **`ECONNREFUSED`:** start the PostgreSQL Windows service and confirm the host and port (default `localhost:5432`).
- **`28P01`:** check the PostgreSQL username and password in `DATABASE_URL`; URL-encode reserved characters in the password.
- **`DATABASE_URL is not configured`:** make sure `.env` is in the project root and has a `DATABASE_URL=...` line.

## Roles

| Role | Access |
| --- | --- |
| `employee` | Shared dashboard with own account details |
| `admin` | Same dashboard route with employee directory and role totals |

The `proxy.ts` interceptor requires a signed-in session for `/dashboard` and sends old role-specific dashboard URLs there. The shared server page checks the current role in PostgreSQL and only loads the employee directory for admins, so a role change takes effect before the session expires. In Next.js 16, the old Middleware file convention was renamed to Proxy.

## Employee ID and password rules

- Employee ID must match `FA[0-9]{6}`: exactly eight characters, with uppercase `FA` followed by six digits.
- Signup requires an employee name between 2 and 80 characters.
- Password must be at least 10 characters and no more than 72 UTF-8 bytes. Passwords are stored as bcrypt hashes.

## Environment variables

| Variable | Required | Purpose |
| --- | --- | --- |
| `DATABASE_URL` | Yes | PostgreSQL connection string |
| `SESSION_SECRET` | Yes | Signs and verifies session cookies; minimum 32 characters |
| `SESSION_TIMEOUT_SECONDS` | No | Session lifetime in seconds; defaults to `60`, maximum `2592000` (30 days) |
| `ADMIN_EMPLOYEE_ID` | For admin setup | Admin ID, such as `FA000001` |
| `ADMIN_EMPLOYEE_NAME` | For admin setup | Admin display name |
| `ADMIN_PASSWORD` | For admin setup | Initial admin password |

Keep `.env` out of source control. `npm run admin:create` updates the configured admin's name and password if run again.
=======
# practice
>>>>>>> 16dcc4b6c3e8415cee7c7d5b0a9104aa93bbc703
