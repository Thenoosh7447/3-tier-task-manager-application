# Task Manager - 3-Tier Application

A full-stack task management application with a React-based frontend, Node.js Express backend, and MongoDB database. This is a hands-on project where developers build the app, and DevOps engineers containerize and orchestrate it.

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    NGINX (Reverse Proxy)                 │
│                      (Port 80)                            │
└──────────────────┬──────────────────────────────────────┘
                   │
        ┌──────────┴──────────┐
        │                     │
    ┌───▼────┐          ┌────▼───┐
    │Frontend │          │ Backend │
    │Port 3000│          │ Port5000│
    └────┬────┘          └────┬────┘
         │                    │
    (Express Server)   (Express API)
         │                    │
    (Static HTML/CSS/JS)      │
                              │
                          ┌───▼──────┐
                          │ MongoDB   │
                          │Port 27017 │
                          └───────────┘
```

## Services

### 1. Frontend (port 3000)
- **Technology**: Node.js + Express
- **Files**: `frontend/`
- **Purpose**: Serves the task manager UI
- **Key Files**:
  - `server.js`: Express server
  - `public/index.html`: SPA with vanilla JavaScript
  - `package.json`: Dependencies

### 2. Backend API (port 5000)
- **Technology**: Node.js + Express + Mongoose
- **Files**: `backend/`
- **Purpose**: REST API for task operations
- **Key Files**:
  - `server.js`: Express API server with MongoDB integration
  - `package.json`: Dependencies
  - `.env`: Environment variables
- **API Endpoints**:
  - `GET /api/tasks` - Fetch all tasks
  - `POST /api/tasks` - Create a new task
  - `PUT /api/tasks/:id` - Update task (toggle completed)
  - `DELETE /api/tasks/:id` - Delete a task
  - `GET /api/health` - Health check

### 3. MongoDB (port 27017)
- **Database**: NoSQL database for task storage
- **Connection String**: `mongodb://mongodb:27017/taskdb`

### 4. Nginx (port 80)
- **Purpose**: Reverse proxy routing requests to frontend and backend
- **Routes**:
  - `/` → Frontend (port 3000)
  - `/api/*` → Backend (port 5000)

## Project Structure

```
app/
├── frontend/
│   ├── package.json
│   ├── server.js
│   └── public/
│       └── index.html
│
├── backend/
│   ├── package.json
│   ├── server.js
│   └── .env
│
└── nginx/
    └── nginx.conf
```

## Features

✅ Create, read, update, and delete tasks
✅ Mark tasks as completed/incomplete
✅ Real-time UI updates via API calls
✅ Clean, modern UI with gradient styling
✅ Error handling and status messages
✅ RESTful API design
✅ MongoDB persistence
✅ Nginx reverse proxy routing

## Development Mode (Local Testing)

### Prerequisites
- Node.js 16+
- MongoDB running locally or Docker

### Running Services Locally

**1. Backend**
```bash
cd backend
npm install
npm start
```

**2. Frontend**
```bash
cd frontend
npm install
npm start
```

**3. Access the app**
- Frontend: http://localhost:3000
- Backend API: http://localhost:5000/api/tasks
- Health: http://localhost:5000/api/health

## Handing Over to DevOps

As a developer, you've provided:

✅ Complete frontend (HTML + CSS + JS)
✅ Complete backend API (Express + Mongoose)
✅ Nginx configuration
✅ Clear separation of concerns
✅ Health check endpoint
✅ Environment variables

**DevOps Engineer, here's what you need to do:**

1. Create **Dockerfile** for each service (frontend, backend)
2. Create **docker-compose.yml** to orchestrate all services
3. Set up **volumes** for MongoDB persistence
4. Configure **environment variables** properly
5. Test the full stack integration
6. Optimize images (multi-stage builds)
7. Add **logging and monitoring** if needed

---

## Technology Stack

| Layer | Technology |
|-------|-----------|
| Frontend | Node.js + Express |
| Backend | Node.js + Express + Mongoose |
| Database | MongoDB |
| Reverse Proxy | Nginx |
| Orchestration | Docker Compose |

---

**Developer**: Builds the application
**DevOps Engineer**: Containerizes and orchestrates it using Docker & Compose
