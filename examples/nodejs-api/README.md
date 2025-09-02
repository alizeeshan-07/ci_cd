# Node.js API CI/CD Example 🚀

This example demonstrates a complete CI/CD pipeline for a Node.js REST API using Express.js.

## Project Structure

```
nodejs-api/
├── .github/
│   └── workflows/
│       ├── ci.yml              # Continuous Integration
│       └── deploy.yml          # Continuous Deployment
├── src/
│   ├── controllers/
│   ├── models/
│   ├── routes/
│   └── app.js
├── tests/
│   ├── unit/
│   └── integration/
├── package.json
├── Dockerfile
└── docker-compose.yml
```

## Features

- **Express.js REST API** with CRUD operations
- **PostgreSQL database** with migrations
- **Unit and integration tests** with Jest
- **Docker containerization**
- **Automated deployment** to Heroku
- **Database migrations** in production
- **Health checks** and monitoring

## Workflow Overview

### CI Pipeline (`.github/workflows/ci.yml`)

```yaml
name: Node.js API CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  NODE_VERSION: '18'
  
jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:14
        env:
          POSTGRES_PASSWORD: test_password
          POSTGRES_USER: test_user
          POSTGRES_DB: test_db
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run linting
        run: npm run lint
      
      - name: Run unit tests
        run: npm run test:unit
        env:
          NODE_ENV: test
      
      - name: Run integration tests
        run: npm run test:integration
        env:
          NODE_ENV: test
          DATABASE_URL: postgresql://test_user:test_password@localhost:5432/test_db
      
      - name: Generate test coverage
        run: npm run test:coverage
      
      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3
        with:
          file: ./coverage/lcov.info
      
      - name: Build application
        run: npm run build
      
      - name: Run security audit
        run: npm audit --audit-level high

  docker-build:
    runs-on: ubuntu-latest
    needs: test
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Build Docker image
        run: docker build -t nodejs-api:${{ github.sha }} .
      
      - name: Test Docker image
        run: |
          docker run -d --name test-container -p 3000:3000 nodejs-api:${{ github.sha }}
          sleep 10
          curl -f http://localhost:3000/health || exit 1
          docker stop test-container
```

### Deployment Pipeline (`.github/workflows/deploy.yml`)

```yaml
name: Deploy to Production

on:
  push:
    branches: [main]
  workflow_dispatch:  # Allow manual deployment

env:
  NODE_VERSION: '18'
  HEROKU_APP_NAME: 'my-nodejs-api-prod'

jobs:
  deploy-staging:
    runs-on: ubuntu-latest
    environment: staging
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build application
        run: npm run build
      
      - name: Deploy to Heroku Staging
        uses: akhileshns/heroku-deploy@v3.12.12
        with:
          heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
          heroku_app_name: "my-nodejs-api-staging"
          heroku_email: ${{ secrets.HEROKU_EMAIL }}
      
      - name: Run database migrations
        run: |
          heroku run npm run db:migrate --app my-nodejs-api-staging
        env:
          HEROKU_API_KEY: ${{ secrets.HEROKU_API_KEY }}
      
      - name: Run smoke tests
        run: npm run test:smoke
        env:
          API_BASE_URL: https://my-nodejs-api-staging.herokuapp.com

  deploy-production:
    runs-on: ubuntu-latest
    needs: deploy-staging
    environment: production
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build application
        run: npm run build
      
      - name: Deploy to Heroku Production
        uses: akhileshns/heroku-deploy@v3.12.12
        with:
          heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
          heroku_app_name: ${{ env.HEROKU_APP_NAME }}
          heroku_email: ${{ secrets.HEROKU_EMAIL }}
      
      - name: Run database migrations
        run: |
          heroku run npm run db:migrate --app ${{ env.HEROKU_APP_NAME }}
        env:
          HEROKU_API_KEY: ${{ secrets.HEROKU_API_KEY }}
      
      - name: Run post-deployment tests
        run: npm run test:smoke
        env:
          API_BASE_URL: https://${{ env.HEROKU_APP_NAME }}.herokuapp.com
      
      - name: Notify team
        if: success()
        uses: 8398a7/action-slack@v3
        with:
          status: success
          text: '🚀 Production deployment successful!'
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
      
      - name: Notify on failure
        if: failure()
        uses: 8398a7/action-slack@v3
        with:
          status: failure
          text: '❌ Production deployment failed!'
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

## Sample Application Code

### package.json

```json
{
  "name": "nodejs-api-example",
  "version": "1.0.0",
  "description": "Example Node.js API with CI/CD",
  "main": "src/app.js",
  "scripts": {
    "start": "node src/app.js",
    "dev": "nodemon src/app.js",
    "build": "echo 'Build step - could include bundling, transpilation, etc.'",
    "test": "jest",
    "test:unit": "jest tests/unit",
    "test:integration": "jest tests/integration",
    "test:coverage": "jest --coverage",
    "test:smoke": "jest tests/smoke",
    "lint": "eslint src/ tests/",
    "lint:fix": "eslint src/ tests/ --fix",
    "db:migrate": "node scripts/migrate.js",
    "db:seed": "node scripts/seed.js"
  },
  "dependencies": {
    "express": "^4.18.2",
    "pg": "^8.8.0",
    "dotenv": "^16.0.3",
    "helmet": "^6.0.1",
    "cors": "^2.8.5",
    "express-rate-limit": "^6.7.0"
  },
  "devDependencies": {
    "jest": "^29.4.0",
    "supertest": "^6.3.3",
    "nodemon": "^2.0.20",
    "eslint": "^8.34.0"
  }
}
```

### src/app.js

```javascript
const express = require('express');
const helmet = require('helmet');
const cors = require('cors');
const rateLimit = require('express-rate-limit');

