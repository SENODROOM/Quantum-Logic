# Deployment Guide

This comprehensive guide covers deploying Quantum Logics to various environments, from development setups to production deployments.

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Environment Setup](#environment-setup)
- [Development Deployment](#development-deployment)
- [Staging Deployment](#staging-deployment)
- [Production Deployment](#production-deployment)
- [Platform-Specific Guides](#platform-specific-guides)
- [Database Setup](#database-setup)
- [Environment Variables](#environment-variables)
- [SSL/HTTPS Setup](#sslhttps-setup)
- [Monitoring and Logging](#monitoring-and-logging)
- [Backup and Recovery](#backup-and-recovery)
- [Troubleshooting](#troubleshooting)

## Overview

Quantum Logics is designed for flexible deployment across various platforms and environments. This guide covers:

- **Development**: Local development setup
- **Staging**: Pre-production testing environment
- **Production**: Live deployment with best practices

### Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Frontend      │    │    Backend      │    │   Database      │
│   (Static)      │    │   (Serverless)  │    │   (Cloud)       │
│                 │    │                 │    │                 │
│ • Netlify       │    │ • Vercel        │    │ • MongoDB Atlas │
│ • Vercel        │    │ • AWS Lambda    │    │ • AWS DocumentDB│
│ • AWS S3        │    │ • Heroku        │    │ • DigitalOcean  │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

## Prerequisites

### Required Accounts and Services

1. **Version Control**: GitHub account
2. **Hosting**: Vercel, Netlify, or similar
3. **Database**: MongoDB Atlas or similar
4. **Email Service**: SendGrid, Mailgun, or Gmail
5. **Domain**: Custom domain (optional)
6. **SSL Certificate**: Usually provided by hosting platform

### Technical Requirements

- **Node.js**: 18+ for local development
- **Git**: For version control
- **CLI Tools**: Platform-specific CLI tools
- **Text Editor**: VS Code, WebStorm, or similar

## Environment Setup

### Environment Classification

#### Development Environment
- **Purpose**: Local development and testing
- **Database**: Local MongoDB or Atlas sandbox
- **Authentication**: Test credentials
- **Features**: Debug logging, hot reload

#### Staging Environment
- **Purpose**: Pre-production testing
- **Database**: Atlas staging cluster
- **Authentication**: Production-like credentials
- **Features**: Production configuration, limited data

#### Production Environment
- **Purpose**: Live application
- **Database**: Atlas production cluster
- **Authentication**: Real user credentials
- **Features**: Full security, monitoring, backups

### Environment Variables Structure

Create separate environment files for each environment:

```bash
# .env.development
NODE_ENV=development
PORT=5000
MONGO_URI=mongodb://localhost:27017/quantum_logics_dev
JWT_SECRET=dev-secret-key
JWT_EXPIRES_IN=7d
EMAIL_HOST=smtp.mailtrap.io
EMAIL_PORT=2525
REACT_APP_API_URL=http://localhost:5000/api

# .env.staging
NODE_ENV=staging
MONGO_URI=mongodb+srv://user:pass@staging-cluster.mongodb.net/quantum_logics_staging
JWT_SECRET=staging-secret-key
JWT_EXPIRES_IN=24h
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
REACT_APP_API_URL=https://staging-api.quantumlogics.io/api

# .env.production
NODE_ENV=production
MONGO_URI=mongodb+srv://user:pass@prod-cluster.mongodb.net/quantum_logics
JWT_SECRET=super-secure-production-secret
JWT_EXPIRES_IN=24h
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
REACT_APP_API_URL=https://api.quantumlogics.io/api
```

## Development Deployment

### Local Development Setup

#### 1. Clone and Setup
```bash
# Clone the repository
git clone --recurse-submodules https://github.com/SENODROOM/Quantum-Logics.git
cd Quantum-Logics

# Install dependencies
cd backend && npm install
cd ../frontend && npm install
```

#### 2. Database Setup
```bash
# Option 1: Local MongoDB
brew services start mongodb-community
# or
sudo systemctl start mongod

# Option 2: MongoDB Atlas (recommended)
# Create a free cluster at https://www.mongodb.com/atlas
# Get connection string and add to .env
```

#### 3. Environment Configuration
```bash
# Backend environment
cd backend
cp .env.example .env.development
# Edit .env.development with your settings

# Frontend environment
cd ../frontend
cp .env.example .env.development
# Edit .env.development with your settings
```

#### 4. Start Development Servers
```bash
# Terminal 1: Backend
cd backend
npm run dev

# Terminal 2: Frontend
cd frontend
npm start
```

### Docker Development Setup

#### Dockerfile (Backend)
```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

EXPOSE 5000

CMD ["npm", "start"]
```

#### Dockerfile (Frontend)
```dockerfile
FROM node:18-alpine as build

WORKDIR /app
COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

#### docker-compose.yml
```yaml
version: '3.8'

services:
  backend:
    build: ./backend
    ports:
      - "5000:5000"
    environment:
      - NODE_ENV=development
      - MONGO_URI=mongodb://mongo:27017/quantum_logics
    depends_on:
      - mongo
    volumes:
      - ./backend:/app
      - /app/node_modules

  frontend:
    build: ./frontend
    ports:
      - "3000:80"
    depends_on:
      - backend

  mongo:
    image: mongo:5.0
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db

volumes:
  mongo_data:
```

#### Running with Docker
```bash
# Build and start all services
docker-compose up --build

# Run in background
docker-compose up -d

# Stop services
docker-compose down

# View logs
docker-compose logs -f
```

## Staging Deployment

### Vercel Staging Setup

#### 1. Connect Repository
```bash
# Install Vercel CLI
npm i -g vercel

# Login to Vercel
vercel login

# Link project
vercel link
```

#### 2. Configure Vercel
```json
// vercel.json
{
  "version": 2,
  "builds": [
    {
      "src": "backend/server.js",
      "use": "@vercel/node"
    },
    {
      "src": "frontend/package.json",
      "use": "@vercel/static-build",
      "config": {
        "distDir": "build"
      }
    }
  ],
  "routes": [
    {
      "src": "/api/(.*)",
      "dest": "/backend/server.js"
    },
    {
      "src": "/(.*)",
      "dest": "/frontend/$1"
    }
  ],
  "env": {
    "NODE_ENV": "staging"
  }
}
```

#### 3. Environment Variables
```bash
# Set environment variables via Vercel CLI
vercel env add MONGO_URI staging
vercel env add JWT_SECRET staging
vercel env add EMAIL_HOST staging
vercel env add EMAIL_USER staging
vercel env add EMAIL_PASS staging
```

#### 4. Deploy to Staging
```bash
# Deploy to staging
vercel --env=staging

# Or use GitHub integration for automatic deployments
```

### Netlify Staging Setup

#### 1. Configure Netlify
```toml
# netlify.toml
[build]
  base = "frontend/"
  command = "npm run build"
  publish = "build"

[build.environment]
  REACT_APP_API_URL = "https://staging-api.quantumlogics.io/api"

[[redirects]]
  from = "/api/*"
  to = "https://staging-api.quantumlogics.io/api/:splat"
  status = 200

[context.staging]
  command = "npm run build"
  [context.staging.environment]
    REACT_APP_API_URL = "https://staging-api.quantumlogics.io/api"
```

#### 2. Deploy to Staging
```bash
# Install Netlify CLI
npm install -g netlify-cli

# Login and link
netlify login
netlify link

# Deploy to staging
netlify deploy --context=staging
```

## Production Deployment

### Vercel Production Deployment

#### 1. Domain Configuration
```bash
# Add custom domain
vercel domains add quantumlogics.io
vercel domains add api.quantumlogics.io

# Configure DNS (provided by Vercel)
# Add these records to your domain registrar:
# A record: @ -> 76.76.21.21
# CNAME record: api -> cname.vercel-dns.com
```

#### 2. Production Environment
```bash
# Set production environment variables
vercel env add MONGO_URI production
vercel env add JWT_SECRET production
vercel env add EMAIL_HOST production
vercel env add EMAIL_USER production
vercel env add EMAIL_PASS production
```

#### 3. Deploy to Production
```bash
# Deploy from main branch
vercel --prod

# Or configure automatic deployment from GitHub
```

### AWS Production Deployment

#### 1. Backend on AWS Lambda

```javascript
// lambda-handler.js
const serverless = require('serverless-http');
const app = require('./server');

module.exports.handler = serverless(app);
```

```json
// serverless.yml
service: quantum-logics-api

provider:
  name: aws
  runtime: nodejs18.x
  region: us-east-1
  environment:
    NODE_ENV: production
    MONGO_URI: ${ssm:/quantum-logics/mongo-uri}
    JWT_SECRET: ${ssm:/quantum-logics/jwt-secret}

functions:
  api:
    handler: lambda-handler.handler
    events:
      - http:
          path: /{proxy+}
          method: ANY
          cors: true
```

#### 2. Frontend on S3 + CloudFront

```bash
# Build and deploy to S3
cd frontend
npm run build

# Sync to S3
aws s3 sync build/ s3://quantum-logics-frontend --delete

# Invalidate CloudFront cache
aws cloudfront create-invalidation --distribution-id YOUR_DISTRIBUTION_ID --paths "/*"
```

#### 3. Database on MongoDB Atlas

```bash
# Create production cluster
# Configure network access
# Create database user
# Get connection string
# Add to environment variables
```

### Docker Production Deployment

#### 1. Production Dockerfile
```dockerfile
# Multi-stage build for production
FROM node:18-alpine as builder

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production && npm cache clean --force

FROM node:18-alpine

RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001

WORKDIR /app

COPY --from=builder /app/node_modules ./node_modules
COPY --chown=nextjs:nodejs . .

USER nextjs

EXPOSE 5000

ENV NODE_ENV=production

CMD ["node", "server.js"]
```

#### 2. Docker Compose Production
```yaml
version: '3.8'

services:
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile.prod
    ports:
      - "5000:5000"
    environment:
      - NODE_ENV=production
      - MONGO_URI=${MONGO_URI}
      - JWT_SECRET=${JWT_SECRET}
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:5000/api/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile.prod
    ports:
      - "80:80"
    depends_on:
      - backend
    restart: unless-stopped

  nginx:
    image: nginx:alpine
    ports:
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./ssl:/etc/nginx/ssl
    depends_on:
      - frontend
      - backend
    restart: unless-stopped
```

## Platform-Specific Guides

### Heroku Deployment

#### 1. Backend Deployment
```bash
# Create Heroku app
heroku create quantum-logics-api

# Set environment variables
heroku config:set NODE_ENV=production
heroku config:set MONGO_URI=mongodb+srv://...
heroku config:set JWT_SECRET=your-secret

# Deploy
git subtree push --prefix backend heroku main
```

#### 2. Frontend Deployment
```bash
# Create Heroku app
heroku create quantum-logics-frontend

# Configure build
echo "web: npm start && serve -s build" > Procfile
heroku config:set REACT_APP_API_URL=https://quantum-logics-api.herokuapp.com/api

# Deploy
git subtree push --prefix frontend heroku main
```

### DigitalOcean App Platform

#### 1. App Configuration
```yaml
# .do/app.yaml
name: quantum-logics
services:
- name: backend
  source_dir: backend
  github:
    repo: SENODROOM/Quantum-Logics
    branch: main
  run_command: npm start
  environment_slug: node-js
  instance_count: 1
  instance_size_slug: basic-xxs
  envs:
  - key: NODE_ENV
    value: production
  - key: MONGO_URI
    value: ${DATABASE_URL}

- name: frontend
  source_dir: frontend
  github:
    repo: SENODROOM/Quantum-Logics
    branch: main
  build_command: npm run build
  run_command: serve -s build
  environment_slug: node-js
  instance_count: 1
  instance_size_slug: basic-xxs
  envs:
  - key: REACT_APP_API_URL
    value: ${_self.URL}/api
```

## Database Setup

### MongoDB Atlas Configuration

#### 1. Create Cluster
```bash
# 1. Sign up at https://www.mongodb.com/atlas
# 2. Create new cluster (M0 free tier for development)
# 3. Choose cloud provider and region
# 4. Configure cluster settings
```

#### 2. Network Access
```bash
# Add IP addresses
# 0.0.0.0/0 (all access) - for development
# Specific IP addresses - for production
```

#### 3. Database User
```bash
# Create database user
# Username: quantum-logics-user
# Password: Generate strong password
# Permissions: Read and write to all databases
```

#### 4. Connection String
```bash
# Get connection string
mongodb+srv://quantum-logics-user:<password>@cluster.mongodb.net/quantum_logics?retryWrites=true&w=majority
```

### Database Seeding

#### Production Seed Script
```javascript
// scripts/seed-production.js
const mongoose = require('mongoose');
const bcrypt = require('bcrypt');
const User = require('../models/User');
const Job = require('../models/Job');

const seedProduction = async () => {
  try {
    // Connect to production database
    await mongoose.connect(process.env.MONGO_URI);
    
    // Create admin user
    const adminPassword = await bcrypt.hash(process.env.ADMIN_PASSWORD, 12);
    const admin = new User({
      name: 'Admin User',
      email: process.env.ADMIN_EMAIL,
      password: adminPassword,
      role: 'admin'
    });
    await admin.save();
    
    // Create initial jobs
    const jobs = [
      {
        title: 'Senior Full Stack Developer',
        department: 'Engineering',
        location: 'Remote',
        type: 'Full-time',
        salary: '$120,000 - $180,000',
        description: 'We are looking for a senior full stack developer...',
        requirements: ['5+ years experience', 'React/Node.js expertise'],
        active: true
      }
    ];
    
    await Job.insertMany(jobs);
    
    console.log('Production database seeded successfully');
    process.exit(0);
  } catch (error) {
    console.error('Seeding error:', error);
    process.exit(1);
  }
};

seedProduction();
```

## Environment Variables

### Security Best Practices

#### 1. Use Secret Management Services
```bash
# AWS Systems Manager Parameter Store
aws ssm put-parameter \
  --name "/quantum-logics/jwt-secret" \
  --value "your-secret-key" \
  --type "SecureString" \
  --description "JWT secret for Quantum Logics"

# Vercel Environment Variables
vercel env add JWT_SECRET production
```

#### 2. Environment Variable Validation
```javascript
// config/env.js
const Joi = require('joi');

const envSchema = Joi.object({
  NODE_ENV: Joi.string().valid('development', 'staging', 'production').required(),
  PORT: Joi.number().default(5000),
  MONGO_URI: Joi.string().required(),
  JWT_SECRET: Joi.string().min(32).required(),
  JWT_EXPIRES_IN: Joi.string().default('24h'),
  EMAIL_HOST: Joi.string().required(),
  EMAIL_PORT: Joi.number().required(),
  EMAIL_USER: Joi.string().email().required(),
  EMAIL_PASS: Joi.string().required()
}).unknown();

const { error, value: envVars } = envSchema.validate(process.env);

if (error) {
  throw new Error(`Config validation error: ${error.message}`);
}

module.exports = envVars;
```

## SSL/HTTPS Setup

### Automatic SSL with Hosting Platforms

#### Vercel
```bash
# SSL is automatically provided
# Custom domain SSL is configured automatically
# Certificates are managed by Vercel
```

#### Netlify
```bash
# SSL is automatically provided
# Let's Encrypt certificates are managed automatically
# Force HTTPS in netlify.toml
```

```toml
# netlify.toml
[[redirects]]
  from = "http://*"
  to = "https://:splat"
  status = 301
  force = true
```

### Custom SSL Configuration

#### Nginx Configuration
```nginx
# nginx.conf
server {
    listen 80;
    server_name quantumlogics.io www.quantumlogics.io;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name quantumlogics.io www.quantumlogics.io;

    ssl_certificate /etc/nginx/ssl/quantumlogics.io.crt;
    ssl_certificate_key /etc/nginx/ssl/quantumlogics.io.key;
    
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512;
    ssl_prefer_server_ciphers off;
    ssl_session_cache shared:SSL:10m;

    location / {
        proxy_pass http://frontend:80;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    location /api {
        proxy_pass http://backend:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

## Monitoring and Logging

### Application Monitoring

#### Vercel Analytics
```bash
# Enable in Vercel dashboard
# Track performance metrics
# Monitor error rates
# View user analytics
```

#### Custom Monitoring
```javascript
// middleware/monitoring.js
const winston = require('winston');

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.json()
  ),
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});

if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({
    format: winston.format.simple()
  }));
}

const requestLogger = (req, res, next) => {
  const start = Date.now();
  
  res.on('finish', () => {
    const duration = Date.now() - start;
    logger.info({
      method: req.method,
      url: req.url,
      status: res.statusCode,
      duration,
      userAgent: req.get('User-Agent'),
      ip: req.ip
    });
  });
  
  next();
};

module.exports = { logger, requestLogger };
```

### Error Tracking

#### Sentry Integration
```javascript
// services/sentry.js
const Sentry = require('@sentry/node');

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: process.env.NODE_ENV,
  tracesSampleRate: 1.0,
});

const errorHandler = (err, req, res, next) => {
  Sentry.captureException(err);
  
  res.status(500).json({
    error: 'Internal server error',
    requestId: req.id
  });
};

module.exports = { errorHandler };
```

## Backup and Recovery

### Database Backups

#### MongoDB Atlas Backups
```bash
# Enable automatic backups in Atlas dashboard
# Configure backup schedule (daily)
# Set retention period (30 days)
# Enable point-in-time recovery
```

#### Manual Backup Script
```javascript
// scripts/backup.js
const { exec } = require('child_process');
const fs = require('fs');
const path = require('path');

const backupDatabase = async () => {
  const timestamp = new Date().toISOString().replace(/[:.]/g, '-');
  const backupDir = path.join(__dirname, '../backups');
  const backupFile = path.join(backupDir, `backup-${timestamp}.gz`);
  
  // Create backup directory if it doesn't exist
  if (!fs.existsSync(backupDir)) {
    fs.mkdirSync(backupDir, { recursive: true });
  }
  
  const command = `mongodump --uri="${process.env.MONGO_URI}" --gzip --archive=${backupFile}`;
  
  exec(command, (error, stdout, stderr) => {
    if (error) {
      console.error('Backup failed:', error);
      return;
    }
    
    console.log('Backup completed:', backupFile);
    
    // Clean up old backups (keep last 7)
    const files = fs.readdirSync(backupDir);
    const backups = files.filter(file => file.startsWith('backup-'));
    backups.sort().slice(0, -7).forEach(file => {
      fs.unlinkSync(path.join(backupDir, file));
    });
  });
};

// Run backup daily
if (require.main === module) {
  backupDatabase();
}

module.exports = backupDatabase;
```

### Recovery Procedures

#### Database Restoration
```bash
# Restore from backup
mongorestore --uri="mongodb://localhost:27017/quantum_logics" --gzip --archive=backup-2024-01-15T10-30-00-000Z.gz

# Point-in-time recovery (Atlas)
# 1. Go to Atlas dashboard
# 2. Navigate to backups
# 3. Select point-in-time restore
# 4. Choose restore time
# 5. Create new cluster from backup
```

## Troubleshooting

### Common Deployment Issues

#### 1. Database Connection Issues
```bash
# Check connection string
# Verify network access
# Check firewall rules
# Validate credentials

# Test connection
node -e "
const mongoose = require('mongoose');
mongoose.connect(process.env.MONGO_URI)
  .then(() => console.log('Connected successfully'))
  .catch(err => console.error('Connection failed:', err));
"
```

#### 2. Environment Variable Issues
```bash
# Verify all required variables are set
# Check for typos in variable names
# Ensure proper escaping of special characters

# List environment variables
vercel env ls
heroku config
printenv
```

#### 3. Build Failures
```bash
# Check Node.js version compatibility
# Verify all dependencies are installed
# Check for syntax errors
# Review build logs for specific errors

# Local build test
npm run build
```

#### 4. CORS Issues
```bash
# Verify frontend URL is in CORS whitelist
# Check API endpoint configuration
# Ensure proper headers are set

# Test CORS
curl -H "Origin: https://yourdomain.com" \
     -H "Access-Control-Request-Method: POST" \
     -H "Access-Control-Request-Headers: X-Requested-With" \
     -X OPTIONS https://api.yourdomain.com/api/test
```

### Performance Issues

#### 1. Slow API Responses
```bash
# Check database query performance
# Monitor response times
# Review database indexes
# Implement caching

# Enable MongoDB slow query logging
db.setProfilingLevel(2, { slowms: 100 })
```

#### 2. Memory Leaks
```bash
# Monitor memory usage
# Check for unclosed connections
# Review event listeners
# Implement proper cleanup

# Memory profiling
node --inspect app.js
```

### Security Issues

#### 1. Unauthorized Access
```bash
# Review authentication logic
# Check JWT implementation
# Verify role-based access control
# Audit API endpoints

# Test authentication
curl -H "Authorization: Bearer <token>" https://api.yourdomain.com/api/protected
```

#### 2. Data Exposure
```bash
# Review error messages
# Check for sensitive data in logs
# Validate input sanitization
# Implement proper error handling
```

---

This deployment guide provides comprehensive instructions for deploying Quantum Logics across various environments and platforms. For specific platform issues or additional help, refer to the platform's documentation or create an issue in the repository.
