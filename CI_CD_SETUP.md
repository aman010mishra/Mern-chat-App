# CI/CD Setup Guide with GitHub Actions

This guide explains how to set up Continuous Integration and Continuous Deployment (CI/CD) for the Chat Application using GitHub Actions.

## Overview

The CI/CD pipeline automatically:
1. Runs linting and tests on code changes
2. Builds Docker images for backend and frontend
3. Pushes images to Docker Hub
4. (Optional) Deploys to your server via SSH

## Prerequisites

- GitHub account with repository access
- Docker Hub account
- (Optional) Production server with SSH access

## GitHub Secrets Configuration

You need to configure the following secrets in your GitHub repository:

### Required Secrets for Docker Build & Push

Go to: **Repository → Settings → Secrets and variables → Actions → New repository secret**

1. **DOCKER_USERNAME**: Your Docker Hub username
2. **DOCKER_PASSWORD**: Your Docker Hub password or access token

### Optional Secrets for Deployment

Only needed if you want automatic deployment to a server:

3. **DEPLOY_HOST**: Your server's IP address or hostname
4. **DEPLOY_USER**: SSH username for your server
5. **DEPLOY_SSH_KEY**: Private SSH key for authentication
6. **DEPLOY_PATH**: Path to your application on the server (e.g., `/var/www/chatapp`)

### How to Add Secrets

```bash
# Navigate to your repository on GitHub
# Go to Settings → Secrets and variables → Actions
# Click "New repository secret"
# Add each secret with its value
```

## GitHub Variables Configuration

For deployment control, set this variable:

Go to: **Repository → Settings → Secrets and variables → Actions → Variables**

- **DEPLOY_ENABLED**: Set to `true` to enable automatic deployment

## Docker Hub Setup

### 1. Create Docker Hub Account

Visit https://hub.docker.com and create an account.

### 2. Create Repositories

Create two repositories on Docker Hub:
- `chatapp-backend`
- `chatapp-frontend`

### 3. Generate Access Token (Recommended)

Instead of using your password:

1. Go to **Account Settings → Security → Access Tokens**
2. Click **New Access Token**
3. Name: `github-actions`
4. Permissions: **Read, Write, Delete**
5. Copy the token and use it as `DOCKER_PASSWORD` secret

## CI/CD Workflow Explained

### Trigger Events

The pipeline runs on:
- Push to `main`, `master`, or `develop` branches
- Pull requests to `main`, `master`, or `develop` branches
- Manual trigger (workflow_dispatch)

### Jobs

#### 1. Backend Lint & Test
- Checks out code
- Sets up Node.js 20
- Installs dependencies
- Runs linter (if configured)
- Runs tests (if configured)

#### 2. Frontend Lint & Test
- Checks out code
- Sets up Node.js 20
- Installs dependencies
- Runs linter
- Builds the frontend
- Uploads build artifacts

#### 3. Build & Push Docker Images
- Only runs on push to `main` or `master`
- Builds backend and frontend Docker images
- Pushes images to Docker Hub with tags:
  - `latest` (for main/master branch)
  - `<branch>-<git-sha>` (for tracking)

#### 4. Deploy (Optional)
- Only runs if `DEPLOY_ENABLED` is `true`
- Connects to server via SSH
- Pulls latest code
- Pulls latest Docker images
- Restarts containers

## Setting Up Your Server for Deployment

### 1. Install Docker on Server

```bash
# Ubuntu/Debian
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

### 2. Clone Repository on Server

```bash
cd /var/www
git clone <your-repository-url> chatapp
cd chatapp
```

### 3. Configure Environment Variables

```bash
# Create .env file
cp .env.example .env
nano .env  # Edit with your production credentials
```

### 4. Set Up SSH Key Authentication

On your local machine:

```bash
# Generate SSH key if you don't have one
ssh-keygen -t ed25519 -C "github-actions"

# Copy public key to server
ssh-copy-id user@your-server-ip

# Copy private key content for GitHub secret
cat ~/.ssh/id_ed25519  # Copy this to DEPLOY_SSH_KEY secret
```

### 5. Test SSH Connection

```bash
ssh user@your-server-ip "cd /var/www/chatapp && docker-compose ps"
```

## Local Testing

Test the workflow locally before pushing:

### Install act (GitHub Actions locally)

```bash
# macOS
brew install act