const app = express();
const PORT = process.env.PORT || 3000;

// Middleware
app.use(helmet());
app.use(cors());
app.use(express.json());

// Rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100 // limit each IP to 100 requests per windowMs
});
app.use(limiter);

// Routes
app.get('/health', (req, res) => {
  res.status(200).json({ 
    status: 'healthy', 
    timestamp: new Date().toISOString(),
    version: process.env.npm_package_version || '1.0.0'
  });
});

app.get('/api/users', (req, res) => {
  // Mock user data
  const users = [
    { id: 1, name: 'John Doe', email: 'john@example.com' },
    { id: 2, name: 'Jane Smith', email: 'jane@example.com' }
  ];
  res.json(users);
});

app.post('/api/users', (req, res) => {
  const { name, email } = req.body;
  
  if (!name || !email) {
    return res.status(400).json({ error: 'Name and email are required' });
  }
  
  const newUser = {
    id: Math.floor(Math.random() * 1000),
    name,
    email
  };
  
  res.status(201).json(newUser);
});

// Error handling
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ error: 'Something went wrong!' });
});

// 404 handler
app.use('*', (req, res) => {
  res.status(404).json({ error: 'Route not found' });
});

if (require.main === module) {
  app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`);
  });
}

module.exports = app;
```

### tests/integration/api.test.js

```javascript
const request = require('supertest');
const app = require('../../src/app');

describe('API Integration Tests', () => {
  describe('GET /health', () => {
    it('should return health status', async () => {
      const response = await request(app)
        .get('/health')
        .expect(200);
      
      expect(response.body.status).toBe('healthy');
      expect(response.body.timestamp).toBeDefined();
    });
  });

  describe('GET /api/users', () => {
    it('should return list of users', async () => {
      const response = await request(app)
        .get('/api/users')
        .expect(200);
      
      expect(Array.isArray(response.body)).toBe(true);
      expect(response.body.length).toBeGreaterThan(0);
    });
  });

  describe('POST /api/users', () => {
    it('should create a new user', async () => {
      const newUser = {
        name: 'Test User',
        email: 'test@example.com'
      };

      const response = await request(app)
        .post('/api/users')
        .send(newUser)
        .expect(201);
      
      expect(response.body.name).toBe(newUser.name);
      expect(response.body.email).toBe(newUser.email);
      expect(response.body.id).toBeDefined();
    });

    it('should return 400 for invalid data', async () => {
      const response = await request(app)
        .post('/api/users')
        .send({ name: 'Test User' }) // Missing email
        .expect(400);
      
      expect(response.body.error).toBe('Name and email are required');
    });
  });
});
```

### Dockerfile

```dockerfile
FROM node:18-alpine

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy source code
COPY src/ ./src/

# Create non-root user
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nodeuser -u 1001

# Change ownership
CHOWN nodeuser:nodejs /app
USER nodeuser

# Expose port
EXPOSE 3000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1

# Start application
CMD ["npm", "start"]
```

## Required Secrets

Add these secrets to your GitHub repository settings:

- `HEROKU_API_KEY`: Your Heroku API key
- `HEROKU_EMAIL`: Your Heroku account email
- `SLACK_WEBHOOK_URL`: Slack webhook for notifications (optional)

## Setup Instructions

1. **Clone this example** to your repository
2. **Install dependencies**: `npm install`
3. **Set up environment variables**:
   ```bash
   cp .env.example .env
   # Edit .env with your configuration
   ```
4. **Run tests locally**:
   ```bash
   npm test
   ```
5. **Configure Heroku apps**:
   ```bash
   heroku create my-nodejs-api-staging
   heroku create my-nodejs-api-prod
   ```
6. **Push to GitHub** and watch your pipeline run!

## Customization

- **Database**: Replace mock data with real PostgreSQL integration
- **Authentication**: Add JWT or OAuth authentication
- **Logging**: Integrate with logging services (Winston, Pino)
- **Monitoring**: Add APM tools (New Relic, DataDog)
- **Documentation**: Generate API docs with Swagger/OpenAPI

## Key Features Demonstrated

- **Multi-environment deployment** (staging → production)
- **Database service** in CI with PostgreSQL
- **Docker containerization** and testing
- **Security scanning** with npm audit
- **Test coverage** reporting
- **Smoke testing** after deployment
- **Team notifications** on deployment status
- **Manual deployment triggers** for emergency releases
