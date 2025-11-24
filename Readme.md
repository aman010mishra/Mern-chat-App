
# Chat Application
This is a full-stack chat application built with **React**, **Node.js**, **Express**, **MongoDB**, and **Socket.IO**. The application allows users to register, log in, and engage in real-time messaging with other users. It also supports features like profile management, image uploads, and theme customization.

APPLICATION LINK: "https://mern-hack-chat.onrender.com/"
---

## 🚀 DevOps and Deployment

This project follows a **DevOps-centric workflow** to ensure scalable, secure, and automated deployment. The complete application is containerized using **Docker**, and service orchestration is managed using **Docker Compose**. A multi-stage **CI/CD pipeline** is configured with **GitHub Actions** to automate build, test, and deployment processes.

### 🔧 CI/CD Pipeline

- **Tool Used**: GitHub Actions
- **Trigger**: Every push to the `main` branch
- **Steps**:
  1. Checkout the latest code
  2. Set up Docker and Docker Compose
  3. Securely generate `.env` file using GitHub Secrets
  4. Build Docker images for both `frontend` and `backend`
  5. Push images to Docker Hub
  6. [Optionally] Pull the latest image on Render for deployment

### 🐳 Docker & Compose

- Each service (frontend, backend) is containerized using its own Dockerfile.
- Services are orchestrated using `docker-compose.yml`, which manages:
  - Networking (custom Docker network)
  - MongoDB service (for local development)
  - Port mappings and container health
  - Shared volumes (for MongoDB persistence)

### 🔐 Secret Management

Sensitive information like:
- `MONGO_URI`
- `JWT_SECRET`
- `CLOUDINARY_API_KEY`

...are managed via **GitHub Secrets**, injected at runtime during CI/CD to ensure security and compliance.

### ☁️ Cloud Deployment

The app is deployed on **Render** using prebuilt Docker images from **Docker Hub**:
- `chatapp-frontend`
- `chatapp-backend`

Each service is independently hosted with environment variables set through the Render dashboard.

---

## 🧩 Features

### Frontend
- **User Authentication**: Sign up, log in, and log out functionality.
- **Real-Time Messaging**: Send and receive messages instantly using **Socket.IO**.
- **Image Uploads**: Attach images to messages and upload profile pictures.
- **Responsive Design**: Fully responsive UI built with **TailwindCSS** and **DaisyUI**.
- **Theme Customization**: Choose from multiple themes for a personalized experience.
- **User Status**: View online/offline status of users in real-time.

### Backend
- **RESTful API**: Built with **Express.js** to handle authentication, messaging, and user management.
- **Database**: **MongoDB** for storing user and message data.
- **Authentication**: Secure user authentication using **JWT** and cookies.
- **Cloudinary Integration**: Store and retrieve images using **Cloudinary**.
- **WebSocket Support**: Real-time communication powered by **Socket.IO**.

---

## ⚙️ Tech Stack

### Frontend
- **React**: For building the user interface.
- **Vite**: For fast development and build processes.
- **Zustand**: For state management.
- **TailwindCSS** and **DaisyUI**: For styling.

### Backend
- **Node.js**: For server-side JavaScript.
- **Express.js**: For building the RESTful API.
- **MongoDB**: For database storage.
- **Socket.IO**: For real-time communication.
- **Cloudinary**: For image storage and management.

---

## 📦 Installation

### Prerequisites
- **Node.js** and **npm**
- **MongoDB Atlas URI**
- **Cloudinary credentials**

### Backend Setup
```bash
cd backend
npm install
```

Create a `.env` file:
```env
MONGO_URI=<your-mongodb-connection-string>
PORT=5001
CLOUDINARY_CLOUD_NAME=<your-cloudinary-cloud-name>
CLOUDINARY_API_KEY=<your-cloudinary-api-key>
CLOUDINARY_API_SECRET=<your-cloudinary-api-secret>
JWT_SECRET=<your-jwt-secret>
```

```bash
npm run dev
```

### Frontend Setup
```bash
cd frontend
npm install
npm run dev
```

---

## 📂 Folder Structure

```bash
backend/
├── .env
├── Dockerfile
├── src/
│   ├── controllers/
│   ├── lib/
│   ├── middleware/
│   ├── models/
│   ├── routes/
│   └── seeds/

frontend/
├── Dockerfile
├── src/
│   ├── components/
│   ├── constants/
│   ├── lib/
│   ├── pages/
│   ├── store/
│   └── index.css
```

---

## 🔌 API Endpoints

### Authentication
- `POST /api/auth/register`: Register a new user.
- `POST /api/auth/login`: Log in a user.
- `POST /api/auth/logout`: Log out a user.
- `PUT /api/auth/update-profile`: Update user profile.
- `GET /api/auth/check`: Check authentication status.

### Messaging
- `GET /api/msg/users`: Get all users except the logged-in user.
- `GET /api/msg/:id`: Get messages between the logged-in user and another user.
- `POST /api/msg/send/:id`: Send a message to another user.

---

## 📝 License

This project is licensed under the MIT License. See the LICENSE file for details.

---

## 🙏 Acknowledgments

- React for the frontend framework.
- Node.js and Express for backend functionality.
- MongoDB for the database.
- Cloudinary for media storage.
- Socket.IO for real-time messaging.
- TailwindCSS and DaisyUI for styling.
- Docker, GitHub Actions, and Render for powering the DevOps workflow.
