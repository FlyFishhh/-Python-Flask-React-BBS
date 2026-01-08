# 🚀 Flask + React Forum System

<div align="center">


**A modern full-stack forum system built with Flask and React, featuring real-time chat, Markdown support, and production-ready deployment**

[Quick Start](#quick-start) · [Deployment Guide](#deployment)

---

## 📖 Overview

This is a **full-stack forum system** built with **Flask** (backend) and **React** (frontend), implementing complete separation of concerns between client and server. The project features modern architecture with core forum functionality including user authentication, topic management, discussion threads, and real-time private messaging. It serves as an excellent reference for learning about separated frontend/backend architecture, WebSocket real-time communication, and production deployment practices.

### ✨ Key Features

- ✅ **Complete Frontend/Backend Separation** - RESTful API with Flask + React SPA, independently deployable and scalable
- ✅ **Real-time Communication** - WebSocket-based instant messaging powered by Flask-SocketIO
- ✅ **Markdown Support** - Full Markdown editing and rendering for topics and replies
- ✅ **Asynchronous Task Processing** - Celery-powered async email notifications
- ✅ **User System** - Registration, authentication, avatar upload, and profile management
- ✅ **@Mention Functionality** - Automatic email notifications when users are mentioned in replies
- ✅ **Board Management** - Multi-board categorization (Q&A, Featured, Discussion, etc.)
- ✅ **Production-Ready Deployment** - Complete deployment solution with Nginx + Gunicorn + Supervisor
- ✅ **Code Highlighting** - Syntax highlighting for code blocks via Highlight.js

---

## 🎯 Technology Stack

### Backend Technologies

| Technology | Description | Version |
|------------|-------------|---------|
| **Flask** | Lightweight web framework | 2.0+ |
| **SQLAlchemy** | Python ORM framework | - |
| **Flask-SocketIO** | WebSocket real-time communication | - |
| **MySQL** | Relational database | 5.7+ |
| **Redis** | Caching and token storage | - |
| **Celery** | Asynchronous task queue | - |
| **Gunicorn** | WSGI HTTP server | - |
| **Nginx** | Reverse proxy server | - |
| **Supervisor** | Process management tool | - |

### Frontend Technologies

| Technology | Description | Version |
|------------|-------------|---------|
| **React** | UI framework | 16.8.6 |
| **React Router** | Frontend routing | 5.0.1 |
| **React-Bootstrap** | UI component library | 1.0.0-beta.9 |
| **React-MDE** | Markdown editor | 7.5.0 |
| **React-Markdown** | Markdown rendering | 4.1.0 |
| **Socket.IO-Client** | WebSocket client | 2.2.0 |
| **Highlight.js** | Code syntax highlighting | - |

---

## 🏗️ System Architecture

### Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                        Client Browser                            │
│                   (React SPA Application)                        │
└────────────────┬────────────────────────────────────────────────┘
                 │
                 │ HTTP/HTTPS + WebSocket
                 ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Nginx Reverse Proxy                         │
│  ┌──────────────┬──────────────────┬────────────────────────┐   │
│  │ /* (static)  │ /api/* (API)     │ /socket.io/* (WS)      │   │
│  │ → Frontend   │ → Backend API    │ → WebSocket Service    │   │
│  └──────┬───────┴──────────┬───────┴────────────┬───────────┘   │
└─────────┼──────────────────┼────────────────────┼───────────────┘
          │                  │                    │
          ▼                  ▼                    ▼
┌──────────────────┐ ┌─────────────────┐ ┌────────────────────┐
│  Static Files    │ │ Gunicorn:2000   │ │ Gunicorn:5000      │
│   /var/www/      │ │ (Main API)      │ │ (WebSocket)        │
│   bbs_frontend/  │ │ - 3 workers     │ │ - 1 worker         │
│   build/         │ │ - gevent        │ │ - eventlet         │
└──────────────────┘ └────────┬────────┘ └──────────┬───────────┘
                              │                     │
                              ├─────────────────────┤
                              │                     │
                 ┌────────────┼─────────────────────┼─────────────┐
                 │            │                     │             │
                 ▼            ▼                     ▼             ▼
         ┌──────────────┐ ┌──────────┐ ┌──────────────┐ ┌──────────────┐
         │    MySQL     │ │  Redis   │ │   Celery     │ │ File Storage │
         │ (Persistent) │ │ (Tokens) │ │ (Async Mail) │ │  (Avatars)   │
         └──────────────┘ └──────────┘ └──────────────┘ └──────────────┘
```

### Project Structure

```
bbs-master/
├── bbs_backend/                    # Flask backend service
│   ├── app.py                      # Main application entry
│   ├── config.py                   # Configuration file (manual creation required)
│   ├── secret.py                   # Secret keys (manual creation required)
│   ├── reset.py                    # Database initialization script
│   ├── utils.py                    # Utility functions
│   ├── chat.py                     # Real-time chat module
│   ├── wsgi.py                     # WSGI main service entry
│   ├── wsgi_chat.py                # WSGI chat service entry
│   ├── models/                     # Data model layer
│   │   ├── base_model.py           # Base model
│   │   ├── user.py                 # User model
│   │   ├── topic.py                # Topic model
│   │   ├── reply.py                # Reply model
│   │   ├── board.py                # Board model
│   │   ├── message.py              # Private message model
│   │   ├── message_content.py      # Message content model
│   │   └── token.py                # Token model
│   └── routes_api/                 # API routing layer
│       ├── api_index.py            # User authentication API
│       ├── api_topic.py            # Topic API
│       ├── api_reply.py            # Reply API
│       ├── api_chat.py             # WebSocket API
│       └── mail.py                 # Email notifications
│
├── bbs_frontend/                   # React frontend application
│   ├── package.json                # Dependency configuration
│   ├── public/                     # Public resources
│   └── src/
│       ├── App.js                  # Main application component
│       ├── index.js                # Application entry point
│       ├── config.js               # Client configuration (manual creation required)
│       ├── api/                    # API call layer
│       ├── components/             # React components
│       │   ├── authentication/     # Authentication related
│       │   ├── topic/              # Topic related
│       │   ├── reply/              # Reply related
│       │   ├── common/             # Common components
│       │   ├── profile/            # User profile
│       │   ├── setting/            # User settings
│       │   └── markdown/           # Markdown editor
│       ├── chat/                   # Real-time chat
│       └── css/                    # Style files
│
└── deploy/                         # Deployment configuration
    ├── deploy_v2.sh                # One-click deployment script
    ├── bbs.nginx                   # Nginx configuration
    └── bbs_backend.conf            # Supervisor configuration
```

### Database Schema

```
┌─────────────┐      ┌─────────────┐      ┌─────────────┐
│    User     │      │    Board    │      │    Topic    │
├─────────────┤      ├─────────────┤      ├─────────────┤
│ id          │      │ id          │      │ id          │
│ username    │◄─┐   │ title       │◄─┐   │ title       │
│ password    │  │   └─────────────┘  │   │ content     │
│ email       │  │                     │   │ user_id     │─┐
│ image       │  │                     └───│ board_id    │ │
│ signature   │  │                         │ views       │ │
└─────────────┘  │   ┌─────────────┐      │ floors_total│ │
                 └───│ Reply       │◄─────│             │ │
                     ├─────────────┤      └─────────────┘ │
                     │ id          │                       │
                     │ content     │                       │
                     │ topic_id    │───────────────────────┘
                     │ user_id     │
                     │ floor       │
                     └─────────────┘

┌─────────────┐      ┌──────────────────┐
│ UserToken   │      │ Message          │
├─────────────┤      ├──────────────────┤
│ id          │      │ id               │
│ token       │      │ room_id          │
│ user_id     │      │ sender_id        │
└─────────────┘      │ receiver_id      │
                     └──────────────────┘
                              │
                              │
                     ┌────────▼──────────┐
                     │ MessageContent    │
                     ├───────────────────┤
                     │ id                │
                     │ message_id        │
                     │ content           │
                     │ sender_id         │
                     │ receiver_id       │
                     └───────────────────┘
```

---

## 📡 API Documentation

### Authentication Endpoints
```
POST   /api/register              User registration
POST   /api/login                 User login
POST   /api/user                  Get current user information
POST   /api/token                 Validate token
POST   /api/reset/send            Send password reset email
POST   /api/reset/update          Update password after reset
```

### Topic Endpoints
```
GET    /api/index                 Get topic list (supports pagination and board filtering)
GET    /api/topic/boards          Get all boards
GET    /api/topic/<id>            Get topic details
POST   /api/topic/add             Create new topic (authentication required)
```

### Reply Endpoints
```
POST   /api/reply/add             Add reply (authentication required, supports @mentions)
```

### User Endpoints
```
GET    /api/user/<id>             Get user information
POST   /api/profile               Get personal profile (authentication required)
POST   /api/image/add             Upload avatar (authentication required)
POST   /api/setting/profile       Update profile (authentication required)
POST   /api/setting/password      Change password (authentication required)
```

### Chat Endpoints
```
POST   /api/chat/new              Create new private message conversation
POST   /api/chat/contacts         Get all chat contacts
POST   /api/chat/messages         Get message history
WS     /socket.io                 WebSocket connection
  ├─ join(room, token)            Join chat room
  └─ send(content, room_id, token) Send message
```

---

## 🚀 Quick Start

### Prerequisites

- Python 3.7+
- Node.js 12+
- MySQL 5.7+
- Redis
- Nginx (for production)

### Local Development

#### 1. Clone the Repository

```bash
git clone https://github.com/your-username/bbs.git
cd bbs
```

#### 2. Backend Configuration

Create configuration file `bbs_backend/config.py`:

```python
# Email sender address
admin_mail = "your-email@example.com"
# Default user email address
test_mail = "default@example.com"
# Server name (include protocol)
server_name = "http://localhost:3000"
```

Create secret file `bbs_backend/secret.py`:

```python
# Flask session secret key
secret_key = "your-random-secret-key"
# Email sender password
mail_password = "your-email-password"
# MySQL database password
database_password = 'your-mysql-password'
```

#### 3. Install Backend Dependencies

```bash
cd bbs_backend
pip install -r requirements.txt  # Or install dependencies manually if no requirements.txt
```

#### 4. Initialize Database

```bash
# Ensure MySQL and Redis are running
python reset.py
```

#### 5. Start Backend Services

```bash
# Start main API service
gunicorn wsgi --bind localhost:2000 --workers 3 --worker-class gevent

# Start chat service (in separate terminal)
gunicorn wsgi_chat --bind localhost:5000 --workers 1 --worker-class eventlet

# Start Celery email service (in separate terminal)
celery worker -A routes_api.mail.celery
```

#### 6. Frontend Configuration

Create configuration file `bbs_frontend/src/config.js`:

```javascript
// Domain or IP address (without protocol)
const server_name = 'localhost'
// Whether to use HTTPS
const use_https = false

export {
    server_name as server,
    use_https as useHTTPS,
}
```

#### 7. Install Frontend Dependencies and Start

```bash
cd bbs_frontend
yarn install
yarn start
```

Visit `http://localhost:3000` to see the application running.

---

## 🔧 Deployment

(Tested on Tencent Cloud Ubuntu Server 18.04.1 LTS 64-bit)

### 1. Log in to Server

Use the default ubuntu account to log in to your server.

### 2. Clone the Repository

```bash
cd /home/ubuntu
git clone https://github.com/your-username/bbs.git
```

### 3. Switch to Root Account

```bash
# Set root password
sudo passwd root
# Switch to root
su root
```

### 4. Create Frontend Configuration

In `/home/ubuntu/bbs/bbs_frontend/src`, create `config.js`:

```javascript
// Domain or IP address (without protocol)
const server_name = 'your-domain.com'
// Whether to use HTTPS
const use_https = false

export {
    server_name as server,
    use_https as useHTTPS,
}
```

### 5. Create Backend Configuration

In `/home/ubuntu/bbs/bbs_backend`, create `config.py`:

```python
# Email sender address
admin_mail = "your-email@example.com"
# Default user email address
test_mail = "default@example.com"
# Server name (include protocol)
server_name = "http://your-domain.com"
```

### 6. Create Backend Secrets

In `/home/ubuntu/bbs/bbs_backend`, create `secret.py`:

```python
# Flask session secret key
secret_key = "your-random-secret-key"
# Email sender password
mail_password = "your-email-password"
# MySQL database password
database_password = 'your-mysql-password'
```

### 7. Run Deployment Script

```bash
cd /home/ubuntu/bbs
# Pass database password as argument
sh deploy/deploy_v2.sh your-mysql-password
```

The deployment script automatically:
- Installs system dependencies (Git, Python3, Node.js, MySQL, Redis, Nginx)
- Installs Python and Node.js dependencies
- Initializes the database
- Builds frontend code
- Configures Nginx reverse proxy
- Configures Supervisor process management
- Starts all services

### 8. HTTPS Support (Optional)

Update `config.py` `server_name` to `https://your-domain.com`

Update `config.js` `use_https` to `true`

Rebuild frontend in `/var/www/bbs_frontend`:

```bash
yarn run build
```

Configure SSL certificate using Certbot:

```bash
# Update /etc/nginx/sites-enabled/bbs server_name to your domain first
sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository universe
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install certbot python-certbot-nginx
sudo certbot --nginx
```

---

## 💡 Core Implementation Details

### 1. Authentication Mechanism

- **Password Security**: SHA256 hashing with salt
- **Token Management**: UUID tokens stored in Redis
- **Client Storage**: Tokens persisted in browser localStorage
- **Request Authentication**: Token validation on each API request

### 2. Real-time Chat Implementation

- **Technology**: Flask-SocketIO for WebSocket communication
- **Room Management**: Each conversation has a unique UUID-based room_id
- **Message Delivery**: Real-time broadcast to room participants
- **Persistence**: All messages stored in MySQL database

### 3. @Mention Functionality

- **Content Parsing**: Extract @username mentions from reply content
- **Async Notifications**: Celery queues email notifications to mentioned users
- **Email Content**: Includes reply content and topic link

### 4. Markdown Support

- **Editor**: React-MDE for content writing
- **Rendering**: React-Markdown for content display
- **Code Highlighting**: Highlight.js integration for syntax highlighting

---

## 📦 Production Architecture

### Process Management (Supervisor)

Three backend services run continuously:

1. **bbs_backend** - Main API Service
   ```bash
   gunicorn wsgi --bind localhost:2000 --workers 3 --worker-class gevent
   ```

2. **bbs_backend_chat** - WebSocket Chat Service
   ```bash
   gunicorn wsgi_chat --bind localhost:5000 --workers 1 --worker-class eventlet
   ```

3. **bbs_backend_mail_celery** - Async Email Service
   ```bash
   celery worker -A routes_api.mail.celery
   ```

### Nginx Reverse Proxy Configuration

```nginx
server {
    listen 80;
    server_name your-domain.com;

    # Frontend static files
    location / {
        root /var/www/bbs_frontend/build;
        try_files $uri /index.html;
    }

    # Backend API
    location /api/ {
        proxy_pass http://localhost:2000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    # WebSocket
    location /socket.io/ {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }

    # User avatars
    location /api/images/ {
        alias /var/www/bbs_backend/images/;
    }
}
```

---

## 🎓 Technical Learning Points

This project is ideal for learning:

### Frontend/Backend Separation Architecture
- RESTful API design principles
- Independent deployment strategies
- Token-based authentication

### React Ecosystem
- Component-based development
- React Router navigation
- React Hooks patterns

### Flask Web Development
- Application structure design
- SQLAlchemy ORM usage
- Blueprint and routing patterns

### WebSocket Real-time Communication
- Flask-SocketIO integration
- Room and broadcast mechanisms
- Bidirectional client-server protocols

### Asynchronous Task Processing
- Celery task queues
- Async email delivery
- Redis as message broker

### Production Deployment
- Nginx reverse proxy
- Gunicorn WSGI server
- Supervisor process management
- SSL certificate configuration

---

## 🛠️ Future Enhancements

Potential features for extension:

- [ ] Article like/favorite functionality
- [ ] User follow/follower system
- [ ] Topic tagging system
- [ ] Full-text search (Elasticsearch)
- [ ] Image upload and CDN integration
- [ ] Admin dashboard
- [ ] Role-based access control
- [ ] Nested comment threads
- [ ] Edit history for topics/replies
- [ ] In-app notification system
- [ ] RSS feed support
- [ ] Mobile responsive optimization

---

## 🤝 Contributing

Issues and Pull Requests are welcome!

---

<div align="center">
**⭐ If you find this project helpful, please give it a star!**

Made with ❤️ by Kelly

