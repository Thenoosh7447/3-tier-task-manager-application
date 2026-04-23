# DevOps Handoff - Task Manager 3-Tier App

## Overview
The development team has completed the 3-tier Task Manager application. Your job as DevOps engineer is to containerize it, orchestrate services, and prepare it for deployment.

---

## What You Need to Build

### 1. Dockerfiles (3 total)

#### Frontend Dockerfile Requirements
- **Base Image**: `node:18-alpine` (lightweight)
- **Working Directory**: `/app`
- **Steps**:
  1. Copy `package.json`
  2. Run `npm install`
  3. Copy application code
  4. Expose port 3000
  5. Start command: `npm start`
- **Build Context**: `./frontend`

#### Backend Dockerfile Requirements
- **Base Image**: `node:18-alpine`
- **Working Directory**: `/app`
- **Steps**:
  1. Copy `package.json`
  2. Run `npm install`
  3. Copy application code
  4. Expose port 5000
  5. Start command: `npm start`
- **Build Context**: `./backend`
- **Note**: MongoDB connection happens via environment variable

#### Nginx Dockerfile Requirements
- **Base Image**: `nginx:alpine`
- **Working Directory**: `/etc/nginx`
- **Steps**:
  1. Copy `nginx/nginx.conf` to `/etc/nginx/conf.d/default.conf`
  2. Expose port 80
- **Build Context**: `./nginx`

### 2. docker-compose.yml

#### Services to Define

**Service 1: MongoDB**
- Image: `mongo:latest`
- Container name: `mongodb`
- Ports: `27017:27017`
- Volumes: 
  - `mongodb-data:/data/db` (for persistence)
- Environment: 
  - `MONGO_INITDB_ROOT_USERNAME=admin` (optional)
  - `MONGO_INITDB_ROOT_PASSWORD=password` (optional)
- Networks: `app-network`

**Service 2: Backend**
- Build: `./backend`
- Container name: `backend`
- Ports: `5000:5000`
- Depends on: `mongodb`
- Environment Variables:
  - `MONGO_URI=mongodb://mongodb:27017/taskdb`
  - `PORT=5000`
  - `NODE_ENV=production`
- Networks: `app-network`
- Command: `npm start`

**Service 3: Frontend**
- Build: `./frontend`
- Container name: `frontend`
- Ports: `3000:3000`
- Depends on: `backend` (optional, for dependency ordering)
- Environment Variables:
  - `PORT=3000`
- Networks: `app-network`
- Command: `npm start`

**Service 4: Nginx (Reverse Proxy)**
- Build: `./nginx`
- Container name: `nginx`
- Ports: `80:80`
- Depends on: 
  - `frontend`
  - `backend`
- Networks: `app-network`
- Health Check (optional):
  - Test: `curl -f http://localhost/ || exit 1`
  - Interval: `30s`
  - Timeout: `10s`
  - Retries: `3`

#### Networks
- Create a custom bridge network: `app-network`
- All services should connect to this network

#### Volumes
- Named volume: `mongodb-data` for MongoDB persistence

---

## Key DevOps Considerations

### 1. Service Dependencies
- MongoDB must start first
- Backend must start after MongoDB (uses depends_on)
- Frontend can start anytime
- Nginx routes to frontend and backend

### 2. Environment Variables
- Backend needs `MONGO_URI` pointing to MongoDB
- Services need proper `PORT` configuration
- Use `.env` file or explicit environment in docker-compose

### 3. Networking
- All services must communicate via service names (Docker DNS)
- Nginx routes to `frontend:3000` and `backend:5000`
- Backend connects to `mongodb:27017`

### 4. Data Persistence
- MongoDB data persists in named volume `mongodb-data`
- Frontend and Backend are stateless

### 5. Ports
- Host → Container mapping:
  - Nginx: `80:80`
  - Frontend: `3000:3000` (optional, proxied via Nginx)
  - Backend: `5000:5000` (optional, proxied via Nginx)
  - MongoDB: `27017:27017` (optional, internal only)

---

## Testing Checklist

After creating Dockerfiles and docker-compose.yml:

- [ ] All services build without errors
- [ ] All services start with `docker-compose up`
- [ ] MongoDB initializes successfully
- [ ] Backend connects to MongoDB (check logs)
- [ ] Frontend loads at `http://localhost`
- [ ] Frontend can create a task
- [ ] Backend API responds at `/api/tasks`
- [ ] Nginx routes requests correctly
- [ ] Tasks persist in MongoDB
- [ ] All services stop cleanly with `docker-compose down`

---

## Commands You'll Need

```bash
# Build all services
docker-compose build

# Start all services
docker-compose up

# Start in detached mode
docker-compose up -d

# View logs
docker-compose logs -f

# Stop all services
docker-compose down

# Remove volumes (caution: deletes data)
docker-compose down -v

# Rebuild and restart
docker-compose up --build -d

# Check running containers
docker-compose ps

# Execute command in container
docker-compose exec backend npm start
```

---

## File Structure for DevOps

```
app/
├── frontend/
│   ├── package.json
│   ├── server.js
│   ├── public/
│   │   └── index.html
│   └── Dockerfile                  ← CREATE THIS
│
├── backend/
│   ├── package.json
│   ├── server.js
│   ├── .env
│   └── Dockerfile                  ← CREATE THIS
│
├── nginx/
│   ├── nginx.conf
│   └── Dockerfile                  ← CREATE THIS
│
├── docker-compose.yml              ← CREATE THIS
└── README.md
```

---

## Optimization Tips (Advanced)

### Multi-stage Builds
- For Frontend: Build stage (copy + npm install) → Runtime stage (node + app)
- For Backend: Same approach to reduce final image size

### Image Size Optimization
- Use `alpine` variants (smaller base images)
- Remove development dependencies in production
- Use `.dockerignore` to exclude unnecessary files

### Security
- Don't run containers as root
- Use `USER` directive in Dockerfiles
- Avoid hardcoding secrets (use environment variables)

### Health Checks
- Implement health check endpoints
- Configure health checks in docker-compose
- Nginx health check: `curl -f http://localhost/`

---

## What's Provided by Developers

✅ Working Node.js frontend (Express server + HTML/CSS/JS)
✅ Working Node.js backend API (Express + Mongoose)
✅ Nginx reverse proxy configuration
✅ Environment variables template (.env)
✅ Project README with architecture

## Your Deliverables (DevOps)

✅ Dockerfile for frontend
✅ Dockerfile for backend
✅ Dockerfile for Nginx
✅ docker-compose.yml orchestration
✅ Verified end-to-end testing
✅ Documentation on how to deploy

---

## Contact & Questions

If services won't start:
- Check logs: `docker-compose logs service-name`
- Verify network connectivity: `docker network inspect app-network`
- Check port conflicts: `netstat -tlnp | grep :PORT`
- Verify environment variables in docker-compose

Happy containerizing! 🚀
