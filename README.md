# Venkatesh Kattamuri | Two-Tier Flask + MySQL DevOps Project

A production-ready, containerized two-tier web application featuring Flask frontend and MySQL database, fully automated with Jenkins CI/CD and deployed on AWS EC2.

---

## 📋 Table of Contents
- [Overview](#overview)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Project Setup (Step-by-Step)](#project-setup-step-by-step)
- [Docker & Docker Compose](#docker--docker-compose)
- [Jenkins CI/CD Pipeline](#jenkins-cicd-pipeline)
- [Development Workflow](#development-workflow)
- [Live Reload Setup](#live-reload-setup)
- [Troubleshooting](#troubleshooting)
- [File Structure](#file-structure)
- [Key Features](#key-features)
- [Contributing](#contributing)

---

## 🎯 Overview

This project demonstrates a complete DevOps workflow using:
- **Two-tier architecture**: Flask web app + MySQL database
- **Containerization**: Docker & Docker Compose
- **Automation**: Jenkins pipeline for CI/CD
- **Cloud deployment**: AWS EC2 instances
- **Message board app**: Real-time message persistence with MySQL

### Key Highlights
✅ Automated deployment via Jenkins  
✅ Persistent data storage with Docker volumes  
✅ Health checks and auto-restart policies  
✅ Live file binding for rapid development  
✅ GitHub-based CI/CD triggers  
✅ Isolated network for secure container communication  

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────┐
│          AWS EC2 Instance               │
├─────────────────────────────────────────┤
│                                         │
│  ┌─────────────────────────────────┐   │
│  │     Docker Network (two-tier)   │   │
│  │                                 │   │
│  │  ┌──────────┐    ┌──────────┐  │   │
│  │  │ Flask    │←──→│  MySQL   │  │   │
│  │  │   App    │    │ Database │  │   │
│  │  │ :5000    │    │  :3306   │  │   │
│  │  └──────────┘    └──────────┘  │   │
│  │                                 │   │
│  └─────────────────────────────────┘   │
│                                         │
└─────────────────────────────────────────┘
         ↑           ↑
         │           │
    GitHub      Jenkins
   (Webhook)   (CI/CD)
```

---

## 🛠️ Tech Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| **Frontend** | Python Flask | Web framework |
| **Template Engine** | Jinja2 | HTML templating |
| **Backend Database** | MySQL 8.0 | Data persistence |
| **Containerization** | Docker | Application packaging |
| **Orchestration** | Docker Compose | Multi-container management |
| **CI/CD** | Jenkins | Automated pipeline |
| **VCS** | Git/GitHub | Source control |
| **Cloud** | AWS EC2 | Hosting |

---

## 📦 Prerequisites

### Local Development
- **Docker** (v20.10+) [Download](https://www.docker.com/products/docker-desktop)
- **Docker Compose** (v1.29+) - included with Docker Desktop
- **Git** [Download](https://git-scm.com/)
- **Python** (3.9+) - optional, for local testing

### For Jenkins Deployment
- AWS EC2 instance (Ubuntu 22.04 LTS recommended)
- Jenkins installed on the instance
- Docker & Docker Compose on the instance
- GitHub account with webhook access

### System Requirements
- **RAM**: 2GB minimum
- **CPU**: 2 cores minimum
- **Storage**: 5GB free space
- **Network**: Ports 5000 (Flask), 3306 (MySQL), 8080 (Jenkins)

---

## 🚀 Quick Start

### 1️⃣ Clone the Repository
```bash
git clone https://github.com/VENKATESH-KATTAMURI/Devops-Project-Two-Tier-Flask-App.git
cd Devops-Project-Two-Tier-Flask-App
```

### 2️⃣ Build and Run with Docker Compose
```bash
docker compose up -d --build
```

### 3️⃣ Access the Application
- **Web App**: http://localhost:5000
- **MySQL**: localhost:3306 (root:root)

### 4️⃣ Stop the Application
```bash
docker compose down
```

---

## 📝 Project Setup (Step-by-Step)

### Step 1: Initialize Git Repository
```bash
cd ~/Devops-Project-Two-Tier-Flask-App
git init
git add .
git commit -m "Initial commit: Two-tier Flask app"
git remote add origin https://github.com/VENKATESH-KATTAMURI/Devops-Project-Two-Tier-Flask-App.git
git branch -M main
git push -u origin main
```

### Step 2: Create Project Structure
```
project-root/
├── app.py                 # Flask application
├── requirements.txt       # Python dependencies
├── Dockerfile            # Flask app container config
├── docker-compose.yml    # Multi-container orchestration
├── Jenkinsfile          # CI/CD pipeline
├── message.sql          # Database schema (optional)
├── templates/
│   └── index.html       # Frontend UI
└── README.md            # This file
```

### Step 3: Install Dependencies
```bash
pip install -r requirements.txt
```

**requirements.txt** should contain:
```
Flask==2.3.0
flask-mysqldb==1.0.1
Jinja2==3.1.2
Werkzeug==2.3.0
```

### Step 4: Configure Database
MySQL table is created automatically by Flask on startup. The table structure:
```sql
CREATE TABLE messages (
    id INT AUTO_INCREMENT PRIMARY KEY,
    message TEXT
);
```

### Step 5: Build Docker Images
```bash
docker compose build
```

### Step 6: Run Containers
```bash
docker compose up -d
```

### Step 7: Verify Deployment
```bash
# Check running containers
docker compose ps

# View logs
docker compose logs flask-app
docker compose logs mysql

# Test Flask app
curl http://localhost:5000

# Verify MySQL connection
docker exec -it mysql mysql -uroot -proot devops -e "SELECT * FROM messages;"
```

---

## 🐳 Docker & Docker Compose

### Dockerfile Explanation

```dockerfile
FROM python:3.9-slim
WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    gcc default-libmysqlclient-dev pkg-config curl && \
    rm -rf /var/lib/apt/lists/*

# Copy requirements and install Python packages
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY app.py .
COPY templates/ templates/

# Expose port and run Flask
EXPOSE 5000
CMD ["python", "app.py"]
```

### docker-compose.yml Explanation

**MySQL Service**:
- Image: `mysql:8.0`
- Volume: `mysql_data:/var/lib/mysql` (persistent storage)
- Health check: Pings every 15 seconds
- Environment: Root password, database name

**Flask Service**:
- Build: From Dockerfile in current directory
- Volume: `.:/app` (live bind mount for development)
- Environment: MySQL connection details, `FLASK_DEBUG=1`
- Depends on: MySQL with health check
- Restart: Always (auto-restart on failure)

### Docker Compose Commands

```bash
# Start services
docker compose up -d

# Rebuild without cache
docker compose build --no-cache

# Stop and remove containers
docker compose down

# Remove everything including volumes
docker compose down -v

# View logs
docker compose logs -f

# Execute command in container
docker exec -it two-tier-app bash
```

---

## 🔧 Jenkins CI/CD Pipeline

### Jenkins Setup

#### 1. Install Jenkins
```bash
# On AWS EC2 Ubuntu 22.04
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install -y jenkins java-11-openjdk
sudo systemctl start jenkins
```

#### 2. Access Jenkins
- URL: `http://<ec2-instance-ip>:8080`
- Get initial admin password: `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`

#### 3. Create Pipeline Job
1. Click "New Item"
2. Enter job name: `Devops-Flask-App`
3. Select "Pipeline"
4. Configure:
   - **Pipeline script**: Select "Pipeline script from SCM"
   - **SCM**: Git
   - **Repository URL**: `https://github.com/VENKATESH-KATTAMURI/Devops-Project-Two-Tier-Flask-App.git`
   - **Branch**: `*/main`

### Jenkinsfile Workflow

```groovy
pipeline {
    agent any
    stages {
        stage('Clone Code') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/VENKATESH-KATTAMURI/Devops-Project-Two-Tier-Flask-App.git'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t flask-app:latest .'
            }
        }
        
        stage('Deploy with Docker Compose') {
            steps {
                sh 'docker compose down || true'
                sh 'docker compose up -d --build'
            }
        }
    }
}
```

### GitHub Webhook Setup

1. Go to repository Settings → Webhooks → Add webhook
2. **Payload URL**: `http://<jenkins-server>:8080/github-webhook/`
3. **Content type**: `application/json`
4. **Events**: Push events
5. Click "Add webhook"

### Triggering Deployments

**Automatic** (via webhook):
```bash
git push origin main
# Jenkins automatically triggers
```

**Manual** (in Jenkins UI):
- Go to job → Click "Build Now"

---

## 💻 Development Workflow

### Local Development with Live Reload

The project is configured for rapid development with live file binding:

#### Enable Live Reload
1. Ensure `docker-compose.yml` has volume mount:
```yaml
flask-app:
  volumes:
    - .:/app           # Mounts project folder
  environment:
    - FLASK_DEBUG=1    # Enables auto-reload
```

2. Start containers:
```bash
docker compose up -d --build
```

3. Edit files locally (e.g., `templates/index.html`, `app.py`)
4. Changes reflect immediately in the container

### Example Workflow

#### Modify Template
```bash
# Edit templates/index.html locally
nano templates/index.html

# Save and refresh browser at http://localhost:5000
# Changes appear instantly (FLASK_DEBUG=1 enabled)
```

#### Add New Route
```bash
# Edit app.py
nano app.py

# Add new route:
@app.route('/health')
def health():
    return {'status': 'healthy'}, 200

# Save file, Flask auto-reloads
# Test: curl http://localhost:5000/health
```

#### Push Changes to GitHub
```bash
git add .
git commit -m "Add new route and update template"
git push origin main

# Jenkins auto-triggers via webhook
# Pipeline builds, tests, and deploys
```

---

## 📱 Live Reload Setup

### Why Live Reload?
- **No container restart needed** for code changes
- **Instant feedback** during development
- **Faster iteration** cycle

### How It Works

1. **Bind Mount** (`.:/app`):
   - Local project folder mounted into container at `/app`
   - Changes to local files reflect in container instantly

2. **FLASK_DEBUG=1**:
   - Flask watches for file changes
   - Auto-restarts when Python files change
   - Templates reload without restart

3. **Benefits**:
   - Edit `index.html` → Refresh browser → See changes
   - Edit `app.py` → Flask restarts → See new routes
   - No `docker-compose down/up` needed

### Disable for Production

In production (AWS), remove the bind mount and set `FLASK_DEBUG=0`:

```yaml
# Production docker-compose.yml
flask-app:
  environment:
    - FLASK_DEBUG=0
  # No volumes mount
```

---

## 🐛 Troubleshooting

### Issue: "MySQL is unhealthy"

**Cause**: MySQL takes time to initialize  
**Solution**:
```bash
docker compose down -v
docker compose up -d --build
# Wait 90+ seconds for MySQL to be ready
```

### Issue: "TemplateNotFound: index.html"

**Cause**: `templates/` folder not in Docker image  
**Solution**:
```bash
# Verify templates exist locally
ls templates/index.html

# Rebuild without cache
docker compose build --no-cache
docker compose up -d

# Verify in container
docker exec -it two-tier-app ls /app/templates/
```

### Issue: "Connection refused" on port 5000

**Cause**: Flask app didn't start or crashed  
**Solution**:
```bash
# Check logs
docker compose logs flask-app

# Verify MySQL is healthy
docker compose ps

# Restart
docker compose restart flask-app
```

### Issue: Jenkins not triggering on push

**Cause**: Webhook misconfigured or Jenkins URL unreachable  
**Solution**:
1. Verify GitHub webhook URL is accessible
2. Check Jenkins firewall/security groups
3. Test webhook manually: GitHub Settings → Webhooks → Click delivery

### Issue: Data lost after restart

**Cause**: No volume mounted for MySQL  
**Solution**: Verify `docker-compose.yml` has:
```yaml
mysql:
  volumes:
    - mysql_data:/var/lib/mysql
```

---

## 📂 File Structure

```
Devops-Project-Two-Tier-Flask-App/
│
├── app.py                          # Flask application
│   ├── MySQL configuration
│   ├── Message routes
│   └── Database initialization
│
├── requirements.txt                # Python dependencies
│   ├── Flask
│   ├── flask-mysqldb
│   └── Other packages
│
├── Dockerfile                      # Flask container config
│   ├── Python 3.9 base image
│   ├── System dependencies
│   └── App dependencies
│
├── docker-compose.yml              # Multi-container setup
│   ├── MySQL service (port 3306)
│   ├── Flask service (port 5000)
│   ├── Volumes & networks
│   └── Health checks
│
├── Jenkinsfile                     # CI/CD pipeline
│   ├── Clone stage
│   ├── Build stage
│   └── Deploy stage
│
├── templates/
│   └── index.html                  # Frontend UI
│       ├── Hero section with name
│       ├── Skills/badges
│       ├── Message form
│       └── Messages display
│
├── message.sql                     # Database schema (optional)
│
└── README.md                       # This file
```

---

## ✨ Key Features

### 1. **Message Board**
- Real-time message submission
- Messages persisted to MySQL
- AJAX form submission (no page reload)
- Responsive design

### 2. **Health Checks**
- MySQL healthcheck: Pings every 15 seconds
- Flask healthcheck: HTTP curl every 10 seconds
- Auto-restart on failure
- Cascading dependency management

### 3. **Data Persistence**
- MySQL data stored in Docker named volume
- Survives container restart
- Survives host reboot (on EC2)

### 4. **Live Development**
- Bind mount for local file editing
- Flask auto-reload on file changes
- No container restart needed
- Instant feedback loop

### 5. **CI/CD Automation**
- GitHub webhook triggers Jenkins
- Automatic build and deployment
- Zero-downtime updates
- Consistent environments

### 6. **Production Ready**
- Docker images optimized for size
- Security best practices (non-root user optional)
- Error logging and monitoring hooks
- Scalable architecture

---

## 🤝 Contributing

### Workflow
1. **Clone the repo**
   ```bash
   git clone https://github.com/VENKATESH-KATTAMURI/Devops-Project-Two-Tier-Flask-App.git
   cd Devops-Project-Two-Tier-Flask-App
   ```

2. **Create a feature branch**
   ```bash
   git checkout -b feature/your-feature
   ```

3. **Make changes**
   ```bash
   # Edit files
   # Test locally: docker compose up -d --build
   ```

4. **Commit and push**
   ```bash
   git add .
   git commit -m "feat: add your feature"
   git push origin feature/your-feature
   ```

5. **Create Pull Request**
   - Go to GitHub → Compare & pull request
   - Describe changes and wait for review

### Development Guidelines
- Test all changes locally before pushing
- Follow Python PEP 8 style guide
- Update README for new features
- Ensure Docker builds successfully
- Test with live reload enabled

---

## 📊 Deployment Checklist

- [ ] Git repository initialized and pushed
- [ ] Docker and Docker Compose installed
- [ ] `docker-compose build` runs successfully
- [ ] `docker-compose up -d` starts all services
- [ ] Flask app accessible at http://localhost:5000
- [ ] MySQL database is healthy
- [ ] Messages persist after container restart
- [ ] Templates load without errors
- [ ] Jenkins server configured
- [ ] GitHub webhook configured
- [ ] Jenkins job created and tested
- [ ] Manual Jenkins build successful
- [ ] GitHub webhook triggers Jenkins automatically

---

## 📞 Support & Issues

- **GitHub Issues**: Report bugs on [GitHub](https://github.com/VENKATESH-KATTAMURI/Devops-Project-Two-Tier-Flask-App/issues)
- **Documentation**: Refer to sections above
- **Docker Docs**: https://docs.docker.com/
- **Flask Docs**: https://flask.palletsprojects.com/
- **Jenkins Docs**: https://www.jenkins.io/doc/

---

## 📜 License

This project is open source and available under the MIT License.

---

## 👨‍💻 Author

**Venkatesh Kattamuri**  
DevOps & Cloud Engineer | Student  
GitHub: [@VENKATESH-KATTAMURI](https://github.com/VENKATESH-KATTAMURI)

---

## 🚀 Next Steps

1. **Run locally**: `docker compose up -d --build`
2. **Access app**: http://localhost:5000
3. **Try live reload**: Edit `templates/index.html` and refresh
4. **Push to GitHub**: `git push origin main`
5. **Deploy via Jenkins**: Webhook triggers automatic deployment
6. **Monitor**: Check Jenkins logs and container health

**Happy coding! 🎉**
