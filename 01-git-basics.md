# Git Basics 📚

Git is the foundation of modern CI/CD. Let's learn the essential commands and concepts.

## What is Git?

Git is a **version control system** that tracks changes in your code. Think of it as a time machine for your project that lets you:
- Save snapshots of your code (commits)
- Work on different features simultaneously (branches)
- Collaborate with others safely
- Roll back to previous versions if something breaks

## Essential Git Commands

### Setting Up Git (First Time Only)

```bash
# Set your name and email (used in commits)
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Check your configuration
git config --list
```

### Creating a Repository

```bash
# Initialize a new Git repository
git init

# Add a remote repository (GitHub)
git remote add origin https://github.com/username/repository-name.git
```

### Basic Workflow

```bash
# 1. Check status of your files
git status

# 2. Add files to staging area
git add filename.txt          # Add specific file
git add .                     # Add all files

# 3. Commit your changes
git commit -m "Your commit message"

# 4. Push to remote repository
git push origin main
```

### Working with Branches

```bash
# Create a new branch
git branch feature-branch-name

# Switch to a branch
git checkout feature-branch-name

# Create and switch in one command
git checkout -b feature-branch-name

# List all branches
git branch

# Merge a branch into main
git checkout main
git merge feature-branch-name

# Delete a branch
git branch -d feature-branch-name
```

### Getting Updates

```bash
# Get latest changes from remote
git pull origin main

# Fetch changes without merging
git fetch origin
```

## Git Workflow Example

Let's say you want to add a new feature:

```bash
# 1. Start from main branch
git checkout main
git pull origin main

# 2. Create feature branch
git checkout -b add-login-feature

# 3. Make your changes, then commit
git add .
git commit -m "Add user login functionality"

# 4. Push your branch
git push origin add-login-feature

# 5. Create a Pull Request on GitHub (we'll cover this next)
```

## Understanding Commits

A commit is like a snapshot of your project at a specific point in time. Good commits:

- Have clear, descriptive messages
- Contain related changes
- Are small and focused

### Good Commit Messages

```bash
# ✅ Good examples
git commit -m "Add user authentication system"
git commit -m "Fix login button styling issue"
git commit -m "Update README with installation instructions"

# ❌ Bad examples
git commit -m "stuff"
git commit -m "changes"
git commit -m "fix"
```

## Common Git Scenarios

### Undo Last Commit (but keep changes)
```bash
git reset --soft HEAD~1
```

### Discard All Local Changes
```bash
git checkout -- .
```

### See Commit History
```bash
git log --oneline
```

### Check Differences
```bash
git diff                    # See unstaged changes
git diff --staged          # See staged changes
```

## Practice Exercise

1. Create a new file called `hello.txt` with some content
2. Add and commit the file
3. Create a new branch called `feature-update`
4. Modify the file and commit the changes
5. Switch back to main and see the difference

Try these commands:

```bash
echo "Hello, World!" > hello.txt
git add hello.txt
git commit -m "Add hello.txt file"

git checkout -b feature-update
echo "Hello, CI/CD World!" > hello.txt
git add hello.txt
git commit -m "Update greeting message"

git checkout main
cat hello.txt  # Should show original content
```

## Next Steps

Now that you understand Git basics, let's learn about [GitHub Workflow](./02-github-workflow.md) to see how teams collaborate using Git and GitHub.

## Key Takeaways

- Git tracks changes in your code over time
- Branches let you work on features without affecting main code
- Commits are snapshots of your project
- Always write clear commit messages
- Pull before you push to get latest changes
