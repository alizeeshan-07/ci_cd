# GitHub Actions - Hands-on CI/CD 🚀

Let's build your first CI/CD pipeline using GitHub Actions! By the end of this guide, you'll have a working automated pipeline for your projects.

## What is GitHub Actions?

GitHub Actions is GitHub's built-in CI/CD platform that lets you automate workflows directly in your repository.

**Key Benefits**:

- **Integrated**: Built into GitHub, no external setup needed
- **Free**: Generous free tier for public and private repos
- **Powerful**: Supports complex workflows and integrations
- **Community**: Thousands of pre-built actions to use

## How GitHub Actions Works

### Core Concepts

**Workflows**: Automated processes defined in YAML files  
**Jobs**: Groups of steps that run on the same runner  
**Steps**: Individual tasks (run commands, use actions)  
**Actions**: Reusable units of code  
**Runners**: Virtual machines that execute workflows  

### Workflow Anatomy

```
Workflow (entire automation)
├── Job 1 (runs on Ubuntu)
│   ├── Step 1: Checkout code
│   ├── Step 2: Setup Node.js
│   └── Step 3: Run tests
└── Job 2 (runs on Windows)
    ├── Step 1: Checkout code
    └── Step 2: Build application
```

## Your First GitHub Action

Let's create a simple workflow that runs tests when code is pushed.

### Step 1: Create the Workflow File

Create this directory structure in your repository:

```
your-repo/
├── .github/
│   └── workflows/
│       └── ci.yml
├── src/
└── package.json
```

### Step 2: Basic Workflow Configuration

Create `.github/workflows/ci.yml`:

```yaml
name: CI Pipeline

# When should this workflow run?
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

# What jobs should run?
jobs:
  test:
    # What type of machine should run this job?
    runs-on: ubuntu-latest
    
    # What steps should this job execute?
    steps:
      # Step 1: Get the code
      - name: Checkout code
        uses: actions/checkout@v3
      
      # Step 2: Setup Node.js
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      # Step 3: Install dependencies
      - name: Install dependencies
        run: npm install
      
      # Step 4: Run tests
      - name: Run tests
        run: npm test
      
      # Step 5: Run linting
      - name: Run linting
        run: npm run lint
```

### Step 3: Create a Sample Project

Let's create a simple Node.js project to test our workflow:

**package.json**:

```json
{
  "name": "cicd-demo",
  "version": "1.0.0",
  "scripts": {
    "test": "jest",
    "lint": "eslint src/",
    "start": "node src/index.js"
  },
  "devDependencies": {
    "jest": "^29.0.0",
    "eslint": "^8.0.0"
  }
}
```

**src/index.js**:

```javascript
function add(a, b) {
  return a + b;
}

function multiply(a, b) {
  return a * b;
}

module.exports = { add, multiply };
```

**src/index.test.js**:

```javascript
const { add, multiply } = require('./index');

test('adds 1 + 2 to equal 3', () => {
  expect(add(1, 2)).toBe(3);
});

test('multiplies 3 * 4 to equal 12', () => {
  expect(multiply(3, 4)).toBe(12);
});
```

### Step 4: Trigger the Workflow

1. **Commit and push** your changes:

```bash
git add .
git commit -m "Add CI workflow and sample code"
git push origin main
```

2. **Check the Actions tab** in your GitHub repository
3. **Watch your workflow run** automatically!

## Advanced Workflow Examples

### Multi-Job Pipeline

```yaml
name: Advanced CI/CD Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  # Job 1: Test the code
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm install
      - run: npm test
      - run: npm run lint

  # Job 2: Build the application
  build:
    runs-on: ubuntu-latest
    needs: test  # Only run if test job succeeds
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm install
      - run: npm run build
      
      # Save build artifacts
      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build-files
          path: dist/

  # Job 3: Deploy to staging (only on main branch)
  deploy-staging:
    runs-on: ubuntu-latest
    needs: [test, build]
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v3
      
      # Download build artifacts from previous job
      - name: Download build artifacts
        uses: actions/download-artifact@v3
        with:
          name: build-files
          path: dist/
      
      - name: Deploy to staging
        run: |
          echo "Deploying to staging environment..."
          # Your deployment commands here
```

