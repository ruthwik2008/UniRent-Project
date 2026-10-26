# UniRent — public website + PostgreSQL backend

This project contains the UniRent website and a Node.js/Express API. Accounts, item listings, booking requests, and wishlists are stored in PostgreSQL, not in a local SQLite file. The server creates its tables automatically on first start and adds sample listings to a new, empty database.

## Deployment overview

For a simple college-review demo, use:
- **GitHub** to store the project code.
- **Neon** to host the PostgreSQL database.
- **Render** to host the website and Node.js API at one public URL.

## 1. Create the PostgreSQL database on Neon

1. Create/sign in to an account at https://neon.com/.
2. Create a project named `unirent-db`.
3. On the project's Connect page, choose a PostgreSQL connection string. A pooled connection string is suitable for this small web app.
4. Copy the connection string and keep it private. Do not commit it to GitHub or share it publicly.

Neon's database is separate from the Render web service, so restarting or redeploying the website does not erase rows stored in PostgreSQL. Free plan quotas and policies can change; check your Neon dashboard for current limits. Free projects can pause when idle, so the first request after a period of inactivity might take a little longer.

## 2. Upload this project to GitHub

Extract the ZIP. Open the `UniRent_Project` folder and upload its contents to the root of a GitHub repository. The repository root must contain `server.js`, `package.json`, and the `unirent` folder. Do not upload `.env` or any database password.

## 3. Deploy on Render

1. Open https://dashboard.render.com/ and select **New → Web Service**.
2. Connect the GitHub repository containing this project.
3. Set **Build Command** to `npm install` and **Start Command** to `npm start`. Leave Root Directory blank when `server.js` and `package.json` are at the repository root.
4. In **Environment Variables**, add:
   - `DATABASE_URL` = the private PostgreSQL connection URL copied from Neon.
   - `JWT_SECRET` = a random private string at least 32 characters long.
   - `NODE_ENV` = `production`.
5. Select a plan and create the service. Render will give the app a public `onrender.com` URL when deployment succeeds.

Never put your real `DATABASE_URL` or `JWT_SECRET` in GitHub source files, screenshots, or public messages. If a secret is exposed, rotate it in the provider dashboard.

## 4. Verify it

Open your public Render URL. Then open `/api/health` after the URL, for example `https://your-app.onrender.com/api/health`. A successful response is:

```json
{"ok":true,"service":"UniRent API","database":"connected"}
```

Create a student account, add a listing, refresh the site, and confirm the listing is still visible. Use a second browser/account to test that the listing is shared. Booking and wishlist endpoints require sign-in.

## Local development

1. Install Node.js 20 or later.
2. Copy `.env.example` to `.env` and set `DATABASE_URL` to a PostgreSQL connection string (a Neon URL can also be used for local development).
3. Set `JWT_SECRET` to a private random string.
4. From this folder run `npm install`, then `npm start`.
5. Open http://localhost:3000.

The code uses `DATABASE_URL` and will not silently fall back to local storage or SQLite if the database is unavailable. This prevents the site from appearing to save data when it cannot reach its database.

## Data stored

- Account name, email, college, and salted password hash (not the plain-text password).
- Item title, category, price, owner, location, description, and optional image data.
- Booking item, renter, dates, duration, total, request message, and status.
- Wishlist entries linked to the signed-in student.

The sample listings are demo records only. Google OAuth, payments, email/SMS notifications, and production-grade marketplace security are not configured. This is a review/demo MVP, not a finished commercial marketplace.

## API routes

- `POST /api/auth/signup` and `POST /api/auth/login`
- `GET /api/me`
- `GET /api/items`, `POST /api/items`, `DELETE /api/items/:id`
- `GET /api/bookings`, `POST /api/bookings`
- `GET /api/wishlist`, `POST /api/wishlist/:id`, `DELETE /api/wishlist/:id`
- `GET /api/health`
