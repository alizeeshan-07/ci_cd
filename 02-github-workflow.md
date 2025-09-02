# GitHub Workflow & Pull Requests 🔄

Now that you know Git basics, let's learn how teams collaborate using GitHub and the power of Pull Requests!

## What is GitHub?

GitHub is a cloud platform that hosts Git repositories and provides collaboration tools. It's like Google Drive for code, but much more powerful.

## GitHub Workflow Overview

The typical GitHub workflow looks like this:

1. **Fork/Clone** a repository
2. **Create a branch** for your feature
3. **Make changes** and commit them
4. **Push** your branch to GitHub
5. **Create a Pull Request** (PR)
6. **Code Review** by team members
7. **Merge** the PR into main branch
8. **Deploy** the changes (CI/CD)

## Setting Up Your GitHub Repository

### Creating a New Repository

1. Go to GitHub.com and click "New repository"
2. Choose a name (e.g., `my-first-cicd-project`)
3. Add a description
4. Choose public or private
5. Initialize with README
6. Click "Create repository"

### Connecting Local Repository to GitHub

```bash
# If you already have a local repository
git remote add origin https://github.com/yourusername/repository-name.git
git branch -M main
git push -u origin main

# If you're starting fresh
git clone https://github.com/yourusername/repository-name.git
cd repository-name
```

## Understanding Pull Requests (PRs)

A **Pull Request** is a way to propose changes to a repository. Think of it as saying: "Hey team, I made some changes. Can you review them before we add them to the main code?"

### Why Use Pull Requests?

- **Code Review**: Others can check your code for bugs or improvements
- **Discussion**: Team can discuss the changes before merging
- **Testing**: Automated tests can run before merging
- **Documentation**: Track what changed and why
- **Safety**: Prevents bad code from reaching production

### Pull Request Workflow

```bash
# 1. Create and switch to a new branch
git checkout -b feature/add-user-profile

# 2. Make your changes
# ... edit files ...

# 3. Commit your changes
git add .
git commit -m "Add user profile page with avatar upload"

# 4. Push your branch to GitHub
git push origin feature/add-user-profile

# 5. Go to GitHub and create a Pull Request
```

## Creating a Pull Request on GitHub

1. **Push your branch** to GitHub
2. **Navigate** to your repository on GitHub
3. **Click "Compare & pull request"** (appears after pushing)
4. **Fill out the PR form**:
   - **Title**: Clear, descriptive title
   - **Description**: What changed and why
   - **Reviewers**: Add team members to review
5. **Click "Create pull request"**

### Good PR Description Template

```markdown
## What changed?
- Added user profile page
- Implemented avatar upload functionality
- Added profile editing capabilities

## Why?
Users requested the ability to customize their profiles and upload profile pictures.

## How to test?
1. Navigate to /profile
2. Upload an image
3. Edit profile information
4. Save changes

## Screenshots
[Add screenshots if UI changes]

## Checklist
- [x] Code follows style guidelines
- [x] Tests added for new functionality
- [x] Documentation updated
- [ ] Reviewed by team lead
```

## Pull Request Review Process

### As a Reviewer

When reviewing a PR:

1. **Read the description** first
2. **Check the code changes** line by line
3. **Test the changes** if possible
4. **Leave comments** for:
   - Bugs or issues
   - Suggestions for improvement
   - Questions about the code
   - Compliments on good work!

### Review Comments

```markdown
# Requesting changes
Could you add error handling for the file upload?

# Suggesting improvements
Consider using a more descriptive variable name here.

# Asking questions
Why did you choose this approach over using the existing utility function?

# Approving
Looks great! The error handling is much improved.
```

### PR Review States

- **Comment**: General feedback without approval/rejection
- **Approve**: Code looks good and can be merged
- **Request Changes**: Issues that must be fixed before merging

## Merging Strategies

When a PR is approved, you can merge it in different ways:

### 1. Merge Commit
```bash
# Creates a merge commit that combines the branches
git checkout main
git merge feature/add-user-profile
```
- **Pros**: Preserves branch history
- **Cons**: Creates "merge bubbles" in history

### 2. Squash and Merge
```bash
# Combines all commits into one
```
- **Pros**: Clean, linear history
- **Cons**: Loses individual commit history

### 3. Rebase and Merge
```bash
# Replays commits on top of main branch
git checkout feature/add-user-profile
git rebase main
```
- **Pros**: Clean, linear history with individual commits
- **Cons**: Can be complex for beginners

## Branch Protection Rules

For important branches (like `main`), you can set up protection rules:

- **Require PR reviews** before merging
- **Require status checks** (tests must pass)
- **Restrict who can push** to the branch
- **Require up-to-date branches** before merging

### Setting Up Branch Protection

1. Go to repository **Settings**
2. Click **Branches** in sidebar
3. Click **Add rule**
4. Configure protection settings

## Common GitHub Workflow Scenarios

### Scenario 1: Simple Feature Addition

```bash
# 1. Start from updated main
git checkout main
git pull origin main

# 2. Create feature branch
git checkout -b feature/add-contact-form

# 3. Make changes and commit
echo "<form>Contact Form HTML</form>" > contact.html
git add contact.html
git commit -m "Add contact form"

# 4. Push and create PR
git push origin feature/add-contact-form
# Then create PR on GitHub
```

### Scenario 2: Fixing a Bug

```bash
# 1. Create hotfix branch
git checkout -b hotfix/fix-login-error

# 2. Fix the bug
# ... edit files ...
git add .
git commit -m "Fix login validation error"

# 3. Push and create urgent PR
git push origin hotfix/fix-login-error
# Create PR with "urgent" label
```

### Scenario 3: Collaborative Feature

```bash
# 1. Alice creates feature branch
git checkout -b feature/shopping-cart

# 2. Alice makes initial commit
git commit -m "Add shopping cart structure"
git push origin feature/shopping-cart

# 3. Bob works on same branch
git checkout feature/shopping-cart
git pull origin feature/shopping-cart
# ... Bob makes changes ...
git commit -m "Add cart item removal"
git push origin feature/shopping-cart

# 4. Create PR when feature is complete
```

## Practice Exercise

Let's practice the complete workflow:

1. **Create a new repository** on GitHub called `cicd-practice`
2. **Clone it** to your computer
3. **Create a branch** called `feature/add-readme-improvements`
4. **Edit the README.md** file to add more content
5. **Commit and push** your changes
6. **Create a Pull Request** on GitHub
7. **Review and merge** your own PR

```bash
# Commands to try:
git clone https://github.com/yourusername/cicd-practice.git
cd cicd-practice
git checkout -b feature/add-readme-improvements

# Edit README.md file
echo "# My CI/CD Practice Project" > README.md
echo "This project helps me learn CI/CD workflows." >> README.md

git add README.md
git commit -m "Improve README with project description"
git push origin feature/add-readme-improvements

# Go to GitHub and create the PR!
```

## Next Steps

Now you understand Git and GitHub workflows! Next, let's dive into [CI/CD Concepts](./03-cicd-concepts.md) to learn how to automate testing and deployment.

## Key Takeaways

- Pull Requests enable safe code collaboration
- Always create a branch for new features
- Write clear PR descriptions and commit messages
- Code reviews catch bugs and improve code quality
- Branch protection rules keep your main branch safe
- Different merge strategies serve different purposes
