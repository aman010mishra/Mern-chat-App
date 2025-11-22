# Security Guidelines

## Overview

This document outlines security best practices and configurations for the Chat Application when running with Docker.

## Critical Security Configurations

### 1. MongoDB Credentials

**NEVER use default or weak credentials in production!**

#### For Development (docker-compose.dev.yml)
- MongoDB runs without authentication for local testing only
- Not exposed to external network
- Data is ephemeral (use volumes for persistence)

#### For Production (docker-compose.yml)
- **REQUIRED**: Set strong MongoDB credentials in `.env`:
  ```env
  MONGO_USERNAME=your_secure_username
  MONGO_PASSWORD=your_complex_secure_password_here
  ```

- Generate secure password:
  ```bash
  openssl rand -base64 32
  ```

- Update `MONGO_URI` to match:
  ```env
  MONGO_URI=mongodb://your_secure_username:your_complex_secure_password_here@mongo:27017/chatapp?authSource=admin
  ```

### 2. Database Port Exposure

**MongoDB should NOT be exposed to the internet!**

#### Development
- `docker-compose.dev.yml` exposes MongoDB on `127.0.0.1:27017`
- Only accessible from localhost
- For local development and testing only

#### Production
- `docker-compose.yml` does NOT expose MongoDB port to host
- MongoDB only accessible within Docker network
- Backend communicates via internal network

### 3. JWT Secret

**REQUIRED**: Generate a strong JWT secret:

```bash
# Generate secure JWT secret
openssl rand -base64 64
```

Add to `.env`:
```env
JWT_SECRET=your_generated_secure_secret_here
```

**Never commit** your actual JWT secret to version control!

### 4. Cloudinary Credentials

Store Cloudinary credentials securely:

```env
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
```

### 5. Environment Variables

#### Never Commit Secrets
- `.env` is in `.gitignore` - keep it there!
- Use `.env.example` as a template only
- Each environment should have its own `.env` file

#### GitHub Actions Secrets
For CI/CD, store secrets in GitHub repository settings:
- `DOCKER_USERNAME` - Docker Hub username
- `DOCKER_PASSWORD` - Docker Hub token (not password!)
- `MONGO_URI` - MongoDB connection string
- `JWT_SECRET` - JWT secret key
- `CLOUDINARY_*` - Cloudinary credentials

## Docker Security Best Practices

### 1. Non-Root Users

Both Dockerfiles create and use non-root users:

```dockerfile
# Create non-root user
RUN addgroup --system --gid 1001 nodejs && \
    adduser --system --uid 1001 nodejs

# Switch to non-root user
USER nodejs
```

### 2. Multi-Stage Builds

Reduces attack surface by excluding dev dependencies:

```dockerfile
FROM node:20-slim AS builder
# ... build steps

FROM node:20-slim
# ... only copy what's needed for production
```

### 3. Health Checks

All services have health checks:
- Early detection of issues
- Automatic container restart on failure
- Prevents routing to unhealthy containers

### 4. Network Isolation

Services communicate on isolated bridge network:
```yaml
networks:
  chatapp-network:
    driver: bridge
```

### 5. Minimal Base Images

Using `node:20-slim` and `nginx:alpine`:
- Smaller attack surface
- Fewer vulnerabilities
- Faster deployment

## Production Deployment Security

### 1. HTTPS/TLS

**Always use HTTPS in production!**

Add reverse proxy (Nginx/Traefik) with SSL:

```yaml
# Example with Traefik
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.frontend.rule=Host(`yourdomain.com`)"
  - "traefik.http.routers.frontend.entrypoints=websecure"
  - "traefik.http.routers.frontend.tls.certresolver=letsencrypt"
```

### 2. Firewall Configuration

Only expose necessary ports:
- Port 80/443 for frontend (HTTPS)
- All other ports should be internal only

```bash
# Example UFW configuration
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw enable
```

### 3. Rate Limiting

