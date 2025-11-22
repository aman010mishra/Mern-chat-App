# Quick Start Guide

## Running the Application on Replit

The application is already configured and running! You can:

1. **Sign up** for a new account
2. **Log in** with your credentials
3. **Start chatting** with other users in real-time
4. **Upload images** to your profile and messages
5. **Customize themes** from the settings page

## Docker Desktop Setup

### Prerequisites
- Docker Desktop installed and running

### Quick Start

1. **Create environment file:**
   ```bash
   cp .env.example .env
   ```

2. **Edit `.env` and set your credentials:**
   - MongoDB username and password (create secure values!)
   - JWT secret (generate with: `openssl rand -base64 32`)
   - Cloudinary credentials

3. **Start all services:**
   ```bash
   docker-compose up -d
   ```

4. **Access the application:**
   - Frontend: http://localhost:80
   - Backend API: http://localhost:8000

5. **View logs:**
   ```bash
   docker-compose logs -f
   ```

6. **Stop services:**
   ```bash
   docker-compose down
   ```

### Development Mode (Simplified)

For local development without authentication:

```bash
# Start development stack
docker-compose -f docker-compose.dev.yml up -d

# Access at http://localhost:5173
```

## GitHub Actions CI/CD Setup

### 1. Configure GitHub Secrets

Go to: **Repository → Settings → Secrets and variables → Actions**

Add these secrets:
- `DOCKER_USERNAME` - Your Docker Hub username
- `DOCKER_PASSWORD` - Your Docker Hub token

### 2. (Optional) Enable Auto-Deployment

Add these additional secrets if you want automatic deployment:
- `DEPLOY_HOST` - Your server IP
- `DEPLOY_USER` - SSH username
- `DEPLOY_SSH_KEY` - SSH private key
- `DEPLOY_PATH` - Path on server (e.g., `/var/www/chatapp`)

Set variable:
- `DEPLOY_ENABLED` = `true`

### 3. Push to GitHub

```bash
git add .
git commit -m "Add Docker and CI/CD configuration"
git push origin main
```

The CI/CD pipeline will automatically:
- Run tests and linting
- Build Docker images
- Push to Docker Hub
- (Optional) Deploy to your server

## Documentation

- **[DOCKER_SETUP.md](DOCKER_SETUP.md)** - Complete Docker guide with commands and troubleshooting
- **[CI_CD_SETUP.md](CI_CD_SETUP.md)** - Detailed CI/CD setup with GitHub Actions
- **[SECURITY.md](SECURITY.md)** - Security best practices and guidelines

## Project Structure

```
.
├── backend/              # Node.js/Express backend
├── frontend/             # React frontend
├── docker-compose.yml    # Production Docker setup
├── docker-compose.dev.yml # Development Docker setup
├── .github/workflows/    # CI/CD pipeline
└── docs/                 # Documentation
```

## Common Commands

### Docker
```bash
# Build images
docker-compose build

# Start services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop services
docker-compose down

# Rebuild and restart
docker-compose up -d --build
```

### Local Development
```bash
# Backend
cd backend
npm install
npm start

# Frontend
cd frontend
npm install
npm run dev
```

## Need Help?

- Check [DOCKER_SETUP.md](DOCKER_SETUP.md) for Docker issues
- Check [CI_CD_SETUP.md](CI_CD_SETUP.md) for CI/CD issues
- Check [SECURITY.md](SECURITY.md) for security configuration
- Review [replit.md](replit.md) for project architecture

## Next Steps

1. ✅ Application is running on Replit
2. ✅ Docker configuration ready
3. ✅ CI/CD pipeline configured
4. 🔄 Set up your GitHub secrets
5. 🔄 Create Docker Hub repositories
6. 🔄 Push to GitHub to trigger CI/CD
7. 🔄 Deploy to production server (optional)
