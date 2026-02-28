# Contributing to Quantum Logics

Thank you for your interest in contributing to Quantum Logics! This document provides guidelines and information for contributors.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Workflow](#development-workflow)
- [Coding Standards](#coding-standards)
- [Testing](#testing)
- [Documentation](#documentation)
- [Submitting Changes](#submitting-changes)
- [Bug Reports](#bug-reports)
- [Feature Requests](#feature-requests)

## Code of Conduct

Please read and follow our [Code of Conduct](CODE_OF_CONDUCT.md) to ensure a welcoming and inclusive environment for all contributors.

## Getting Started

### Prerequisites

- Git (with submodule support)
- Node.js 18+
- npm or yarn
- MongoDB (for backend development)

### Initial Setup

1. **Fork the repository**
   ```bash
   # Fork on GitHub, then clone your fork
   git clone https://github.com/YOUR_USERNAME/Quantum-Logics.git
   cd Quantum-Logics
   ```

2. **Set up upstream remote**
   ```bash
   git remote add upstream https://github.com/SENODROOM/Quantum-Logics.git
   ```

3. **Initialize submodules**
   ```bash
   git submodule update --init --recursive
   ```

4. **Install dependencies**
   ```bash
   # Backend dependencies
   cd backend
   npm install
   
   # Frontend dependencies
   cd ../frontend
   npm install
   ```

5. **Set up environment variables**
   ```bash
   # Backend
   cp backend/.env.example backend/.env
   # Edit backend/.env with your MongoDB URI
   
   # Frontend
   cp frontend/.env.example frontend/.env
   # Edit frontend/.env with your API URL
   ```

## Development Workflow

### Branch Strategy

We use a simplified Git flow:

- `main` - Production-ready code
- `develop` - Integration branch for features
- `feature/*` - New features
- `bugfix/*` - Bug fixes
- `hotfix/*` - Critical production fixes

### Creating a Feature Branch

1. **Keep your fork up to date**
   ```bash
   git fetch upstream
   git checkout develop
   git merge upstream/develop
   ```

2. **Create a new branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

3. **Make your changes**
   - Follow the coding standards
   - Write tests for new functionality
   - Update documentation

4. **Commit your changes**
   ```bash
   git add .
   git commit -m "feat: add your feature description"
   ```

### Commit Message Format

We follow [Conventional Commits](https://www.conventionalcommits.org/) specification:

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, etc.)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks

**Examples:**
```
feat(auth): add JWT token refresh mechanism

Implement automatic token refresh to improve user experience
and reduce login frequency.

Closes #123
```

```
fix(jobs): resolve application duplicate issue

Prevent users from submitting multiple applications for the same job.
```

## Coding Standards

### General Guidelines

- **Consistency**: Follow existing code patterns and style
- **Clarity**: Write self-documenting code with meaningful variable names
- **Simplicity**: Keep functions small and focused
- **Performance**: Consider performance implications of changes

### Backend (Node.js/Express)

- **ESLint**: Follow the configured ESLint rules
- **Async/Await**: Prefer async/await over callbacks
- **Error Handling**: Use consistent error handling patterns
- **Validation**: Use `express-validator` for input validation
- **Security**: Always validate and sanitize user input

```javascript
// Good example
const createJob = async (req, res) => {
  try {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }

    const job = await Job.create(req.body);
    res.status(201).json(job);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};
```

### Frontend (React)

- **Components**: Use functional components with hooks
- **State Management**: Prefer local state, lift when necessary
- **Props**: Use TypeScript-style prop comments for clarity
- **CSS**: Follow the existing CSS variable system
- **Accessibility**: Include proper ARIA labels and semantic HTML

```jsx
// Good example
const JobCard = ({ job, onApply }) => {
  const [isExpanded, setIsExpanded] = useState(false);

  return (
    <article className="job-card" role="article">
      <h3>{job.title}</h3>
      <p>{job.department}</p>
      <button 
        onClick={() => onApply(job.id)}
        aria-label={`Apply for ${job.title} position`}
      >
        Apply Now
      </button>
    </article>
  );
};
```

## Testing

### Backend Testing

- **Unit Tests**: Test individual functions and methods
- **Integration Tests**: Test API endpoints
- **Test Database**: Use a separate test database

```bash
# Run backend tests
cd backend
npm test
```

### Frontend Testing

- **Component Tests**: Test React components
- **User Interaction Tests**: Test user flows
- **Accessibility Tests**: Ensure WCAG compliance

```bash
# Run frontend tests
cd frontend
npm test
```

### Test Coverage

- Aim for at least 80% code coverage
- Write tests for new features
- Update tests when fixing bugs

## Documentation

### Code Documentation

- **JSDoc**: Document functions and classes
- **Comments**: Explain complex logic
- **README**: Update project documentation

### API Documentation

- **Endpoints**: Document all API endpoints
- **Parameters**: Document request/response formats
- **Examples**: Provide usage examples

## Submitting Changes

### Pull Request Process

1. **Create a Pull Request**
   - Target the `develop` branch for features
   - Target the `main` branch for hotfixes
   - Use a descriptive title

2. **Fill out the PR template**
   - Description of changes
   - Testing performed
   - Related issues
   - Screenshots (if applicable)

3. **Code Review**
   - Address reviewer feedback
   - Keep discussions constructive
   - Update PR as needed

4. **Merge**
   - Maintain a clean commit history
   - Use squash and merge when appropriate

### PR Checklist

Before submitting a PR, ensure:

- [ ] Code follows project standards
- [ ] Tests pass locally
- [ ] Documentation is updated
- [ ] Commit messages are conventional
- [ ] No sensitive data is committed
- [ ] Submodules are properly updated (if applicable)

## Bug Reports

### Reporting a Bug

1. **Search existing issues** to avoid duplicates
2. **Use the bug report template**
3. **Provide detailed information**:
   - Steps to reproduce
   - Expected vs actual behavior
   - Environment details
   - Screenshots/error messages

### Bug Report Template

```markdown
## Bug Description
Brief description of the bug

## Steps to Reproduce
1. Go to...
2. Click on...
3. See error

## Expected Behavior
What should happen

## Actual Behavior
What actually happens

## Environment
- OS: [e.g., Windows 10, macOS 12.0]
- Browser: [e.g., Chrome 96, Firefox 95]
- Node.js: [e.g., 18.0.0]
- MongoDB: [e.g., 5.0.0]

## Additional Context
Any other relevant information
```

## Feature Requests

### Requesting a Feature

1. **Search existing issues** to avoid duplicates
2. **Use the feature request template**
3. **Provide detailed information**:
   - Problem statement
   - Proposed solution
   - Use cases
   - Implementation ideas

### Feature Request Template

```markdown
## Feature Description
Brief description of the feature

## Problem Statement
What problem does this solve?

## Proposed Solution
How should this work?

## Use Cases
Who would use this and how?

## Implementation Ideas
Any technical suggestions

## Additional Context
Any other relevant information
```

## Getting Help

- **Issues**: Open an issue for bugs or questions
- **Discussions**: Use GitHub Discussions for general questions
- **Documentation**: Check existing documentation first
- **Code Review**: Request reviews from maintainers

## Recognition

Contributors are recognized in:
- README.md contributors section
- Release notes
- Annual contributor highlights

Thank you for contributing to Quantum Logics! 🚀
