# Two-Tier Flask + MySQL

A production-ready DevOps project with Flask web app, MySQL database, Docker containerization, and Jenkins CI/CD automation.

---

## 🚀 Quick Start

```bash
# 1. Clone
git clone https://github.com/VENKATESH-KATTAMURI/Devops-Project-Two-Tier-Flask-App.git
cd Devops-Project-Two-Tier-Flask-App

# 2. Run
docker compose up -d --build

# 3. Access
# Web: http://localhost:5000
# MySQL: localhost:3306 (root:root)

# 4. Stop
docker compose down
```

---

## 📸 Project Screenshots

### 1. Project Setup & Initial Configuration
![Setup](./Screenshots/1.png)
*Initial project structure and Docker setup configuration*

### 2. Docker Build Process
![Docker Build](./Screenshots/2.png)
*Building Docker images for Flask application and MySQL database*

### 3. Docker Containers Running
![Containers Running](./Screenshots/3.png)
*Flask app and MySQL containers running with health checks passed*

### 4. Application Home Page
![Application Home](./Screenshots/4.png)
*Landing page featuring message board with Venkatesh Kattamuri branding*

### 5. Jenkins Pipeline Configuration
![Jenkins Setup](./Screenshots/5.png)
*Jenkins CI/CD pipeline configuration with GitHub integration*

### 6. Jenkins Build Execution
![Jenkins Build](./Screenshots/6.png)
*Automated build and deployment process through Jenkins pipeline*

### 7. Deployment Success & Application Live
![Deployment Success](./Screenshots/7.png)
*Successfully deployed application with messages persisting in MySQL database*

---

## 🛠️ Tech Stack

- **Frontend**: Python Flask + Jinja2
- **Database**: MySQL 8.0
- **Containerization**: Docker & Docker Compose
- **CI/CD**: Jenkins + GitHub Webhook
- **Cloud**: AWS EC2
- **VCS**: Git/GitHub

---

## 📂 Project Structure

```
├── app.py                 # Flask application with message routes
├── requirements.txt       # Python dependencies
├── Dockerfile            # Flask container config
├── docker-compose.yml    # MySQL + Flask services
├── Jenkinsfile          # CI/CD pipeline
├── templates/
│   └── index.html       # Frontend UI with message board
└── README.md            # This file
```

---

## 🛠️ Setup (Step-by-Step)

### Local Development

1. **Install Docker** → [Download](https://www.docker.com/)
2. **Clone repo** → `git clone ...`
3. **Build** → `docker compose build`
4. **Run** → `docker compose up -d`
5. **Test** → Open http://localhost:5000

### Key Commands

```bash
docker compose logs -f              # View logs
docker compose ps                   # Check status
docker exec -it two-tier-app bash   # Enter Flask container
docker compose down -v              # Stop & remove volumes
docker compose build --no-cache     # Force rebuild
```

---

## 🐳 Docker & Docker Compose

**Dockerfile**:
- Python 3.9 slim base
- Installs: gcc, mysql-client, curl
- Copies app code and templates

**docker-compose.yml**:
- **MySQL**: `mysql:8.0` on port 3306, persistent volume
- **Flask**: Builds from Dockerfile, port 5000
- **Network**: Isolated docker network
- **Health checks**: Auto-restart on failure
- **Live reload**: Volume bind mount for dev (`.:/app`)

---

## 🔧 Jenkins CI/CD Setup

### 1. Install Jenkins (AWS EC2 Ubuntu 22.04)

```bash
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update && sudo apt-get install -y jenkins java-11-openjdk
sudo systemctl start jenkins
```

### 2. Configure Jenkins Job

1. New Item → Pipeline
2. SCM: Git
3. Repository: `https://github.com/VENKATESH-KATTAMURI/Devops-Project-Two-Tier-Flask-App.git`
4. Branch: `*/main`

### 3. Add GitHub Webhook

- Go to repo Settings → Webhooks → Add
- Payload URL: `http://<jenkins-ip>:8080/github-webhook/`
- Event: Push

### 4. Deploy

```bash
# Push code
git push origin main

# Jenkins auto-triggers via webhook → builds and deploys
```

---

## 💻 Development Workflow

### Live Reload (Hot Reload)

`docker-compose.yml` has:
```yaml
volumes:
  - .:/app              # Mount local files
environment:
  - FLASK_DEBUG=1       # Auto-reload on changes
```

**How to use**:
1. Edit `templates/index.html` locally
2. Refresh browser → See changes instantly
3. No container restart needed
4. Edit `app.py` → Flask auto-reloads

### Example

```bash
# Edit template
nano templates/index.html

# Save → Refresh browser → Changes appear

# Edit app.py
nano app.py

# Save → Flask restarts → Test new routes
```

---

## 🐛 Quick Troubleshooting

| Issue | Solution |
|-------|----------|
| MySQL unhealthy | `docker compose down -v && docker compose up -d --build` |
| Template not found | `docker exec -it two-tier-app ls /app/templates/` |
| Port 5000 refused | `docker compose logs flask-app` |
| Data lost on restart | Verify volume in compose: `mysql_data:/var/lib/mysql` |
| Jenkins not triggering | Check webhook in GitHub Settings → Webhooks |

---

## ✨ Features

✅ Message board with MySQL persistence  
✅ Health checks & auto-restart  
✅ Live reload for development  
✅ Jenkins CI/CD automation  
✅ GitHub webhook integration  
✅ Docker volumes for data persistence  
✅ Isolated container network  

---

## 📋 Deployment Checklist

- [ ] Docker & Docker Compose installed
- [ ] `docker compose build` successful
- [ ] `docker compose up -d` starts all services
- [ ] Flask app at http://localhost:5000
- [ ] MySQL healthy (check with `docker compose ps`)
- [ ] Messages persist after restart
- [ ] Jenkins installed on AWS EC2
- [ ] GitHub webhook configured
- [ ] Push triggers Jenkins build

---

## 📖 Full Docs

For detailed setup, refer to [Full README Guide](./DETAILED_README.md)

---

## 🚀 Next Steps

```bash
# 1. Run locally
docker compose up -d --build

# 2. Edit files
nano templates/index.html

# 3. Commit & push
git add .
git commit -m "Update UI"
git push origin main

# 4. Jenkins deploys automatically via webhook
```

---

## 👨‍💻 Author

**Venkatesh Kattamuri** | DevOps & Cloud Engineer | [GitHub](https://github.com/VENKATESH-KATTAMURI)
