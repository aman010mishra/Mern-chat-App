# Chat Application - Replit Project

## Overview
Full-stack real-time chat application built with MERN stack (MongoDB, Express, React, Node.js) and Socket.IO. The application supports user authentication, real-time messaging, image uploads via Cloudinary, and theme customization.

## Project Status
- **Last Updated**: November 22, 2025
- **Status**: Running and configured with Docker and CI/CD support
- **Environment**: Replit Development + Docker Production Ready

## Recent Changes (November 22, 2025)

### Docker Configuration
- Updated `backend/Dockerfile` with multi-stage builds and security best practices
- Updated `frontend/Dockerfile` with Nginx configuration
- Created `frontend/nginx.conf` for proper API proxying and static file serving
- Enhanced `docker-compose.yml` with health checks, proper networking, and environment variables
- Added `.env.example` for easy environment setup

### CI/CD Setup
- Enhanced `.github/workflows/ci-cd.yml` with comprehensive pipeline:
  - Separate lint/test jobs for backend and frontend
  - Docker build and push with layer caching
  - Optional SSH deployment to production servers
  - Support for multiple branches (main, master, develop)
  - Manual workflow dispatch option

### Documentation
- Created `DOCKER_SETUP.md` - Complete guide for Docker deployment
- Created `CI_CD_SETUP.md` - GitHub Actions CI/CD setup instructions
- Updated `.gitignore` files for better project hygiene

## Architecture

### Backend (Port 8000)
- **Framework**: Express.js
- **Database**: MongoDB (via Mongoose)
- **Authentication**: JWT with HTTP-only cookies
- **Real-time**: Socket.IO for WebSocket communication
- **File Storage**: Cloudinary for image uploads
- **API Endpoints**:
  - `/api/auth/*` - Authentication routes (signup, login, logout, profile)
  - `/api/msg/*` - Messaging routes (send, get messages, get users)

### Frontend (Port 5000 - Development)
- **Framework**: React 19 with Vite
- **Styling**: TailwindCSS + DaisyUI
- **State Management**: Zustand
- **Routing**: React Router v7
- **Real-time**: Socket.IO client
- **HTTP Client**: Axios with custom configuration

### Development Configuration
- Frontend runs on port 5000 with Vite dev server
- Backend runs on port 8000
- Frontend proxies `/api` and `/socket.io` requests to backend
- Vite configured with `allowedHosts: true` for Replit proxy support

### Production Configuration (Docker)
- Frontend: Nginx on port 80
- Backend: Node.js on port 8000
- MongoDB: Port 27017 with persistent volumes
- All services on isolated bridge network
- Health checks enabled for all services

## Environment Variables

Required secrets (configured in Replit):
- `MONGO_URI` - MongoDB connection string
- `JWT_SECRET` - Secret key for JWT tokens
- `CLOUDINARY_CLOUD_NAME` - Cloudinary cloud name
- `CLOUDINARY_API_KEY` - Cloudinary API key
- `CLOUDINARY_API_SECRET` - Cloudinary API secret

Configured environment variables:
- `PORT=8000` - Backend server port
- `NODE_ENV=development` - Current environment

## Workflows

### Start application
- **Command**: `bash -c 'cd backend && npm start & cd frontend && npm run dev'`
- **Type**: webview on port 5000
- **Purpose**: Runs both backend and frontend concurrently for development

## Docker Deployment

### Local Docker Desktop
```bash
# Build and run
docker-compose build
docker-compose up -d

# View logs
docker-compose logs -f

# Stop
docker-compose down
```

### Production (with Docker Hub)
1. Set up GitHub secrets for Docker Hub credentials
2. Push to main branch triggers automatic build and push
3. Images available at:
   - `<username>/chatapp-backend:latest`
   - `<username>/chatapp-frontend:latest`

## CI/CD Pipeline

