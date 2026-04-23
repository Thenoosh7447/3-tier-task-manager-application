# Task Manager 3-Tier App - Complete Project Overview

## 📦 What You're Getting

A complete, **production-ready 3-tier application** that's ready to be containerized by you as the DevOps engineer.

---

## 🎨 Application Overview

**Task Manager** - A full-stack web application where users can:
- ✅ Create tasks
- ✅ Mark tasks as complete/incomplete
- ✅ Delete tasks
- ✅ View all tasks in real-time

---

## 🏗️ Architecture Diagram

```
┌─────────────────────────────────────────────────────┐
│                   USER BROWSER                       │
│              (http://localhost:80)                   │
└────────────────────────┬────────────────────────────┘
                         │
                         ▼
           ┌─────────────────────────┐
           │   NGINX Reverse Proxy   │
           │      (Port 80)          │
           └────────┬────────────────┘
                    │
        ┌───────────┴──────────────┐
        │                          │
        ▼                          ▼
  ┌──────────────┐        ┌──────────────┐
  │   Frontend   │        │   Backend    │
  │ (Port 3000)  │        │  (Port 5000) │
  │              │        │              │
  │ Express.js   │        │ Express.js   │
  │ HTML/CSS/JS  │        │ MongoDB ORM  │
  └──────────────┘        └────────┬─────┘
                                   │
                                   ▼
                          ┌──────────────────┐
                          │    MongoDB       │
                          │   (Port 27017)   │
                          │    TaskDB        │
                          └──────────────────┘
```

---

## 📂 Project Structure

```
app/
│
├── 📄 README.md                    # Developer documentation
├── 📄 DEVOPS_HANDOFF.md           # Your DevOps task instructions
├── 📄 QUICK_REFERENCE.md          # Quick DevOps reference guide
│
├── 📁 frontend/
│   ├── 📄 package.json             # Dependencies (express)
│   ├── 📄 server.js                # Express server (Node.js)
│   ├── 📁 public/
│   │   └── 📄 index.html           # Full UI (HTML/CSS/JS)
│   └── 🐳 Dockerfile               # ← YOU CREATE THIS
│
├── 📁 backend/
│   ├── 📄 package.json             # Dependencies (express, mongoose)
│   ├── 📄 server.js                # Express API server
│   ├── 📄 .env                     # Environment variables
│   └── 🐳 Dockerfile               # ← YOU CREATE THIS
│
├── 📁 nginx/
│   ├── 📄 nginx.conf               # Reverse proxy config
│   └── 🐳 Dockerfile               # ← YOU CREATE THIS
│
└── 🐳 docker-compose.yml           # ← YOU CREATE THIS
```

---

## 🔴 WHAT'S PROVIDED (DEVELOPER SIDE)

### ✅ Frontend
- Complete Node.js Express server
- Beautiful, modern UI (HTML/CSS/JavaScript)
- Real-time task management interface
- Communicates with backend via fetch API
- No framework dependencies (vanilla JS)

### ✅ Backend
- Complete REST API with 5 endpoints
- MongoDB integration with Mongoose
- Full CRUD operations
- Error handling
- Health check endpoint

### ✅ Database
- MongoDB configuration ready
- Mongoose schema defined
- Connection string: `mongodb://mongodb:27017/taskdb`

### ✅ Reverse Proxy
- Complete Nginx configuration
- Routes `/` to frontend
- Routes `/api/*` to backend
- Proper headers and proxy settings

### ✅ Documentation
- README.md - Application overview
- DEVOPS_HANDOFF.md - Step-by-step DevOps instructions
- QUICK_REFERENCE.md - Quick lookup guide

---

## 🟢 WHAT YOU NEED TO CREATE (DEVOPS SIDE)

### 1️⃣ frontend/Dockerfile
```
Purpose: Containerize the Node.js frontend
Base: node:18-alpine
Install dependencies, expose 3000, run npm start
```

### 2️⃣ backend/Dockerfile
```
Purpose: Containerize the Node.js backend API
Base: node:18-alpine
Install dependencies, expose 5000, run npm start
```

### 3️⃣ nginx/Dockerfile
```
Purpose: Containerize Nginx reverse proxy
Base: nginx:alpine
Copy nginx.conf, expose 80
```

### 4️⃣ docker-compose.yml
```
Purpose: Orchestrate all 4 services
Services:
  - mongodb (database)
  - backend (API)
  - frontend (UI)
  - nginx (reverse proxy)
Networks: app-network
Volumes: mongodb-data
```

---

## 🔗 Service Dependencies

```
Startup Order (managed by depends_on):
1. MongoDB starts first
2. Backend waits for MongoDB, then starts
3. Frontend starts (no dependencies)
4. Nginx starts after frontend & backend

Communication:
- Frontend talks to Backend via Nginx proxy
- Backend talks to MongoDB via service DNS (mongodb:27017)
- Nginx routes based on URL path
```

---

## 🧪 Testing Workflow

After you create the 4 files:

