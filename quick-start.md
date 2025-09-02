# Quick Start Guide 🚀

New to CI/CD? Start here! This guide will get you up and running with your first automated pipeline in 15 minutes.

## Before You Begin

**You'll need**:
- Git installed on your computer
- A GitHub account
- Basic command line knowledge

**What you'll learn**:
- How to create your first GitHub Actions workflow
- How to automatically test code changes
- How to deploy your application automatically

## 5-Minute Setup

### Step 1: Create a New Repository

1. Go to [GitHub.com](https://github.com) and click "New repository"
2. Name it `my-first-cicd-project`
3. Check "Add a README file"
4. Click "Create repository"

### Step 2: Clone and Set Up Locally

```bash
# Clone your repository
git clone https://github.com/YOUR_USERNAME/my-first-cicd-project.git
cd my-first-cicd-project

# Create a simple Node.js project
npm init -y
npm install express jest supertest

# Create basic project structure
mkdir src tests
```

### Step 3: Create a Simple App

Create `src/app.js`:

```javascript
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.json({ message: 'Hello, CI/CD World!' });
});

app.get('/health', (req, res) => {
  res.json({ status: 'healthy' });
});

const PORT = process.env.PORT || 3000;
if (require.main === module) {
  app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}`);
  });
}

module.exports = app;
```

Create `tests/app.test.js`:

```javascript
const request = require('supertest');
const app = require('../src/app');

test('GET / should return hello message', async () => {
  const response = await request(app)
    .get('/')
    .expect(200);
  
  expect(response.body.message).toBe('Hello, CI/CD World!');
});

test('GET /health should return healthy status', async () => {
  const response = await request(app)
    .get('/health')
    .expect(200);
  
  expect(response.body.status).toBe('healthy');
});
```

Update `package.json` scripts:

```json
{
  "scripts": {
    "start": "node src/app.js",
    "test": "jest",
    "dev": "node src/app.js"
  }
}
```

### Step 4: Create Your First CI/CD Workflow

Create `.github/workflows/ci.yml`:

```yaml
name: My First CI/CD Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
      - name: Get the code
        uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm install
      
      - name: Run tests
        run: npm test
      
      - name: Test the app runs
        run: |
          npm start &
          sleep 5
          curl -f http://localhost:3000/health
```

### Step 5: Push and Watch the Magic!

```bash
# Add all files
git add .

# Commit your changes
git commit -m "Add simple app with CI/CD pipeline"

# Push to GitHub
git push origin main
```

**🎉 Congratulations!** Go to your repository on GitHub, click the "Actions" tab, and watch your first CI/CD pipeline run!

## What Just Happened?

1. **GitHub Actions detected** your workflow file
2. **A virtual machine** was created with Ubuntu
3. **Your code was downloaded** to the machine
4. **Dependencies were installed** automatically
5. **Tests ran** and verified your code works
6. **The app was started** and health-checked

## Next Steps: Add Automatic Deployment

Let's make it even cooler by adding automatic deployment to a free hosting service.

### Deploy to Netlify (for static sites)

Add this job to your `.github/workflows/ci.yml`:

```yaml
  deploy:
    runs-on: ubuntu-latest
    needs: test  # Only deploy if tests pass
    if: github.ref == 'refs/heads/main'  # Only deploy from main branch
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm install
      
      - name: Build app (if you have a build step)
        run: echo "Building app..." # Replace with actual build command
      
      - name: Deploy to Netlify
        uses: netlify/actions/cli@master
        with:
          args: deploy --prod --dir=.
        env:
          NETLIFY_SITE_ID: ${{ secrets.NETLIFY_SITE_ID }}
          NETLIFY_AUTH_TOKEN: ${{ secrets.NETLIFY_AUTH_TOKEN }}
```

### Deploy to Heroku (for Node.js apps)

Add this job instead:

```yaml
  deploy:
    runs-on: ubuntu-latest
    needs: test
    if: github.ref == 'refs/heads/main'
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Deploy to Heroku
        uses: akhileshns/heroku-deploy@v3.12.12
        with:
          heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
          heroku_app_name: "your-app-name"
          heroku_email: "your-email@example.com"
```

## Practice Exercises

### Exercise 1: Break and Fix
1. Modify a test to make it fail
2. Push the change and watch the pipeline fail
3. Fix the test and push again
4. See the pipeline turn green! ✅

### Exercise 2: Add a Feature
1. Create a new branch: `git checkout -b add-new-endpoint`
2. Add a new API endpoint in your app
3. Write a test for the new endpoint
4. Push the branch and create a Pull Request
5. Watch the pipeline run on your PR!

### Exercise 3: Environment Variables
1. Add an environment variable to your workflow
2. Use it in your application
3. Test that it works correctly

## Troubleshooting Common Issues

### "Tests are not running"
- Check that your `package.json` has a `test` script
- Make sure Jest is installed: `npm install jest --save-dev`

### "Workflow file not found"
- Ensure the file is in `.github/workflows/` directory
- Check the YAML syntax is correct (indentation matters!)

### "Node.js version issues"
- Make sure you're using a supported Node.js version (16, 18, or 20)
- Check your local Node.js version: `node --version`

### "Permission denied"
- Check that your repository has Actions enabled
- Go to Settings → Actions → General → Allow all actions

## Understanding the Workflow File

Let's break down what each part does:

```yaml
name: My First CI/CD Pipeline  # Name shown in GitHub Actions

on:  # When should this run?
  push:
    branches: [main]  # Run when code is pushed to main
  pull_request:
    branches: [main]  # Run when PR is created to main

jobs:  # What work should be done?
  test:  # Job name
    runs-on: ubuntu-latest  # What computer to use
    
    steps:  # What steps to execute
      - name: Get the code  # Step name (can be anything)
        uses: actions/checkout@v3  # Pre-built action to download code
      
      - name: Setup Node.js  # Another step
        uses: actions/setup-node@v3  # Pre-built action for Node.js
        with:  # Configuration for this action
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies  # Custom step
        run: npm install  # Shell command to run
```

## Key Concepts You've Learned

- **Workflows**: Automated processes that run when events happen
- **Jobs**: Groups of steps that run together
- **Steps**: Individual tasks like running commands or using actions
- **Actions**: Pre-built tools (like setup-node) that do common tasks
- **Triggers**: Events that start workflows (push, pull_request, etc.)

## What's Next?

Now you have a working CI/CD pipeline! Here's what to explore next:

1. **[Git Basics](./01-git-basics.md)** - Master version control
2. **[GitHub Workflow](./02-github-workflow.md)** - Learn about Pull Requests
3. **[CI/CD Concepts](./03-cicd-concepts.md)** - Understand the theory
4. **[GitHub Actions](./04-github-actions.md)** - Advanced pipeline features
5. **[Examples](./examples/)** - Real-world project templates

## Tips for Success

- **Start simple** and add complexity gradually
- **Test your workflows** with small changes first
- **Read the logs** when something fails - they usually tell you exactly what's wrong
- **Use the community** - thousands of pre-built actions are available
- **Practice regularly** - the more you use CI/CD, the more natural it becomes

## Get Help

- **GitHub Docs**: [docs.github.com/actions](https://docs.github.com/actions)
- **Community Forum**: [github.community](https://github.community)
- **Awesome Actions**: Pre-built actions to use in your workflows

**You did it!** 🎉 You've created your first CI/CD pipeline. Every time you push code now, it will be automatically tested. Welcome to the world of automated software delivery!