### GitHub Actions Workflow
- **Triggers**: Push/PR to main, master, develop branches
- **Jobs**:
  1. Backend lint and test
  2. Frontend lint and test
  3. Build and push Docker images (on push to main/master)
  4. Deploy to server (optional, requires SSH configuration)

### Required GitHub Secrets
- `DOCKER_USERNAME` - Docker Hub username
- `DOCKER_PASSWORD` - Docker Hub password/token
- `DEPLOY_HOST` - (Optional) Server IP for deployment
- `DEPLOY_USER` - (Optional) SSH username
- `DEPLOY_SSH_KEY` - (Optional) SSH private key
- `DEPLOY_PATH` - (Optional) Application path on server

## Key Features

### Authentication
- User registration and login
- JWT-based authentication with HTTP-only cookies
- Profile picture upload
- Session management

### Messaging
- Real-time one-to-one chat
- Online/offline user status
- Message history
- Image attachments
- Socket.IO for instant delivery

### UI/UX
- Multiple theme support (DaisyUI themes)
- Responsive design
- Message skeletons for loading states
- User presence indicators

## File Structure

```
.
├── backend/
│   ├── src/
│   │   ├── controllers/     # Request handlers
│   │   ├── lib/             # Utilities (DB, Cloudinary, Socket.IO)
│   │   ├── middleware/      # Auth middleware
│   │   ├── models/          # Mongoose schemas
│   │   ├── routes/          # API routes
│   │   └── seeds/           # Database seeders
│   ├── Dockerfile           # Production Docker image
│   └── package.json
├── frontend/
│   ├── src/
│   │   ├── components/      # React components
│   │   ├── pages/           # Page components
│   │   ├── store/           # Zustand stores
│   │   ├── lib/             # Axios config, utils
│   │   └── constants/       # App constants
│   ├── nginx.conf           # Nginx configuration for production
│   ├── Dockerfile           # Production Docker image
│   └── vite.config.js       # Vite configuration
├── .github/
│   └── workflows/
│       └── ci-cd.yml        # GitHub Actions pipeline
├── docker-compose.yml       # Docker orchestration
├── .env.example             # Environment variables template
├── DOCKER_SETUP.md          # Docker deployment guide
└── CI_CD_SETUP.md           # CI/CD setup guide
```

## Development Best Practices

### Code Style
- ES6+ syntax with ES modules
- Async/await for asynchronous operations
- Error handling with try-catch blocks
- HTTP-only cookies for authentication

### Security
- Non-root users in Docker containers
- Environment variables for secrets
- JWT token expiration
- CORS configuration
- Input validation and sanitization

### Performance
- Multi-stage Docker builds
- Layer caching in CI/CD
- Gzip compression in Nginx
- Static asset caching
- Connection pooling for database

## Testing Locally

### Without Docker
```bash
# Terminal 1 - Backend
cd backend
npm install
npm start

# Terminal 2 - Frontend
cd frontend
npm install
npm run dev
```

### With Docker
```bash
# Start all services
docker-compose up -d

# Access: http://localhost:80
```

## Troubleshooting

### Replit Environment
- Frontend must use port 5000 with `allowedHosts: true`
- Backend uses localhost:8000
- Vite proxy handles API requests

### Docker Issues
- Check logs: `docker-compose logs <service>`
- Rebuild: `docker-compose build --no-cache`
- Reset: `docker-compose down -v && docker-compose up -d`

### CI/CD Issues
- Verify GitHub secrets are set correctly
- Check Docker Hub repositories exist
- Review workflow logs in GitHub Actions

## Future Enhancements

- [ ] Add automated tests (Jest, Cypress)
- [ ] Implement message notifications
- [ ] Add group chat functionality
- [ ] Support for voice/video calls
- [ ] Message search functionality
- [ ] File attachment support (beyond images)
- [ ] Read receipts
- [ ] Typing indicators

## Resources

- [React Documentation](https://react.dev)
- [Express.js Documentation](https://expressjs.com)
- [Socket.IO Documentation](https://socket.io)
- [Docker Documentation](https://docs.docker.com)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
