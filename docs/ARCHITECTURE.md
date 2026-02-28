# Quantum Logics Architecture

This document provides a comprehensive overview of the Quantum Logics application architecture, including system design, data flow, and technical decisions.

## Table of Contents

- [System Overview](#system-overview)
- [Architecture Principles](#architecture-principles)
- [High-Level Architecture](#high-level-architecture)
- [Backend Architecture](#backend-architecture)
- [Frontend Architecture](#frontend-architecture)
- [Data Architecture](#data-architecture)
- [Security Architecture](#security-architecture)
- [Deployment Architecture](#deployment-architecture)
- [Scalability Considerations](#scalability-considerations)
- [Technology Stack](#technology-stack)
- [Design Patterns](#design-patterns)
- [API Design](#api-design)
- [State Management](#state-management)

## System Overview

Quantum Logics is a full-stack job board application built as a monorepo with separate backend and frontend submodules. The system enables companies to post job listings and manage applications, while users can browse opportunities and submit applications.

### Key Components

1. **Backend API** - Express.js REST API with MongoDB
2. **Frontend Application** - React SPA with custom CSS
3. **Database** - MongoDB for data persistence
4. **Authentication** - JWT-based authentication system
5. **Admin Panel** - Administrative interface for content management

## Architecture Principles

### Core Principles

1. **Separation of Concerns** - Clear boundaries between frontend, backend, and data layers
2. **Modularity** - Independent, reusable components and services
3. **Scalability** - Designed to handle growth in users and data
4. **Security** - Security-first approach at all layers
5. **Maintainability** - Clean, well-documented code for long-term maintenance
6. **Performance** - Optimized for speed and resource efficiency

### Technical Principles

- **RESTful API Design** - Standard HTTP methods and status codes
- **Component-Based Frontend** - Reusable React components
- **Data Validation** - Input validation at multiple layers
- **Error Handling** - Consistent error handling and logging
- **Environment Configuration** - Environment-specific settings

## High-Level Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Frontend      │    │    Backend      │    │   Database      │
│   (React SPA)   │◄──►│   (Express API) │◄──►│   (MongoDB)     │
│                 │    │                 │    │                 │
│ - User Interface│    │ - REST API      │    │ - Job Data      │
│ - State Mgmt    │    │ - Auth Service  │    │ - User Data     │
│ - API Client    │    │ - Business Logic│    │ - Application   │
│                 │    │ - Validation    │    │   Data          │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

### Data Flow

1. **User Interaction** → Frontend Component
2. **API Request** → Backend Service
3. **Business Logic** → Database Operations
4. **Response** → Frontend Update

## Backend Architecture

### Directory Structure

```
backend/
├── config/           # Configuration files
├── middleware/       # Express middleware
├── models/          # Mongoose data models
├── routes/          # API route definitions
├── services/        # Business logic services
├── server.js        # Application entry point
└── seed.js          # Database seeding script
```

### Core Components

#### 1. Server (`server.js`)

- Express application setup
- Middleware configuration
- Route registration
- Error handling
- Database connection

```javascript
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');

// Middleware setup
app.use(cors());
app.use(express.json());
app.use('/api/auth', authRoutes);
app.use('/api/jobs', jobRoutes);
```

#### 2. Models (`models/`)

Data models using Mongoose ODM:

- **User** - User accounts and authentication
- **Job** - Job listings and details
- **Application** - Job applications and status

```javascript
const jobSchema = new mongoose.Schema({
  title: { type: String, required: true },
  department: { type: String, required: true },
  location: { type: String, required: true },
  type: { type: String, enum: ['Full-time', 'Part-time', 'Contract'] },
  salary: String,
  description: { type: String, required: true },
  requirements: [String],
  postedAt: { type: Date, default: Date.now },
  active: { type: Boolean, default: true }
});
```

#### 3. Routes (`routes/`)

RESTful API endpoints:

- **Auth Routes** - Registration, login, token management
- **Job Routes** - CRUD operations for job listings
- **Application Routes** - Application submission and management
- **User Routes** - User management (admin only)

#### 4. Middleware (`middleware/`)

Custom middleware for:

- **Authentication** - JWT token verification
- **Authorization** - Role-based access control
- **Validation** - Input validation using express-validator
- **Error Handling** - Centralized error processing

#### 5. Services (`services/`)

Business logic abstraction:

- **Email Service** - Notification sending
- **Auth Service** - Token generation and validation
- **Job Service** - Job listing operations

### API Design

#### RESTful Endpoints

```
Authentication:
POST   /api/auth/register    # Register new user
POST   /api/auth/login       # User login

Jobs:
GET    /api/jobs             # Get active jobs (public)
GET    /api/jobs/all         # Get all jobs (admin)
POST   /api/jobs             # Create job (admin)
PUT    /api/jobs/:id         # Update job (admin)
DELETE /api/jobs/:id         # Delete job (admin)

Applications:
POST   /api/applications     # Submit application
GET    /api/applications/mine # My applications
GET    /api/applications     # All applications (admin)
PATCH  /api/applications/:id/status # Update status (admin)

Users:
GET    /api/users            # All users (admin)
```

#### Response Format

```javascript
// Success Response
{
  "success": true,
  "data": { /* response data */ }
}

// Error Response
{
  "success": false,
  "error": "Error message",
  "details": { /* additional error info */ }
}
```

## Frontend Architecture

### Directory Structure

```
frontend/
├── public/           # Static assets
├── src/
│   ├── components/   # Reusable components
│   ├── pages/       # Page components
│   ├── hooks/       # Custom React hooks
│   ├── services/    # API service functions
│   ├── utils/       # Utility functions
│   └── styles/      # CSS and styling
└── App.js           # Root component
```

### Core Components

#### 1. Application Structure

Single-page application with component-based architecture:

- **App.js** - Root component with routing logic
- **Navbar** - Navigation component
- **HomePage** - Landing page with hero and values
- **JobsPage** - Job listings and filtering
- **AuthModal** - Login/registration modal
- **UserDashboard** - User-specific dashboard
- **AdminDashboard** - Administrative interface

#### 2. Component Hierarchy

```
App
├── Navbar
├── HomePage
│   ├── HeroSection
│   ├── ValuesSection
│   ├── TeamSection
│   └── CTAFooter
├── JobsPage
│   ├── JobFilters
│   └── JobList
│       └── JobCard
├── AuthModal
│   ├── LoginForm
│   └── RegisterForm
├── UserDashboard
│   ├── OpenPositions
│   └── MyApplications
└── AdminDashboard
    ├── Overview
    ├── ManageJobs
    ├── Applications
    └── Users
```

#### 3. State Management

Local state management using React hooks:

- **useState** - Component-level state
- **useEffect** - Side effects and API calls
- **useContext** - Global state (authentication)
- **localStorage** - Client-side persistence

```javascript
const [user, setUser] = useState(null);
const [jobs, setJobs] = useState([]);
const [loading, setLoading] = useState(false);

useEffect(() => {
  fetchJobs();
}, []);

const fetchJobs = async () => {
  setLoading(true);
  try {
    const response = await api.get('/jobs');
    setJobs(response.data);
  } catch (error) {
    console.error('Error fetching jobs:', error);
  } finally {
    setLoading(false);
  }
};
```

#### 4. API Integration

Axios-based API client:

```javascript
import axios from 'axios';

const api = axios.create({
  baseURL: process.env.REACT_APP_API_URL || 'http://localhost:5000/api',
  headers: {
    'Content-Type': 'application/json'
  }
});

// Add auth token to requests
api.interceptors.request.use(config => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

#### 5. Styling Architecture

Custom CSS with CSS variables:

```css
:root {
  --bg: #050810;
  --primary: #00d4ff;
  --accent: #ff6b35;
  --card: #0d1424;
  --text: #e8f4ff;
  --text2: #8ba8c7;
}

/* Component-specific styles */
.job-card {
  background: var(--card);
  border: 1px solid var(--primary);
  padding: 1.5rem;
  border-radius: 8px;
  transition: transform 0.2s ease;
}
```

## Data Architecture

### Database Schema

#### MongoDB Collections

**Users Collection**
```javascript
{
  _id: ObjectId,
  name: String,
  email: String (unique),
  password: String (hashed),
  role: String (enum: ['user', 'admin']),
  createdAt: Date,
  updatedAt: Date
}
```

**Jobs Collection**
```javascript
{
  _id: ObjectId,
  title: String,
  department: String,
  location: String,
  type: String,
  salary: String,
  description: String,
  requirements: [String],
  postedAt: Date,
  active: Boolean,
  createdAt: Date,
  updatedAt: Date
}
```

**Applications Collection**
```javascript
{
  _id: ObjectId,
  jobId: ObjectId (ref: 'Job'),
  userId: ObjectId (ref: 'User'),
  jobTitle: String,
  userName: String,
  userEmail: String,
  phone: String,
  experience: String,
  linkedin: String,
  portfolio: String,
  coverLetter: String,
  status: String (enum: ['pending', 'reviewed', 'accepted', 'rejected']),
  appliedAt: Date,
  updatedAt: Date
}
```

### Data Relationships

```
User (1) ←→ (N) Application (N) ←→ (1) Job
```

- Users can submit multiple applications
- Jobs can receive multiple applications
- Applications link users to specific jobs

### Indexing Strategy

```javascript
// Performance indexes
db.users.createIndex({ email: 1 }, { unique: true });
db.jobs.createIndex({ active: 1, postedAt: -1 });
db.applications.createIndex({ userId: 1, jobId: 1 }, { unique: true });
db.applications.createIndex({ status: 1, appliedAt: -1 });
```

## Security Architecture

### Authentication & Authorization

#### JWT-Based Authentication

```javascript
// Token generation
const token = jwt.sign(
  { userId: user._id, role: user.role },
  process.env.JWT_SECRET,
  { expiresIn: '24h' }
);

// Middleware verification
const authenticateToken = (req, res, next) => {
  const authHeader = req.headers['authorization'];
  const token = authHeader && authHeader.split(' ')[1];
  
  if (!token) {
    return res.status(401).json({ error: 'Access token required' });
  }
  
  jwt.verify(token, process.env.JWT_SECRET, (err, user) => {
    if (err) return res.status(403).json({ error: 'Invalid token' });
    req.user = user;
    next();
  });
};
```

#### Role-Based Access Control

```javascript
const requireAdmin = (req, res, next) => {
  if (req.user.role !== 'admin') {
    return res.status(403).json({ error: 'Admin access required' });
  }
  next();
};
```

### Security Measures

1. **Input Validation** - Server-side validation for all inputs
2. **Password Hashing** - bcrypt for password storage
3. **CORS Configuration** - Cross-origin resource sharing setup
4. **Environment Variables** - Sensitive data in environment files
5. **Rate Limiting** - API rate limiting (future enhancement)
6. **HTTPS** - SSL/TLS encryption in production

### Data Protection

```javascript
// Password hashing
const saltRounds = 10;
const hashedPassword = await bcrypt.hash(password, saltRounds);

// Password verification
const isValidPassword = await bcrypt.compare(password, user.password);
```

## Deployment Architecture

### Development Environment

```
Local Development:
├── Frontend (React Dev Server) :3000
├── Backend (Express/Nodemon)   :5000
└── Database (MongoDB)          :27017
```

### Production Architecture

```
Production Deployment:
├── Frontend (Static Files)     → CDN/Static Hosting
├── Backend (Node.js)           → Vercel/Heroku/AWS
├── Database (MongoDB Atlas)    → Cloud Database
└── Environment Variables        → Platform Secrets
```

### Deployment Strategy

#### Backend Deployment (Vercel)

```json
// vercel.json
{
  "version": 2,
  "builds": [
    {
      "src": "server.js",
      "use": "@vercel/node"
    }
  ],
  "routes": [
    {
      "src": "/api/(.*)",
      "dest": "/server.js"
    }
  ]
}
```

#### Frontend Deployment

- Static build optimization
- Asset compression
- CDN distribution
- Environment-specific configurations

## Scalability Considerations

### Horizontal Scaling

1. **Load Balancing** - Multiple backend instances
2. **Database Sharding** - MongoDB sharding for large datasets
3. **Caching** - Redis for session and data caching
4. **CDN** - Content delivery network for static assets

### Performance Optimization

1. **Database Indexing** - Optimized query performance
2. **API Pagination** - Large dataset handling
3. **Image Optimization** - Compressed media assets
4. **Code Splitting** - React lazy loading
5. **Caching Strategy** - Browser and server caching

### Monitoring & Analytics

1. **Application Monitoring** - Error tracking and performance
2. **Database Monitoring** - Query performance and resource usage
3. **User Analytics** - Usage patterns and feature adoption
4. **Security Monitoring** - Threat detection and prevention

## Technology Stack

### Backend Technologies

| Technology | Purpose | Version |
|------------|---------|---------|
| Node.js | Runtime Environment | 18+ |
| Express.js | Web Framework | 4.18.2 |
| MongoDB | Database | 5.0+ |
| Mongoose | ODM | 8.2.0 |
| JWT | Authentication | 9.0.2 |
| bcrypt | Password Hashing | 2.4.3 |
| cors | Cross-Origin Resource Sharing | 2.8.5 |
| dotenv | Environment Variables | 16.4.5 |
| nodemailer | Email Service | 8.0.1 |
| express-validator | Input Validation | 7.0.1 |

### Frontend Technologies

| Technology | Purpose | Version |
|------------|---------|---------|
| React | UI Framework | 19.2.4 |
| React Router | Client-side Routing | 7.13.0 |
| Axios | HTTP Client | 1.13.5 |
| React Testing Library | Testing | 16.3.2 |
| Web Vitals | Performance Metrics | 2.1.4 |

### Development Tools

| Technology | Purpose | Version |
|------------|---------|---------|
| Nodemon | Development Server | 3.1.0 |
| ESLint | Code Linting | Configured |
| Git | Version Control | Latest |
| Vercel | Deployment Platform | Latest |

## Design Patterns

### Backend Patterns

1. **Repository Pattern** - Data access abstraction
2. **Service Layer Pattern** - Business logic separation
3. **Middleware Pattern** - Request processing pipeline
4. **Factory Pattern** - Object creation
5. **Observer Pattern** - Event handling

### Frontend Patterns

1. **Component Pattern** - Reusable UI components
2. **Container/Presentational Pattern** - Logic and UI separation
3. **Custom Hook Pattern** - Reusable state logic
4. **Higher-Order Component Pattern** - Component enhancement
5. **Render Props Pattern** - Component composition

### Architectural Patterns

1. **MVC Pattern** - Model-View-Controller
2. **REST API Pattern** - Resource-based architecture
3. **SPA Pattern** - Single Page Application
4. **Microservice Pattern** - Service separation (future)

## API Design Philosophy

### RESTful Principles

1. **Resource-Based URLs** - Nouns over verbs
2. **HTTP Methods** - Proper use of GET, POST, PUT, DELETE
3. **Status Codes** - Appropriate HTTP status codes
4. **Stateless** - No server-side session state
5. **Cacheable** - Leverage HTTP caching

### API Versioning

```
Current: /api/v1/jobs
Future:  /api/v2/jobs
```

### Error Handling

```javascript
// Consistent error response format
{
  "success": false,
  "error": "Validation failed",
  "details": {
    "field": "email",
    "message": "Invalid email format"
  },
  "timestamp": "2024-01-01T00:00:00.000Z"
}
```

## State Management Strategy

### Client-Side State

1. **Local Component State** - useState for UI state
2. **Global State** - useContext for authentication
3. **Server State** - React Query/SWR for API data
4. **Form State** - Controlled components
5. **URL State** - React Router for navigation state

### Data Synchronization

1. **Optimistic Updates** - Immediate UI updates
2. **Error Recovery** - Rollback on failure
3. **Real-time Updates** - WebSocket integration (future)
4. **Cache Invalidation** - Smart cache management

### Performance Considerations

1. **Memoization** - React.memo, useMemo, useCallback
2. **Code Splitting** - Lazy loading components
3. **Virtual Scrolling** - Large lists optimization
4. **Debouncing** - Search input optimization

---

This architecture document serves as a guide for understanding the Quantum Logics application structure and technical decisions. It will evolve as the application grows and new requirements emerge.
