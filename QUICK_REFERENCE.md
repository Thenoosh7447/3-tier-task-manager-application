# Quick Reference - Task Manager DevOps Setup

## 🎯 Your Mission (as DevOps Engineer)

Create the missing files to containerize this 3-tier app:
1. **3 Dockerfiles** (frontend, backend, nginx)
2. **1 docker-compose.yml** (orchestration)

---

## 📝 File Checklist

- [ ] `frontend/Dockerfile`
- [ ] `backend/Dockerfile`
- [ ] `nginx/Dockerfile`
- [ ] `docker-compose.yml` (in root)

---

## 🐳 Dockerfile Templates

### Frontend Dockerfile (frontend/Dockerfile)
```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

### Backend Dockerfile (backend/Dockerfile)
```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
EXPOSE 5000
CMD ["npm", "start"]
```

### Nginx Dockerfile (nginx/Dockerfile)
```dockerfile
FROM nginx:alpine
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
```

---

## 🔧 docker-compose.yml Key Structure

```yaml
version: '3.8'

services:
  mongodb:
    image: mongo:latest
    container_name: mongodb
    ports:
      - "27017:27017"
    volumes:
      - mongodb-data:/data/db
    networks:
      - app-network

  backend:
    build: ./backend
    container_name: backend
    ports:
      - "5000:5000"
    environment:
      MONGO_URI: mongodb://mongodb:27017/taskdb
      PORT: 5000
      NODE_ENV: production
    depends_on:
      - mongodb
    networks:
      - app-network

  frontend:
    build: ./frontend
    container_name: frontend
    ports:
      - "3000:3000"
    networks:
      - app-network

  nginx:
    build: ./nginx
    container_name: nginx
    ports:
      - "80:80"
    depends_on:
      - frontend
      - backend
    networks:
      - app-network

volumes:
  mongodb-data:

networks:
  app-network:
    driver: bridge
```

---

## 📊 Service Communication Map

```
User (Browser)
    ↓
    ↓ Port 80
    ↓
┌─────────────────┐
│      Nginx      │
└────────┬────────┘
         │
    ┌────┴────┐
    ↓         ↓
Frontend    Backend
:3000       :5000
    ↓         ↓
    └────┬────┘
         ↓
      MongoDB
       :27017
```

---

## ✅ Testing Steps

```bash
# 1. Build all containers
docker-compose build

# 2. Start all services
docker-compose up -d

# 3. Check if services are running
docker-compose ps

# 4. View logs (especially backend for MongoDB connection)
docker-compose logs backend

# 5. Test Frontend
curl http://localhost

# 6. Test Backend API
curl http://localhost/api/tasks

# 7. Try to create a task
curl -X POST http://localhost/api/tasks \
  -H "Content-Type: application/json" \
  -d '{"title":"Test Task"}'

# 8. View all tasks
curl http://localhost/api/tasks

# 9. Stop all services
docker-compose down

# 10. Clean up (remove volumes)
docker-compose down -v
```

---

## 🔑 Key Points to Remember

1. **Service Names are DNS**: Inside Docker, `mongodb:27017` works because Docker DNS resolves `mongodb` to the container IP
2. **Networks**: All services must be on `app-network` to communicate
3. **Port Mapping**: `80:80` means host port 80 → container port 80
4. **depends_on**: Controls startup order but doesn't wait for readiness
5. **Volumes**: `mongodb-data:/data/db` persists data even if container restarts
6. **Environment Variables**: Backend needs `MONGO_URI` to connect to MongoDB
7. **Nginx**: Acts as single entry point; routes `/` to frontend, `/api` to backend

---

## 🚀 Common Commands

```bash
# Fresh start
docker-compose down -v && docker-compose up --build -d

# Follow logs in real-time
docker-compose logs -f

# Get into a container
docker-compose exec backend sh

# Restart a single service
docker-compose restart backend

# View resource usage
docker stats

# Check network
docker network inspect app-network
```

---

## ❌ Troubleshooting

| Problem | Solution |
|---------|----------|
| Backend can't connect to MongoDB | Check `MONGO_URI` env var, ensure `mongodb` service is running |
| Frontend shows "Cannot reach API" | Check Nginx routing, ensure backend container name is correct |
| Port already in use | Change ports in docker-compose or kill the process using the port |
| Services won't start | Check `docker-compose logs service-name` for errors |
| MongoDB data lost after restart | Ensure `volumes:` section is configured correctly |

---

## 📚 Key Files Provided

| File | Purpose |
|------|---------|
| `frontend/server.js` | Express server serving HTML |
| `frontend/public/index.html` | Task UI |
| `backend/server.js` | REST API with MongoDB |
| `backend/.env` | Environment variables template |
| `nginx/nginx.conf` | Reverse proxy config |

---

## 🎓 Learning Outcomes

After completing this project, you'll understand:

✅ Writing Dockerfiles for different frameworks
✅ Multi-container orchestration with Docker Compose
✅ Service networking in Docker
✅ Environment variables in containerized apps
✅ Volume management for persistence
✅ Reverse proxy configuration (Nginx)
✅ Real DevOps workflow (dev hands off → DevOps deploys)

---

**Next Steps:**
1. Create the 4 files (3 Dockerfiles + docker-compose.yml)
2. Run `docker-compose build`
3. Run `docker-compose up -d`
4. Test with curl or browser
5. Document any challenges/learnings

Good luck! 🚀