### Environment-Specific Deployments

```yaml
name: Deploy Pipeline

on:
  push:
    branches: [main, develop]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      # Deploy to different environments based on branch
      - name: Deploy to Development
        if: github.ref == 'refs/heads/develop'
        run: |
          echo "Deploying to development environment"
          # Development deployment commands
        
      - name: Deploy to Production
        if: github.ref == 'refs/heads/main'
        run: |
          echo "Deploying to production environment"
          # Production deployment commands
```

## Working with Secrets

For deploying or accessing external services, you'll need to store sensitive information securely.

### Adding Secrets to GitHub

1. Go to your repository **Settings**
2. Click **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Add your secrets (e.g., `API_KEY`, `DATABASE_URL`)

### Using Secrets in Workflows

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Deploy to server
        env:
          API_KEY: ${{ secrets.API_KEY }}
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
        run: |
          echo "Deploying with API key: ${API_KEY:0:4}..."
          # Deployment commands using environment variables
```

## Popular GitHub Actions

### Essential Actions

```yaml
# Checkout repository code
- uses: actions/checkout@v3

# Setup programming languages
- uses: actions/setup-node@v3
- uses: actions/setup-python@v4
- uses: actions/setup-java@v3

# Cache dependencies
- uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}

# Upload/Download artifacts
- uses: actions/upload-artifact@v3
- uses: actions/download-artifact@v3
```

### Deployment Actions

```yaml
# Deploy to AWS
- uses: aws-actions/configure-aws-credentials@v2

# Deploy to Heroku
- uses: akhileshns/heroku-deploy@v3.12.12

# Deploy to GitHub Pages
- uses: peaceiris/actions-gh-pages@v3

# Docker build and push
- uses: docker/build-push-action@v4
```

### Testing and Quality Actions

```yaml
# Code coverage
- uses: codecov/codecov-action@v3

# Security scanning
- uses: github/codeql-action/analyze@v2

# Dependency review
- uses: actions/dependency-review-action@v3
```

## Matrix Builds

Test your code across multiple environments:

```yaml
jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node-version: [16, 18, 20]
        
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}
      - run: npm test
```

This creates 9 jobs (3 OS × 3 Node versions)!

## Conditional Workflows

### Run Only on Specific Changes

```yaml
on:
  push:
    paths:
      - 'src/**'      # Only when src/ files change
      - '!src/docs/**' # Except docs
    branches:
      - main
      - develop
```

### Schedule Workflows

```yaml
on:
  schedule:
    - cron: '0 2 * * *'  # Run daily at 2 AM
  push:
    branches: [main]
```

### Manual Triggers

```yaml
on:
  workflow_dispatch:  # Allows manual triggering
    inputs:
      environment:
        description: 'Environment to deploy to'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to ${{ github.event.inputs.environment }}
        run: echo "Deploying to ${{ github.event.inputs.environment }}"
```

## Real-World Example: Complete Web App Pipeline

Let's build a complete pipeline for a web application:

```yaml
name: Web App CI/CD

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  NODE_VERSION: '18'
  
