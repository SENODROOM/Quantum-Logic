# Frequently Asked Questions (FAQ)

This document answers common questions about Quantum Logics, covering setup, usage, development, and troubleshooting.

## Table of Contents

- [General Questions](#general-questions)
- [Setup and Installation](#setup-and-installation)
- [Usage and Features](#usage-and-features)
- [Development](#development)
- [Troubleshooting](#troubleshooting)
- [Security](#security)
- [Contributing](#contributing)
- [Deployment](#deployment)

## General Questions

### What is Quantum Logics?

Quantum Logics is a comprehensive job board application built with modern web technologies. It allows companies to post job listings and manage applications, while job seekers can browse opportunities and submit applications. The project showcases a full-stack MERN-style architecture with a focus on clean design and user experience.

### What technologies does Quantum Logics use?

**Backend:**
- Node.js with Express.js
- MongoDB with Mongoose ODM
- JWT authentication
- bcrypt for password hashing
- Nodemailer for email services

**Frontend:**
- React 19 with hooks
- React Router for navigation
- Axios for API communication
- Custom CSS with CSS variables
- No external UI framework (custom design)

**Development Tools:**
- Git with submodules
- Nodemon for development
- ESLint for code quality
- Vercel for deployment

### Is Quantum Logics open source?

Yes! Quantum Logics is licensed under the MIT License. You can use, modify, and distribute the code freely. See the [LICENSE](../LICENSE) file for details.

### Can I use Quantum Logics for my company?

Absolutely! Quantum Logics is designed to be easily customizable for different organizations. You can modify the branding, add custom fields, and integrate with your existing systems.

## Setup and Installation

### How do I get started with Quantum Logics?

1. **Clone the repository:**
   ```bash
   git clone --recurse-submodules https://github.com/SENODROOM/Quantum-Logics.git
   cd Quantum-Logics
   ```

2. **Install dependencies:**
   ```bash
   # Backend
   cd backend
   npm install
   
   # Frontend
   cd ../frontend
   npm install
   ```

3. **Set up environment variables:**
   ```bash
   # Backend
   cp backend/.env.example backend/.env
   # Edit backend/.env with your MongoDB URI
   
   # Frontend
   cp frontend/.env.example frontend/.env
   # Edit frontend/.env with your API URL
   ```

4. **Start the development servers:**
   ```bash
   # Terminal 1: Backend
   cd backend
   npm run dev
   
   # Terminal 2: Frontend
   cd frontend
   npm start
   ```

### What are the system requirements?

- **Node.js**: Version 18 or higher
- **npm**: Version 8 or higher (or yarn)
- **MongoDB**: Version 5.0 or higher (local or Atlas)
- **Git**: For version control and submodules
- **Modern browser**: Chrome, Firefox, Safari, or Edge

### How do I set up MongoDB?

**Option 1: MongoDB Atlas (Recommended for production)**
1. Create a free account at [MongoDB Atlas](https://www.mongodb.com/atlas)
2. Create a new cluster
3. Get your connection string
4. Add it to your `.env` file

**Option 2: Local MongoDB**
1. Install MongoDB Community Server
2. Start the MongoDB service
3. Create a database for the application
4. Update your `.env` file with the local connection string

### What environment variables do I need?

**Backend (.env):**
```bash
MONGO_URI=mongodb://localhost:27017/quantum_logics
JWT_SECRET=your-super-secret-jwt-key
JWT_EXPIRES_IN=24h
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USER=noreply@quantumlogics.io
EMAIL_PASS=your-app-password
```

**Frontend (.env):**
```bash
REACT_APP_API_URL=http://localhost:5000/api
REACT_APP_APP_NAME=Quantum Logics
```

### How do I seed the database?

```bash
cd backend
npm run seed
```

This will create an admin user and some sample job postings.

**Default Admin Credentials:**
- Email: `admin@quantumlogics.io`
- Password: `admin123`

## Usage and Features

### How do I create an account?

1. Click the "Sign Up" button in the navigation
2. Fill in your name, email, and password
3. Click "Create Account"
4. You'll be automatically logged in

### How do I apply for a job?

1. Browse available jobs on the Careers page
2. Click on a job to view details
3. Click "Apply Now"
4. Fill in the application form with your details
5. Submit your application

### What information do I need to provide for an application?

- **Contact Information**: Phone number
- **Experience**: Years of experience
- **LinkedIn Profile**: LinkedIn profile URL (optional)
- **Portfolio**: Portfolio or GitHub URL (optional)
- **Cover Letter**: Why you're interested in the position

### How do I check my application status?

1. Log in to your account
2. Go to your User Dashboard
3. Click on "My Applications" tab
4. View the status of all your applications

### What are the application statuses?

- **Pending**: Application received, not yet reviewed
- **Reviewed**: Application has been reviewed
- **Accepted**: Application approved for next steps
- **Rejected**: Application not selected

### How do I access the admin panel?

1. Log in with admin credentials
2. Click on "Admin Dashboard" in the navigation
3. You'll see the admin interface with management options

### What can I do as an admin?

- **Overview**: View statistics and metrics
- **Manage Jobs**: Create, edit, delete, and activate/deactivate jobs
- **Applications**: Review and update application statuses
- **Users**: View all registered users

## Development

### How is the project structured?

```
Quantum-Logics/
├── backend/              # Express.js API (submodule)
├── frontend/             # React application (submodule)
├── docs/                 # Documentation
├── .gitmodules           # Git submodules configuration
├── .gitignore            # Git ignore rules
├── LICENSE               # MIT License
└── README.md             # Project overview
```

### How do I work with submodules?

**Update submodules:**
```bash
git submodule update --init --recursive
```

**Pull latest changes:**
```bash
git submodule update --recursive --remote
```

**Commit submodule changes:**
```bash
cd backend
git add .
git commit -m "Backend changes"
git push

cd ../frontend
git add .
git commit -m "Frontend changes"
git push

# Update parent repository
cd ..
git add .
git commit -m "Update submodules"
git push
```

### How do I add a new API endpoint?

1. **Define the route** in `backend/routes/`:
```javascript
// routes/example.js
const express = require('express');
const router = express.Router();

router.get('/', async (req, res) => {
  try {
    // Your logic here
    res.json({ data: 'example' });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

module.exports = router;
```

2. **Register the route** in `backend/server.js`:
```javascript
const exampleRoutes = require('./routes/example');
app.use('/api/example', exampleRoutes);
```

3. **Add authentication** if needed:
```javascript
const { authenticateToken } = require('./middleware/auth');
router.get('/', authenticateToken, async (req, res) => {
  // Protected route logic
});
```

### How do I add a new React component?

1. **Create the component** in `frontend/src/components/`:
```jsx
// components/NewComponent.jsx
import React from 'react';

const NewComponent = ({ prop1, prop2 }) => {
  return (
    <div className="new-component">
      <h2>{prop1}</h2>
      <p>{prop2}</p>
    </div>
  );
};

export default NewComponent;
```

2. **Add styles** in the component file or separate CSS:
```css
.new-component {
  background: var(--card);
  padding: 1rem;
  border-radius: 8px;
}
```

3. **Import and use** in your page:
```jsx
import NewComponent from '../components/NewComponent';

const Page = () => {
  return (
    <div>
      <NewComponent prop1="Title" prop2="Description" />
    </div>
  );
};
```

### How do I test the application?

**Backend Testing:**
```bash
cd backend
npm test
```

**Frontend Testing:**
```bash
cd frontend
npm test
```

**Manual Testing:**
1. Start both servers
2. Use the application as different user types
3. Test all major features
4. Check responsive design on different devices

## Troubleshooting

### Why won't the backend start?

**Common issues:**
1. **MongoDB connection failed**: Check your MONGO_URI in `.env`
2. **Port already in use**: Change the PORT in `.env` or kill the process
3. **Missing dependencies**: Run `npm install` in the backend directory

**Debug steps:**
```bash
cd backend
npm install
npm run dev
# Check the console for error messages
```

### Why can't I connect to the database?

1. **Check MongoDB URI**: Ensure it's correct and accessible
2. **Network issues**: Check if MongoDB is running and accessible
3. **Authentication**: Verify username and password if using auth
4. **Firewall**: Ensure port 27017 is open (for local MongoDB)

### Why is the frontend not loading the API?

**Common issues:**
1. **CORS errors**: Check backend CORS configuration
2. **Wrong API URL**: Verify REACT_APP_API_URL in frontend `.env`
3. **Backend not running**: Ensure backend server is running
4. **Network issues**: Check if you can access the API directly

**Debug steps:**
```bash
# Test API directly
curl http://localhost:5000/api/jobs

# Check browser console for errors
# Check Network tab in developer tools
```

### Why am I getting authentication errors?

**Common issues:**
1. **Expired token**: Log out and log back in
2. **Invalid JWT_SECRET**: Check backend `.env` file
3. **Token not sent**: Check if Authorization header is included
4. **CORS issues**: Verify frontend is allowed to make requests

### Why are emails not sending?

**Common issues:**
1. **Wrong email configuration**: Check EMAIL_HOST, EMAIL_USER, EMAIL_PASS
2. **App password needed**: Use app-specific password for Gmail
3. **Firewall blocking**: Port 587 might be blocked
4. **Email provider limits**: Check sending limits

### How do I reset the database?

**Warning: This will delete all data!**

```bash
cd backend
# Connect to MongoDB
mongo

# Drop the database
use quantum_logics
db.dropDatabase()

# Exit and reseed
exit
npm run seed
```

## Security

### Is Quantum Logics secure?

Quantum Logics implements several security measures:
- Password hashing with bcrypt
- JWT-based authentication
- Input validation and sanitization
- CORS configuration
- Rate limiting on authentication routes
- Environment variable protection

### How can I improve security?

1. **Use HTTPS** in production
2. **Implement rate limiting** on all API routes
3. **Add input validation** on all forms
4. **Use environment variables** for sensitive data
5. **Regularly update dependencies**
6. **Implement logging and monitoring**
7. **Add email verification** for registration

### Should I use the default admin credentials?

No! Change the default admin credentials immediately after setup:
1. Log in as admin
2. Create a new admin account
3. Delete the default admin account
4. Or update the password in the database

## Contributing

### How can I contribute to Quantum Logics?

1. **Fork the repository**
2. **Create a feature branch**
3. **Make your changes**
4. **Add tests** if applicable
5. **Update documentation**
6. **Submit a pull request**

See the [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines.

### What are the coding standards?

- Follow existing code style and patterns
- Use conventional commit messages
- Write clear, descriptive variable names
- Add comments for complex logic
- Keep functions small and focused
- Test your changes thoroughly

### How do I report a bug?

1. **Search existing issues** to avoid duplicates
2. **Create a new issue** with detailed information
3. **Include steps to reproduce**
4. **Add screenshots** if applicable
5. **Provide environment details**

## Deployment

### How do I deploy Quantum Logics?

**Backend (Vercel):**
1. Connect your GitHub repository to Vercel
2. Set environment variables in Vercel dashboard
3. Deploy automatically on push to main branch

**Frontend (Netlify/Vercel):**
1. Build the application: `npm run build`
2. Deploy the build folder to your hosting provider
3. Set environment variables for production

### What environment variables do I need in production?

**Backend:**
- MONGO_URI (MongoDB Atlas connection string)
- JWT_SECRET (secure random string)
- EMAIL_HOST, EMAIL_USER, EMAIL_PASS (for notifications)
- NODE_ENV=production

**Frontend:**
- REACT_APP_API_URL (production API URL)

### How do I set up a custom domain?

1. **Purchase a domain** from a registrar
2. **Configure DNS** to point to your hosting provider
3. **Set up SSL** certificate (usually automatic)
4. **Update environment variables** if needed

### How do I monitor the application in production?

1. **Use Vercel Analytics** for frontend monitoring
2. **Set up MongoDB Atlas monitoring** for database
3. **Implement error logging** (Sentry, LogRocket)
4. **Set up uptime monitoring** (UptimeRobot)
5. **Monitor API performance** and response times

---

## Still Have Questions?

If you couldn't find the answer to your question here:

1. **Check the documentation**: Browse other docs in the `/docs` folder
2. **Search GitHub issues**: See if someone asked before
3. **Create a new issue**: Ask your question on GitHub
4. **Contact us**: Reach out to the maintainers

We're here to help and want to make Quantum Logics as accessible as possible for everyone!
