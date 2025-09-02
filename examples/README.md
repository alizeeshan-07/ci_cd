# Practical CI/CD Examples 💼

This directory contains real-world CI/CD examples for different types of projects. Each example includes complete workflows, configuration files, and explanations.

## Examples Available

### 1. [Simple Node.js API](./nodejs-api/)
- REST API with Express.js
- Unit and integration tests
- Deployment to Heroku
- Database migrations

### 2. [React Web Application](./react-webapp/)
- React frontend with testing
- Build optimization
- Deployment to Netlify/Vercel
- Preview deployments for PRs

### 3. [Python Django Project](./python-django/)
- Django web application
- PostgreSQL database
- Docker containerization
- AWS deployment

### 4. [Docker Multi-Service App](./docker-microservices/)
- Multiple services with Docker Compose
- Database migrations
- Integration testing
- Kubernetes deployment

### 5. [Mobile App CI/CD](./mobile-app/)
- React Native application
- iOS and Android builds
- App store deployment
- Beta testing distribution

## Getting Started

1. Choose an example that matches your project type
2. Copy the workflow files to your repository
3. Modify the configuration for your specific needs
4. Test the pipeline with a simple change

## Common Patterns

All examples demonstrate these CI/CD best practices:

- **Automated Testing**: Unit, integration, and end-to-end tests
- **Code Quality**: Linting, formatting, and security scans
- **Environment Promotion**: Dev → Staging → Production
- **Rollback Strategies**: Quick recovery from failed deployments
- **Monitoring**: Health checks and alerts
- **Security**: Secret management and vulnerability scanning

## Customization Tips

- Adjust the trigger conditions (`on:` section) for your workflow
- Modify environment variables for your specific setup
- Add or remove testing steps based on your tech stack
- Configure deployment targets for your hosting platform

Let's dive into the examples! 🚀
