# MERN Chat Application

## Project Overview
A full-stack real-time chat application built with React, Node.js, Express, MongoDB, and Socket.IO. Features include user registration, login, real-time messaging, profile management, image uploads via Cloudinary, and theme customization.

## Repository
- GitHub: https://github.com/aman010mishra/Mern-chat-App.git

## Tech Stack
- **Frontend**: React 19, Vite, TailwindCSS, DaisyUI, Zustand, Socket.IO Client
- **Backend**: Node.js, Express, MongoDB/Mongoose, Socket.IO, JWT, Cloudinary
- **Database**: MongoDB Atlas

## Configuration

### Port Setup
- **Backend**: Running on port 8000
- **Frontend**: Running on port 5000 (Replit webview)

### Environment Variables
All secrets are securely stored in Replit Secrets:
- `MONGO_URI`: MongoDB Atlas connection string
- `JWT_SECRET`: Secret key for JWT authentication
- `CLOUDINARY_CLOUD_NAME`: Cloudinary cloud name
- `CLOUDINARY_API_KEY`: Cloudinary API key
- `CLOUDINARY_API_SECRET`: Cloudinary API secret

### Workflows
1. **Start Backend**: `cd backend && npm start` (port 8000)
2. **Start Frontend**: `cd frontend && npm run dev` (port 5000, webview)

## Current Status

### ✅ Completed Setup
- [x] Repository cloned and configured
- [x] GitHub links updated in package.json
- [x] Dependencies installed for backend and frontend
- [x] Environment variables configured with Replit Secrets
- [x] Vite configured for Replit (host: 0.0.0.0, port: 5000, allowedHosts: true)
- [x] Backend CORS updated to accept frontend connections
- [x] Socket.IO proxy configured in Vite
- [x] Workflows created and started

### ⚠️ Action Required: MongoDB Atlas IP Whitelist

**The application cannot connect to MongoDB Atlas because Replit's IP address is not whitelisted.**

#### How to Fix:
1. Go to [MongoDB Atlas](https://cloud.mongodb.com/)
2. Log in to your account
3. Select your cluster (Cluster3)
4. Click on "Network Access" in the left sidebar
5. Click "Add IP Address" button
6. Select "Allow Access from Anywhere" option (this will add 0.0.0.0/0)
   - **Note**: This allows connections from any IP. For better security, you can whitelist specific Replit IP ranges instead.
7. Click "Confirm"
8. Wait a few minutes for the changes to propagate

#### After Whitelisting:
Once you've updated the IP whitelist in MongoDB Atlas, the backend will automatically connect to the database. You don't need to do anything else - just refresh your application.

## Recent Changes (Nov 19, 2025)
- Updated package.json repository URLs from Abhijeet14d/Chat-App to aman010mishra/Mern-chat-App
- Configured MongoDB URI and all required secrets
- Adjusted backend port from 5001 to 8000 (Replit compatible port)
- Configured Vite for Replit's proxy environment with proper host settings
- Updated backend CORS to allow frontend connections
- Added Socket.IO proxy configuration for real-time features
