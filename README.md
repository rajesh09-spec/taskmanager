# Learning Task Manager — Local Run & Troubleshooting

This repository contains a Node/Express backend and a React (Create React App) frontend for a simple task management app with teacher/student roles.

## Prerequisites
- Node.js (v16+ recommended)
- npm
- MongoDB (Atlas or local). Provide a `MONGODB_URI` in `server/.env` if using a remote DB.

## Quick start

1) Start the backend (in a terminal):

```powershell
Set-Location 'C:\Users\palle\OneDrive\Desktop\learning-task-manager\server'
$env:PORT=8001
npm run dev
```

Expected output: `MongoDB Connected` and `Server running on port 8001`.

2) Start the frontend (in a separate terminal):

```powershell
Set-Location 'C:\Users\palle\OneDrive\Desktop\learning-task-manager\client'
npm start
```

The client is configured to proxy API requests to `http://localhost:8001` (see `client/package.json`).

## API endpoints (examples)
- POST `/api/auth/register` — register (body: `name,email,password,role[,teacherId]`)
- POST `/api/auth/login` — login (body: `email,password`) — returns `{ token, role, userId }`
- GET `/api/tasks` — list tasks (role-based)
- POST `/api/tasks` — create task (teachers can assign tasks to students)

Example `Invoke-RestMethod` (PowerShell) to register a teacher:

```powershell
Invoke-RestMethod -Method Post -Uri http://127.0.0.1:8001/api/auth/register -Body (@{ name='Teacher'; email='teacher@example.com'; password='password'; role='teacher' } | ConvertTo-Json) -ContentType 'application/json'
```

If `localhost` fails, use `127.0.0.1` (IPv4) as shown above.

## Running server tests
From the `server` folder:

```powershell
Set-Location 'C:\Users\palle\OneDrive\Desktop\learning-task-manager\server'
npm test
```

Tests use an in-memory MongoDB; they do not require your production database.

## Troubleshooting connectivity
- If client requests show `Network error` or PowerShell reports `Unable to connect to the remote server`:
  - Ensure the server process is running and shows `Server running on port 8001`.
  - Check listeners and PIDs:

```powershell
netstat -ano | findstr :8001
```

  - If a PID is returned, inspect it:

```powershell
Get-Process -Id <PID>
```

  - Check TCP connectivity (IPv4 and hostname):

```powershell
Test-NetConnection -ComputerName 127.0.0.1 -Port 8001 -InformationLevel Detailed
Test-NetConnection -ComputerName localhost -Port 8001 -InformationLevel Detailed
```

  - If `127.0.0.1` works but `localhost` doesn't, your system resolved `localhost` to IPv6 (e.g., `::1`). Use `127.0.0.1` or adjust the hosts file.
  - If the server starts and immediately exits, paste the terminal logs here — common causes: missing `MONGODB_URI`, port conflict, or unhandled exceptions.

## Notes
- The client expects the backend to return `userId` and `token` on successful login.
- During development we use `nodemon` in the server for auto-reload.

If you want, I can also add a server-specific `README.md` inside `server/` or open this file for you to view. Tell me if you prefer that.
