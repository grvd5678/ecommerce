# 🛍️ ShopHub — Full-Stack E-Commerce & AI Platform

[![Backend CI](https://github.com/grvd5678/ecommerce-backend/actions/workflows/ci.yml/badge.svg)](https://github.com/grvd5678/ecommerce-backend/actions)
[![Frontend CI](https://github.com/grvd5678/ecommerce-frontend/actions/workflows/ci.yml/badge.svg)](https://github.com/grvd5678/ecommerce-frontend/actions)
[![Node.js Version](https://img.shields.io/badge/node-%3E%3D20.0.0-brightgreen.svg)](https://nodejs.org/)
[![License: ISC](https://img.shields.io/badge/License-ISC-blue.svg)](https://opensource.org/licenses/ISC)

A production-ready, full-stack MERN e-commerce platform engineered with a high-performance **React 18 (Vite)** frontend, a secure **Node.js/Express** REST API backend with **Socket.io real-time WebSockets**, a **Redis in-memory caching layer**, and dual database architecture supporting **Local MongoDB Community Server** and **MongoDB Atlas (Cloud)**. Features **Voice-Activated Commerce (Web Speech API)**, **Real-Time Collaborative Group Shopping**, executive **Recharts visual analytics**, integrated **Google Gemini AI** (conversational shopping assistant & SEO marketing generator), dual payment gateways (**Stripe & Razorpay**), automated **GitHub Actions CI/CD**, multi-provider email dispatch (**Resend HTTPS API / SendGrid / Gmail SMTP** with server log OTP fallback), and **Docker** containerization.

---

## 🌐 Live Deployments

| Service | Platform | Live URL |
|---|---|---|
| **Frontend Web App** | Render (Static Site) | [https://ecommerce-frontend-l3zz.onrender.com](https://ecommerce-frontend-l3zz.onrender.com) |
| **Backend REST API** | Render (Web Service) | [https://ecommerce-api-tio6.onrender.com](https://ecommerce-api-tio6.onrender.com) |
| **API Health Check** | Render | [https://ecommerce-api-tio6.onrender.com/health](https://ecommerce-api-tio6.onrender.com/health) |

---

## 📑 Table of Contents

- [Architectural Overview](#-architectural-overview)
- [Key Features](#-key-features)
  - [1. 🎙️ Voice-Activated Commerce & Navigation](#1-️-voice-activated-commerce--navigation)
  - [2. 👥 Real-Time Collaborative Group Cart (Socket.io)](#2--real-time-collaborative-group-cart-socketio)
  - [3. ⚡ Redis Caching Layer (Sub-5ms Performance)](#3--redis-caching-layer-sub-5ms-performance)
  - [4. 🛍️ Customer Shopping Experience](#4-️-customer-shopping-experience)
  - [5. 📊 Business Intelligence & Admin Suite](#5--business-intelligence--admin-suite)
  - [6. 🤖 Generative AI Layer (Google Gemini)](#6--generative-ai-layer-google-gemini)
- [Tech Stack](#-tech-stack)
- [Project Structure](#-project-structure)
- [Quick Start (Local Development)](#-quick-start-local-development)
- [Database & Cache Setup (Local vs. Cloud)](#-database--cache-setup-local-vs-cloud)
- [Docker Setup (1-Command Orchestration)](#-docker-setup-1-command-orchestration)
- [Environment Variables](#-environment-variables)
- [API Reference](#-api-reference)
- [Testing & CI/CD](#-testing--cicd)
- [Security & Production Hardening](#-security--production-hardening)
- [License](#-license)

---

## 🏗️ Architectural Overview

```mermaid
flowchart TD
    Client["React 18 + Vite (Tailwind CSS, Web Speech API, Socket.io-client)"]
    WS["Socket.io WebSocket Server (Room-based State Sync)"]
    Proxy["Nginx / Express Reverse Proxy"]
    API["Node.js + Express REST API (Node 20, ES Modules)"]
    Redis[("Redis In-Memory Cache (Cache-Aside, Sub-5ms)")]
    DB[("MongoDB: Local Server (27017) or Cloud Atlas (SRV)")]
    Gemini["Google Gemini AI API (gemini-3.6-flash)"]
    Email["Email Dispatch: Resend (Port 443 HTTPS) / SendGrid / Gmail SMTP"]
    Payments["Stripe & Razorpay Gateways"]

    Client <-->|WebSockets (Live Group Cart)| WS
    Client -->|HTTPS / REST| Proxy
    Proxy --> API
    API <-->|ioredis (Cache-Aside & Invalidation)| Redis
    API -->|Mongoose 8 Auto-TLS Detection| DB
    API -->|SDK| Gemini
    API -->|HTTPS / SMTP| Email
    API -->|Webhooks & Orders| Payments
```

---

## ✨ Key Features

### 1. 🎙️ Voice-Activated Commerce & Navigation
- **Hands-Free Product Search:** Click the animated microphone icon in the header and speak naturally (e.g. *"Show laptops"*, *"Search wireless headphones"*).
- **Voice-Guided Page Navigation:** Command the app verbally (*"Go to cart"*, *"View order history"*, *"Open profile"*, *"Go home"*).
- **Text-to-Speech (TTS) Voice Responses:** ShopHub's AI assistant speaks back audio confirmations in real time using the browser's native `speechSynthesis`.
- **Multimodal AI Assistant:** Floating chatbot allows both text input and voice conversations with Google Gemini AI.

### 2. 👥 Real-Time Collaborative Group Cart (Socket.io)
- **Shared Group Shopping Sessions:** Click *"Start Group Order"* to generate an instant 6-character room code or one-click invite URL (`/cart?room=ROOM123`).
- **Live Bi-Directional State Synchronization:** When User A adds an item, changes quantities, or removes an item, the cart updates live on User B's screen via WebSockets.
- **Active Member Counter & Real-Time Toast Notifications:** Shows connected peer count and triggers live toast events (*"👥 Gourav added Sony Headphones"*).

### 3. ⚡ Redis Caching Layer (Sub-5ms Performance)
- **Cache-Aside Architecture:** High-traffic read endpoints (`GET /api/products`, `GET /api/admin/analytics`, `GET /api/products/categories`) are cached in Redis memory with configurable TTLs.
- **Automated Cache Invalidation:** Whenever an admin creates, updates, or deletes a product, matching cache keys (`products:*`) are purged automatically.
- **Graceful Degraded Mode:** If Redis is offline, requests seamlessly fall back to MongoDB with zero downtime or user interruption.

### 4. 🛍️ Customer Shopping Experience
- **Resilient Authentication & OTP Verification:** Stateless JWT authentication, bcrypt password hashing, and zero-hang OTP verification. Supports **Resend.com** (HTTPS port 443), SendGrid, Gmail SMTP, and auto-logged console OTP fallbacks.
- **Product Discovery:** Instant search with debounce, multi-criteria filtering (category, price range, ratings), sorting, and dynamic product recommendations.
- **Payment Processing:** Integrated dual gateways supporting **Stripe** and **Razorpay** with backend cryptographic signature verification.
- **Order Lifecycle:** Self-service order history, live visual order tracker (`OrderTracker`), and order cancellation workflows.
- **Engagement & Social Proof:** Star ratings, customer review submission, helpful vote counters, and back-in-stock notifications (`NotifyMe`).

### 5. 📊 Business Intelligence & Admin Suite
- **Visual Analytics Dashboard (Powered by Recharts):**
  - **KPI Metrics:** Total gross revenue, order volume, catalog size, active users, and Average Order Value (AOV).
  - **Revenue & Orders Area Chart:** Gradient timeline tracking daily gross sales.
  - **Fulfillment Donut Chart:** Status breakdown (`Delivered`, `Processing`, `Pending`, `Shipped`, `Cancelled`).
  - **Top 5 Best-Sellers Bar Chart:** Units sold and gross revenue per item.
  - **Category Breakdown:** Inventory distribution grouped by department.
  - **Time Range Filter:** 7 Days, 30 Days, 90 Days, and All-Time data aggregation.
- **Catalog Management:** Full CRUD operations for products with category tagging, stock management, and image asset links.
- **User & Order Management:** Role-based access control (Admin/Customer), order status updates, and user role promotion.

### 6. 🤖 Generative AI Layer (Google Gemini)
- **AI Shopping Assistant:** Conversational chatbot widget on the storefront answering customer queries regarding product features, pricing, and shipping.
- **AI Product Description Generator:** One-click *"✨ Generate with AI"* button in the Admin Product Editor that generates high-converting, SEO-optimized marketing copy from a product title using `gemini-3.6-flash`.

---

## 🛠️ Tech Stack

### Frontend
- **Core:** React 18, Vite 7, React Router DOM v6
- **Voice Commerce:** Web Speech API (`SpeechRecognition` & `speechSynthesis`)
- **Real-Time WebSockets:** `socket.io-client`
- **Styling:** Tailwind CSS, Lucide React icons
- **Data Visualization:** Recharts 2.x
- **Animations:** Framer Motion (Page transitions, cart drawer, micro-interactions)
- **State & Networking:** Context API + useReducer, Axios (with auth interceptors)

### Backend
- **Core:** Node.js 20 (ES Modules), Express.js 5
- **Real-Time Server:** `socket.io` HTTP Server
- **In-Memory Cache:** `ioredis` (Redis 7)
- **Database:** MongoDB Community Server (Local) & MongoDB Atlas (Cloud), Mongoose 9 (with TLS auto-detection)
- **AI & Cloud Services:** `@google/generative-ai` (Gemini), Resend API, `@sendgrid/mail`, Nodemailer
- **Security:** Helmet, CORS, Express Rate Limit, CSRF protection
- **Testing:** Jest, Supertest, MongoDB Memory Server

### DevOps & Infrastructure
- **Containerization:** Docker, Docker Compose (MongoDB, Redis, Node.js Backend, Nginx Frontend)
- **CI/CD:** GitHub Actions (Automated build verification & Jest test suites)
- **Hosting:** Render (Cloud Native Web Service & Static Site)

---

## 📂 Project Structure

```
ecommerce/
├── docker-compose.yml              # Multi-service container orchestration (Mongo, Redis, API, Web)
├── README.md                       # Master monorepo documentation
├── ecommerce-backend/
│   ├── .github/workflows/ci.yml    # Backend GitHub Actions CI pipeline
│   ├── config/                     # Database (TLS auto-detect), Redis client & configs
│   ├── controllers/                # Business logic handlers (auth, products, etc.)
│   ├── middleware/                 # Auth (JWT), Cache-aside, Validation (Joi), Rate Limit
│   ├── models/                     # Mongoose schemas (8 collections: User, Product, Order, etc.)
│   ├── routes/                     # REST API route definitions
│   ├── tests/                      # Jest & Supertest integration suites
│   ├── utils/                      # Socket server handler, Resend/SMTP email & loggers
│   ├── Dockerfile                  # Node 20 Alpine container
│   ├── server.js                   # Application entry point (Express + Socket.io)
│   ├── seed.js                     # 11-product database seeder
│   └── package.json
└── ecommerce-frontend/
    ├── .github/workflows/ci.yml    # Frontend GitHub Actions CI pipeline
    ├── src/
    │   ├── components/             # GroupCartBanner, SearchHeader (Voice), Chatbot, Recharts
    │   ├── context/                # Auth, Cart (Group socket sync), Wishlist, Theme contexts
    │   ├── hooks/                  # useVoiceCommand custom Web Speech hook
    │   ├── pages/                  # Route views (Home, AdminDashboard, Login, Cart)
    │   └── utils/                  # Socket.io client, Axios instance & reducers
    ├── Dockerfile                  # Multi-stage Vite + Nginx container
    ├── nginx.conf                  # Production SPA routing configuration
    └── package.json
```

---

## 🚀 Quick Start (Local Development)

### Prerequisites
- Node.js >= 20.0.0
- npm >= 10.0.0
- MongoDB Community Server (local) OR MongoDB Atlas account
- (Optional) Redis (local or Docker container)

### 1. Clone the Monorepo
```bash
git clone https://github.com/grvd5678/ecommerce.git
cd ecommerce
```

### 2. Backend Setup
```bash
cd ecommerce-backend
npm install

# Configure MONGODB_URI=mongodb://127.0.0.1:27017/ecommerce in .env
npm run dev             # Starts API & WebSocket server on http://localhost:5000
```

To seed the initial 11 products into your database:
```bash
node seed.js
```

### 3. Frontend Setup
Open a second terminal:
```bash
cd ecommerce-frontend
npm install
npm run dev             # Starts SPA on http://localhost:5173
```

---

## 🗄️ Database & Cache Setup (Local vs. Cloud)

### Database (Automatic TLS Detection)
- Local: Disables TLS for `127.0.0.1:27017` / `localhost`.
- Cloud: Enforces TLS & SRV for `mongodb+srv://` (MongoDB Atlas).

### Cache Service (Redis)
- Run Redis locally or via Docker (`docker compose up -d redis`).
- If Redis is absent, ShopHub runs seamlessly in fallback mode.

---

## 🐳 Docker Setup (1-Command Orchestration)

Spin up the entire stack (**MongoDB 7**, **Redis 7**, **Backend API**, and **Frontend Nginx**) locally with a single command:

```bash
docker compose up -d --build
```

- **Frontend Application:** `http://localhost:80`
- **Backend API & WebSockets:** `http://localhost:5000/api`
- **Redis Cache:** `localhost:6379`
- **MongoDB Database:** `localhost:27017` (stored in persistent volume `mongodb_data`)

To stop containers:
```bash
docker compose down
```

---

## 📄 License

This project is licensed under the [ISC License](LICENSE).