Add rate limiting to prevent abuse:

```javascript
// backend/src/index.js
import rateLimit from 'express-rate-limit';

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100 // limit each IP to 100 requests per windowMs
});

app.use('/api/', limiter);
```

### 4. CORS Configuration

Restrict CORS to your domain:

```javascript
// backend/src/index.js
app.use(cors({
  origin: process.env.FRONTEND_URL || 'https://yourdomain.com',
  credentials: true,
}));
```

### 5. Security Headers

Nginx configuration already includes:
- `X-Frame-Options`
- `X-Content-Type-Options`
- `X-XSS-Protection`

### 6. Regular Updates

Keep dependencies updated:

```bash
# Check for vulnerabilities
npm audit

# Update dependencies
npm update

# Rebuild Docker images
docker-compose build --no-cache
```

## Secrets Management

### Development
Use `.env` file (never commit!)

### Production Options

1. **Docker Secrets** (Docker Swarm):
   ```yaml
   secrets:
     - mongo_password
     - jwt_secret
   ```

2. **Environment Variables** (via hosting platform):
   - AWS Secrets Manager
   - Azure Key Vault
   - Google Cloud Secret Manager

3. **HashiCorp Vault**:
   - Centralized secrets management
   - Automatic rotation
   - Audit logging

## Monitoring and Logging

### 1. Enable Logging

```yaml
services:
  backend:
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
```

### 2. Monitor Failed Login Attempts

Implement in backend:
```javascript
// Track failed login attempts
// Lock accounts after N failed attempts
// Alert on suspicious activity
```

### 3. Database Backups

Regular automated backups:

```bash
# Backup MongoDB
docker exec chatapp-mongo mongodump --out /backup

# Restore
docker exec chatapp-mongo mongorestore /backup
```

## Incident Response

### If Secrets Are Compromised

1. **Immediately** rotate all credentials
2. Review access logs for suspicious activity
3. Update `.env` with new credentials
4. Restart all services:
   ```bash
   docker-compose down
   docker-compose up -d
   ```
5. Notify affected users if necessary

### If Database Is Compromised

1. Take database offline
2. Restore from latest clean backup
3. Investigate breach source
4. Update all credentials
5. Review and update security measures

## Security Checklist

Before deploying to production:

- [ ] Strong MongoDB credentials set
- [ ] MongoDB NOT exposed to internet
- [ ] JWT secret generated and secure
- [ ] All `.env` values updated (no defaults)
- [ ] HTTPS/TLS configured
- [ ] Firewall rules configured
- [ ] CORS restricted to domain
- [ ] Rate limiting enabled
- [ ] Security headers configured
- [ ] Regular backup strategy in place
- [ ] Monitoring and logging enabled
- [ ] Dependencies updated
- [ ] Vulnerability scanning completed
- [ ] GitHub secrets configured
- [ ] `.env` NOT in version control

## Vulnerability Scanning

### Scan Docker Images

```bash
# Using Trivy
docker run aquasec/trivy image chatapp-backend:latest

# Using Snyk
snyk container test chatapp-backend:latest
```

### Scan Dependencies

```bash
# Backend
cd backend
npm audit
npm audit fix

# Frontend
cd frontend
npm audit
npm audit fix
```

## Compliance

### GDPR Considerations
- Implement user data export
- Implement right to deletion
- Encrypt sensitive data
- Log data access

### Security Standards
- Follow OWASP Top 10
- Implement CSP headers
- Use prepared statements (already done with Mongoose)
- Validate and sanitize all inputs

## Resources

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Docker Security Best Practices](https://docs.docker.com/engine/security/)
- [Node.js Security Checklist](https://blog.risingstack.com/node-js-security-checklist/)
- [MongoDB Security Checklist](https://www.mongodb.com/docs/manual/administration/security-checklist/)

## Contact

For security issues, please do not open a public issue. Contact the security team directly.