# Linux
curl https://raw.githubusercontent.com/nektos/act/master/install.sh | sudo bash

# Windows
choco install act-cli
```

### Run workflow locally

```bash
# List available workflows
act -l

# Run specific job
act -j backend-lint-test

# Run with secrets
act -j build-push-docker --secret-file .secrets
```

## Monitoring CI/CD Pipeline

### View Workflow Runs

1. Go to your repository on GitHub
2. Click **Actions** tab
3. View running/completed workflows

### Troubleshooting Failed Builds

```bash
# Check logs in GitHub Actions UI
# Common issues:

# 1. Docker Hub authentication failure
# → Verify DOCKER_USERNAME and DOCKER_PASSWORD secrets

# 2. Build failure
# → Check Dockerfile syntax
# → Verify dependencies in package.json

# 3. Deployment failure
# → Test SSH connection manually
# → Verify server has Docker installed
# → Check server disk space
```

## Customizing the Pipeline

### Modify Workflow File

Edit `.github/workflows/ci-cd.yml`:

```yaml
# Add more test steps
- name: Run integration tests
  run: npm run test:integration

# Add security scanning
- name: Security scan
  run: npm audit

# Add environment-specific deployments
- name: Deploy to staging
  if: github.ref == 'refs/heads/develop'
  # ... deployment steps
```

### Branch-Specific Deployments

```yaml
# Deploy develop branch to staging
deploy-staging:
  if: github.ref == 'refs/heads/develop'
  # ... staging deployment steps

# Deploy main branch to production
deploy-production:
  if: github.ref == 'refs/heads/main'
  # ... production deployment steps
```

## Best Practices

1. **Never commit secrets** to the repository
2. **Use environment-specific .env files** on servers
3. **Test locally** before pushing
4. **Use semantic versioning** for Docker tags
5. **Monitor workflow runs** regularly
6. **Set up notifications** for failed builds
7. **Use pull requests** for code review before merging

## Security Recommendations

1. **Rotate secrets regularly**
2. **Use Docker Hub access tokens** instead of passwords
3. **Limit SSH key access** to specific commands
4. **Enable 2FA** on Docker Hub and GitHub
5. **Scan images for vulnerabilities**:
   ```yaml
   - name: Scan image
     uses: aquasecurity/trivy-action@master
     with:
       image-ref: 'chatapp-backend:latest'
   ```

## Notification Setup

Add Slack/Discord notifications for build status:

```yaml
- name: Notify on Slack
  if: always()
  uses: 8398a7/action-slack@v3
  with:
    status: ${{ job.status }}
    webhook_url: ${{ secrets.SLACK_WEBHOOK }}
```

## Cost Optimization

1. **Use matrix builds** for testing multiple Node versions
2. **Cache dependencies** (already configured)
3. **Use Docker layer caching** (already configured)
4. **Limit workflow runs** to necessary branches
5. **Set timeout limits**:
   ```yaml
   jobs:
     build:
       timeout-minutes: 30
   ```

## Rollback Strategy

If deployment fails:

```bash
# SSH into server
ssh user@your-server-ip

# View running containers
docker-compose ps

# Rollback to previous image
docker-compose pull chatapp-backend:previous-tag
docker-compose up -d --force-recreate

# Or restore from backup
docker-compose down
git checkout previous-commit
docker-compose up -d
```

## Advanced Features

### Multi-environment Deployment

```yaml
# Add environment-specific jobs
deploy-staging:
  environment: staging
  # ... deployment steps

deploy-production:
  environment: production
  needs: [deploy-staging]
  # ... deployment steps with approval
```

### Automated Testing

```yaml
- name: E2E Tests
  run: |
    docker-compose up -d
    npm run test:e2e
    docker-compose down
```

### Performance Monitoring

```yaml
- name: Lighthouse CI
  uses: treosh/lighthouse-ci-action@v9
  with:
    urls: 'http://localhost:80'
```

## Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Docker Hub Documentation](https://docs.docker.com/docker-hub/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [SSH Action Documentation](https://github.com/appleboy/ssh-action)
