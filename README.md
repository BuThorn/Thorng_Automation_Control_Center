# Thorng Automation – Control Center

Starter full-stack project:
- Frontend: React + Vite + Tailwind CSS + Axios + Lucide
- Backend: FastAPI + SQLAlchemy + MySQL
- Authentication: password hashing + expiring JWT bearer tokens
- Roles: USER / ADMIN
- Telegram user account connection: Telethon phone-code + optional 2FA flow
- Telegram actions: one-to-one message and authorized channel post; no bulk sender

## 1. Requirements
Install Python 3.11+ and Node.js 20.19+ or 22.12+; install MySQL Server and create a database.

```sql
CREATE DATABASE thorng_automation CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

## 2. Backend setup (Windows PowerShell)
```powershell
cd backend
py -m venv venv
.\venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
Copy-Item .env.example .env
```
Edit `backend/.env` and set your MySQL credentials, a long random `SECRET_KEY`, and Telegram API credentials from your own Telegram developer account. Do not share `.env` or Telegram session files.

Run:
```powershell
uvicorn app.main:app --reload
```
API docs: http://127.0.0.1:8000/docs

The tables are created automatically for local development. For production, use migrations and a dedicated database user.

## 3. Frontend setup (new PowerShell terminal)
```powershell
cd frontend
npm install
Copy-Item .env.example .env
npm run dev
```
Open the URL printed by Vite (usually http://localhost:5173).

## 4. Create the first admin
Register an account in the UI. Then, from MySQL Workbench, promote that account manually:
```sql
UPDATE users SET role='ADMIN' WHERE email='your-email@example.com';
```
Do not ship a hard-coded default admin password.

## 5. Telegram connection
Get your own `api_id` and `api_hash` from Telegram's official API development page, then add them to `.env`.
In the dashboard, enter your own phone number, request the login code, and submit the code. If 2FA is enabled, submit the 2FA password when prompted. Never enter Telegram login codes or passwords into an untrusted site.
Telethon session files are sensitive credentials: protect the `backend/sessions` folder and never upload it to GitHub.

To interactively test connecting your own Telegram account against a remote API, run this from `backend`:
```powershell
.\venv\Scripts\python.exe .\test_api_suite.py --telegram-connect --base-url https://your-api.example.com
```
The script prompts locally for Thorng credentials, your Telegram phone number, and the Telegram login code; it prompts for a 2FA password only when required. Run it only against an API deployment you trust. The normal API test suite remains the default when `--telegram-connect` is omitted.

The starter supports individual messages and channel posts only. Use it only for recipients who expect your messages and channels where your account has permission to post. No mass messaging, scraping, or unsolicited outreach is included.

## Notes
- This is a local development starter, not a production security audit.
- Add HTTPS, rate limits, CSRF/CORS review, secret management, monitoring, database migrations, and encrypted session storage before deployment.
- JWTs are held in sessionStorage in this starter; production deployments should evaluate a secure HttpOnly cookie architecture.