```bash
# Step 1: Build
docker-compose build
# ✓ Should succeed with no errors

# Step 2: Start
docker-compose up -d
# ✓ All 4 services should be running

# Step 3: Verify MongoDB
docker-compose logs mongodb
# ✓ Should see "Waiting for connections"

# Step 4: Verify Backend
docker-compose logs backend
# ✓ Should see "Connected to MongoDB"
# ✓ Should see "Backend API running on port 5000"

# Step 5: Verify Frontend
curl http://localhost
# ✓ Should return HTML content

# Step 6: Test API
curl http://localhost/api/tasks
# ✓ Should return empty array: []

# Step 7: Create a Task
curl -X POST http://localhost/api/tasks \
  -H "Content-Type: application/json" \
  -d '{"title":"Test Task"}'
# ✓ Should return task object with _id

# Step 8: Verify Task Persists
curl http://localhost/api/tasks
# ✓ Should return array with 1 task

# Step 9: Browser Test
Open http://localhost in browser
# ✓ Should see Task Manager UI
# ✓ Should see the task you created
# ✓ Should be able to toggle/delete

# Step 10: Cleanup
docker-compose down -v
# ✓ All containers stopped, volumes cleaned
```

---

## 💡 Key DevOps Concepts You'll Practice

### 1. Containerization
- Writing efficient Dockerfiles
- Using lightweight `alpine` images
- Multi-layer image building

### 2. Orchestration
- Defining services in docker-compose
- Service discovery via DNS
- Container networking

### 3. Data Persistence
- Named volumes for databases
- Volume mounting

### 4. Environment Management
- Environment variables in docker-compose
- Configuration management

### 5. Reverse Proxying
- Nginx routing configuration
- Load distribution

### 6. System Integration
- Service dependencies
- Health checks
- Logging

---

## 📊 Technology Stack Summary

| Component | Tech | Port | Status |
|-----------|------|------|--------|
| Frontend Server | Node.js + Express | 3000 | ✅ Provided |
| Frontend UI | HTML/CSS/JavaScript | - | ✅ Provided |
| Backend API | Node.js + Express | 5000 | ✅ Provided |
| Database | MongoDB | 27017 | ✅ Provided (config) |
| Reverse Proxy | Nginx | 80 | ✅ Provided (config) |
| Containerization | Docker | - | 🔴 **YOU CREATE** |
| Orchestration | Docker Compose | - | 🔴 **YOU CREATE** |

---

## 🎯 Success Criteria

You'll know you've completed the project when:

- [ ] All 3 Dockerfiles build without errors
- [ ] docker-compose.yml is valid and complete
- [ ] `docker-compose up -d` starts all 4 services
- [ ] All services reach healthy state (view logs)
- [ ] Frontend loads at http://localhost
- [ ] You can create a task in the browser
- [ ] Task persists after refresh
- [ ] MongoDB data survives container restart
- [ ] `docker-compose down -v` cleanly stops everything

---

## 🚀 What's Next (After This Project)

Once you complete this containerization project, you could:

1. **Kubernetes**: Deploy with K8s instead of Docker Compose
2. **CI/CD**: Add GitHub Actions to build/push images
3. **Monitoring**: Add Prometheus + Grafana for metrics
4. **Logging**: ELK stack for centralized logging
5. **Load Testing**: Scale with multiple instances
6. **Infrastructure as Code**: Terraform for cloud deployment

---

## 📞 Quick Help

### "My Backend Can't Connect to MongoDB"
→ Check docker-compose logs backend
→ Verify MONGO_URI=mongodb://mongodb:27017/taskdb
→ Ensure mongodb service is running

### "Frontend Shows 'Cannot Reach API'"
→ Check Nginx logs
→ Verify upstream backend { server backend:5000; }
→ Ensure backend container name matches

### "Services Won't Start"
→ Run docker-compose ps to see status
→ Check docker-compose logs for error messages
→ Verify ports aren't already in use

### "Data Disappeared After Restart"
→ Check volumes: section in docker-compose.yml
→ Ensure mongodb-data volume is defined
→ Verify mongodb service mounts /data/db volume

---

## 📚 Files to Review (in order)

1. **Start Here**: `QUICK_REFERENCE.md` - Get the gist
2. **Then**: `DEVOPS_HANDOFF.md` - Detailed requirements
3. **Reference**: `backend/server.js` - Understand the API
4. **Reference**: `frontend/public/index.html` - Understand the UI
5. **Reference**: `nginx/nginx.conf` - Understand the routing

---

## ✨ Project Highlights

This project teaches you:

✅ Real-world full-stack architecture
✅ Container best practices
✅ Service orchestration
✅ Network design
✅ Data persistence
✅ DevOps workflow (separation of concerns)
✅ Production-ready configuration

---

## 🎓 By the End, You'll Have

1. **A containerized 3-tier app** working end-to-end
2. **Hands-on Docker experience** with multiple services
3. **DevOps portfolio project** to showcase
4. **Docker Compose mastery** for local development
5. **Foundation for Kubernetes** migration later

---

**Ready to become a DevOps engineer? Let's containerize! 🐳**