jobs:
  # Job 1: Quality checks
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
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
      
      - name: Run integration tests
        run: npm run test:integration
      
      - name: Check test coverage
        run: npm run test:coverage
      
      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3

  # Job 2: Build application
  build:
    runs-on: ubuntu-latest
    needs: quality
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build application
        run: npm run build
      
      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: dist-files
          path: dist/

  # Job 3: Deploy to staging
  deploy-staging:
    runs-on: ubuntu-latest
    needs: [quality, build]
    if: github.ref == 'refs/heads/develop'
    environment: staging
    steps:
      - uses: actions/checkout@v3
      
      - name: Download build artifacts
        uses: actions/download-artifact@v3
        with:
          name: dist-files
          path: dist/
      
      - name: Deploy to staging
        env:
          STAGING_API_KEY: ${{ secrets.STAGING_API_KEY }}
        run: |
          echo "Deploying to staging..."
          # Deploy commands here
      
      - name: Run smoke tests
        run: npm run test:smoke -- --env=staging

  # Job 4: Deploy to production
  deploy-production:
    runs-on: ubuntu-latest
    needs: [quality, build]
    if: github.ref == 'refs/heads/main'
    environment: production
    steps:
      - uses: actions/checkout@v3
      
      - name: Download build artifacts
        uses: actions/download-artifact@v3
        with:
          name: dist-files
          path: dist/
      
      - name: Deploy to production
        env:
          PRODUCTION_API_KEY: ${{ secrets.PRODUCTION_API_KEY }}
        run: |
          echo "Deploying to production..."
          # Deploy commands here
      
      - name: Run smoke tests
        run: npm run test:smoke -- --env=production
      
      - name: Notify team
        if: success()
        run: |
          echo "Production deployment successful!"
          # Send notification (Slack, email, etc.)
```

## Debugging Workflows

### Common Issues and Solutions

**Workflow doesn't trigger**:
- Check file is in `.github/workflows/`
- Verify YAML syntax
- Check trigger conditions (`on:` section)

**Job fails unexpectedly**:
- Check the logs in Actions tab
- Verify all required secrets are set
- Test commands locally first

**Dependency issues**:
- Use `npm ci` instead of `npm install` for reproducible builds
- Cache dependencies to speed up builds
- Lock dependency versions

### Debugging Tips

```yaml
# Add debug information
- name: Debug workflow
  run: |
    echo "GitHub context:"
    echo "Event: ${{ github.event_name }}"
    echo "Ref: ${{ github.ref }}"
    echo "SHA: ${{ github.sha }}"
    
# Test with smaller steps
- name: Test environment
  run: |
    node --version
    npm --version
    pwd
    ls -la
```

## Best Practices

### Performance

- **Use caching** for dependencies
- **Run jobs in parallel** when possible
- **Use appropriate runner types** (ubuntu is fastest/cheapest)
- **Cache build artifacts** between jobs

### Security

- **Never commit secrets** to your repository
- **Use least privilege** access for tokens
- **Regularly rotate secrets**
- **Use GitHub's security features** (dependency scanning, code scanning)

### Maintainability

- **Use descriptive job and step names**
- **Comment complex workflows**
- **Break large workflows** into smaller, reusable ones
- **Use workflow templates** for consistency

### Example: Optimized Workflow

```yaml
name: Optimized CI/CD

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      # Cache dependencies for faster builds
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      # Run tests in parallel using matrix
      - name: Run tests
        run: npm run test -- --parallel
      
      # Only run coverage on main branch
      - name: Generate coverage
        if: github.ref == 'refs/heads/main'
        run: npm run test:coverage
```

## Practice Exercises

### Exercise 1: Basic CI Pipeline

1. Create a new repository with a simple JavaScript project
2. Add a GitHub Actions workflow that:
   - Runs on push and pull requests
   - Installs dependencies
   - Runs tests
   - Runs linting

### Exercise 2: Multi-Environment Deployment

1. Extend your workflow to deploy to different environments:
   - `develop` branch → staging environment
   - `main` branch → production environment
2. Add environment-specific secrets

### Exercise 3: Matrix Testing

1. Create a workflow that tests your code across:
   - Multiple Node.js versions (16, 18, 20)
   - Multiple operating systems (Ubuntu, Windows, macOS)

## Next Steps

Congratulations! You now know how to build CI/CD pipelines with GitHub Actions. Next, explore the [Practical Examples](./examples/) folder for real-world scenarios and advanced patterns.

## Key Takeaways

- GitHub Actions automates your entire software workflow
- Workflows are defined in YAML files in `.github/workflows/`
- Jobs can run in parallel or sequence with dependencies
- Use secrets for sensitive information
- Matrix builds test across multiple environments
- Caching and optimization improve performance
- Start simple and gradually add complexity
