# Day 26: Version Control & Git Workflows 🔀

## 🎯 Learning Objectives

By the end of Day 26, you will:
- Master advanced Git commands and workflows
- Understand branching strategies (GitFlow, GitHub Flow, trunk-based)
- Handle merge conflicts effectively and efficiently
- Implement Git hooks for automation and quality control
- Use interactive rebase for clean commit history
- Apply best practices for commit messages and PR reviews
- Work effectively in team collaboration scenarios
- Understand Git internals for better troubleshooting
- Implement continuous integration with Git

---

## 📚 Topics to Study

### 1. **Git Fundamentals Review**

```bash
# Repository initialization
git init
git clone <repository-url>

# Basic workflow
git status                    # Check current state
git add <file>               # Stage changes
git add .                    # Stage all changes
git commit -m "message"      # Commit staged changes
git push origin main         # Push to remote
git pull origin main         # Pull from remote

# Viewing history
git log                      # Full commit history
git log --oneline           # Compact history
git log --graph --oneline   # Visual branch history
git log -p                  # Show diffs
git show <commit-hash>      # Show specific commit

# Checking differences
git diff                    # Unstaged changes
git diff --staged          # Staged changes
git diff branch1 branch2   # Compare branches

# Undoing changes
git restore <file>          # Discard working directory changes
git restore --staged <file> # Unstage file
git reset HEAD~1           # Undo last commit (keep changes)
git reset --hard HEAD~1    # Undo last commit (discard changes)
git revert <commit-hash>   # Create new commit that undoes changes
```

### 2. **Branching Strategies**

```bash
# Branch basics
git branch                           # List branches
git branch <branch-name>            # Create branch
git checkout <branch-name>          # Switch branch
git checkout -b <branch-name>       # Create and switch
git switch <branch-name>            # Modern way to switch
git switch -c <branch-name>         # Create and switch (modern)
git branch -d <branch-name>         # Delete branch (safe)
git branch -D <branch-name>         # Force delete branch

# GitFlow Strategy
/*
Main branches:
- main/master: Production-ready code
- develop: Integration branch for features

Supporting branches:
- feature/*: New features (from develop)
- release/*: Release preparation (from develop)
- hotfix/*: Production fixes (from main)

Workflow:
1. Feature development:
   git checkout -b feature/user-auth develop
   # Work on feature
   git checkout develop
   git merge --no-ff feature/user-auth
   git branch -d feature/user-auth

2. Release preparation:
   git checkout -b release/1.2.0 develop
   # Bug fixes, version bumps
   git checkout main
   git merge --no-ff release/1.2.0
   git tag -a v1.2.0 -m "Release version 1.2.0"
   git checkout develop
   git merge --no-ff release/1.2.0
   git branch -d release/1.2.0

3. Hotfix:
   git checkout -b hotfix/security-patch main
   # Fix the issue
   git checkout main
   git merge --no-ff hotfix/security-patch
   git tag -a v1.2.1 -m "Security patch"
   git checkout develop
   git merge --no-ff hotfix/security-patch
   git branch -d hotfix/security-patch
*/

# GitHub Flow (Simpler)
/*
- main: Always deployable
- feature/*: All changes in branches
- Workflow:
  1. Create branch from main
  2. Make changes and commit
  3. Open PR
  4. Review and discuss
  5. Merge to main
  6. Deploy immediately
*/

# Trunk-Based Development
/*
- Single main branch
- Short-lived feature branches (< 2 days)
- Feature flags for incomplete features
- Continuous integration
- Frequent small commits
*/

# Branch naming conventions
feature/user-authentication
bugfix/login-error
hotfix/security-vulnerability
refactor/api-optimization
docs/update-readme
test/add-unit-tests
```

### 3. **Merging and Rebasing**

```bash
# Merge strategies
git merge <branch>              # Fast-forward if possible
git merge --no-ff <branch>     # Always create merge commit
git merge --squash <branch>    # Squash all commits into one

# Example workflow
git checkout main
git merge feature/new-feature  # Merge feature into main

# Rebasing (rewrite history)
git checkout feature/my-feature
git rebase main               # Replay commits on top of main
git rebase -i HEAD~3         # Interactive rebase (last 3 commits)

# Interactive rebase options:
/*
pick: Keep commit as is
reword: Change commit message
edit: Pause to amend commit
squash: Combine with previous commit
fixup: Like squash but discard message
drop: Remove commit
*/

# Example interactive rebase:
# git rebase -i HEAD~3
/*
pick abc123 Add user model
squash def456 Fix user model typo
reword ghi789 Update user tests

Results in 2 commits with cleaned up history
*/

# Rebase vs Merge
/*
Merge:
+ Preserves complete history
+ Non-destructive
+ Shows merge points
- Can create complex history
- "Merge bubbles" in history

Rebase:
+ Clean linear history
+ Easier to follow
+ No merge commits
- Rewrites history (dangerous on shared branches)
- Can be complex with conflicts

Golden Rule: Never rebase public branches!
*/

# Cherry-picking
git cherry-pick <commit-hash>  # Apply specific commit to current branch
git cherry-pick abc123 def456  # Pick multiple commits
```

### 4. **Handling Merge Conflicts**

```bash
# When conflicts occur during merge
git merge feature-branch
# CONFLICT: Merge conflict in file.js

# Check conflict status
git status

# Conflict markers in file:
/*
<<<<<<< HEAD (current branch)
const version = '1.0.0';
=======
const version = '2.0.0';
>>>>>>> feature-branch

Resolution:
1. Choose one version
2. Combine both
3. Write new solution
*/

# After resolving conflicts
git add <resolved-file>
git commit  # Complete the merge

# Abort merge if needed
git merge --abort

# Tools for conflict resolution
git mergetool              # Launch merge tool
git diff --ours           # Show our changes
git diff --theirs         # Show their changes
git diff --base           # Show base version

# Rebase conflicts
git rebase main
# CONFLICT
# Resolve conflict
git add <file>
git rebase --continue     # Continue rebase
git rebase --abort        # Cancel rebase
git rebase --skip         # Skip current commit

# Strategies to minimize conflicts
/*
1. Pull frequently
2. Keep branches short-lived
3. Communicate with team
4. Break large features into smaller pieces
5. Use feature flags
6. Coordinate on shared files
*/
```

### 5. **Git History Management**

```bash
# Amending commits
git commit --amend                    # Modify last commit
git commit --amend --no-edit         # Keep same message
git commit --amend -m "New message"  # Change message

# Resetting
git reset --soft HEAD~1   # Undo commit, keep changes staged
git reset --mixed HEAD~1  # Undo commit, keep changes unstaged
git reset --hard HEAD~1   # Undo commit, discard changes

# Reflog (recovery tool)
git reflog                # Show all ref updates
git reset --hard HEAD@{2} # Recover to previous state

# Stashing changes
git stash                     # Save changes temporarily
git stash push -m "WIP: feature" # Stash with message
git stash list               # List all stashes
git stash pop                # Apply and remove last stash
git stash apply stash@{0}    # Apply specific stash
git stash drop stash@{0}     # Delete specific stash
git stash clear              # Delete all stashes

# Cleaning
git clean -n              # Dry run (show what would be deleted)
git clean -fd             # Force delete untracked files/directories
git clean -fdx            # Also remove ignored files

# Tagging
git tag v1.0.0                          # Lightweight tag
git tag -a v1.0.0 -m "Release 1.0.0"   # Annotated tag
git tag -a v1.0.0 <commit-hash>        # Tag specific commit
git push origin v1.0.0                 # Push specific tag
git push origin --tags                 # Push all tags
git tag -d v1.0.0                      # Delete local tag
git push origin :refs/tags/v1.0.0      # Delete remote tag
```

### 6. **Git Hooks**

```bash
# Git hooks are scripts that run automatically on certain Git events
# Located in .git/hooks/ directory

# Common hooks:
# pre-commit: Before commit is created
# pre-push: Before push to remote
# commit-msg: After commit message is entered
# post-checkout: After checking out a branch
# pre-rebase: Before rebase

# Example: pre-commit hook (.git/hooks/pre-commit)
#!/bin/sh
# Run linter before commit
npm run lint
if [ $? -ne 0 ]; then
  echo "Linting failed. Commit aborted."
  exit 1
fi

# Run tests before commit
npm test
if [ $? -ne 0 ]; then
  echo "Tests failed. Commit aborted."
  exit 1
fi

# Make hook executable
chmod +x .git/hooks/pre-commit

# Using Husky for managing hooks
# npm install --save-dev husky
# npx husky install

# Create pre-commit hook
# npx husky add .git/hooks/pre-commit "npm test"

# package.json with Husky
{
  "husky": {
    "hooks": {
      "pre-commit": "npm run lint && npm test",
      "pre-push": "npm run build",
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
    }
  }
}

# Lint-staged (run linters on staged files only)
# npm install --save-dev lint-staged

# package.json
{
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{json,md}": [
      "prettier --write"
    ]
  }
}

# .husky/pre-commit
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx lint-staged
```

### 7. **Commit Message Best Practices**

```bash
# Conventional Commits format
# <type>(<scope>): <subject>
#
# <body>
#
# <footer>

# Types:
# feat: New feature
# fix: Bug fix
# docs: Documentation changes
# style: Code style changes (formatting)
# refactor: Code refactoring
# perf: Performance improvements
# test: Adding or updating tests
# chore: Maintenance tasks
# ci: CI/CD changes
# build: Build system changes

# Examples:
git commit -m "feat(auth): add user authentication system"

git commit -m "fix(api): resolve timeout issue in data fetch

- Increased timeout from 5s to 10s
- Added retry logic for failed requests
- Updated error handling

Closes #123"

git commit -m "docs: update README with installation instructions"

git commit -m "refactor(components): simplify button component logic"

# Breaking changes:
git commit -m "feat(api)!: change API response format

BREAKING CHANGE: API now returns data in new format
Migration guide: https://docs.example.com/migration"

# Using commitlint
# npm install --save-dev @commitlint/cli @commitlint/config-conventional

# commitlint.config.js
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [
      2,
      'always',
      ['feat', 'fix', 'docs', 'style', 'refactor', 'perf', 'test', 'chore']
    ],
    'subject-case': [2, 'always', 'lower-case'],
    'subject-max-length': [2, 'always', 72]
  }
};
```

### 8. **Collaboration Workflows**

```bash
# Forking workflow (open source)
# 1. Fork repository on GitHub
# 2. Clone your fork
git clone https://github.com/YOUR_USERNAME/repo.git
cd repo

# 3. Add upstream remote
git remote add upstream https://github.com/ORIGINAL_OWNER/repo.git

# 4. Create feature branch
git checkout -b feature/awesome-feature

# 5. Make changes and commit
git add .
git commit -m "feat: add awesome feature"

# 6. Keep fork updated
git fetch upstream
git rebase upstream/main

# 7. Push to your fork
git push origin feature/awesome-feature

# 8. Create Pull Request on GitHub

# Pull Request best practices
/*
1. Clear title following commit conventions
2. Detailed description:
   - What changes were made
   - Why changes were necessary
   - How to test the changes
   - Screenshots/demos if applicable
3. Link related issues
4. Request specific reviewers
5. Keep PR focused and small
6. Respond to feedback promptly
7. Update PR based on reviews
8. Ensure CI passes
*/

# PR template (.github/pull_request_template.md)
/*
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
Steps to test the changes

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Comments added for complex code
- [ ] Documentation updated
- [ ] Tests added/updated
- [ ] All tests passing
- [ ] No merge conflicts
*/

# Code review checklist
/*
1. Code quality and maintainability
2. Test coverage
3. Performance implications
4. Security concerns
5. Documentation
6. Breaking changes
7. Edge cases handled
8. Error handling
9. Naming conventions
10. Code duplication
*/
```

### 9. **Advanced Git Commands**

```bash
# Bisect (find bug-introducing commit)
git bisect start
git bisect bad                    # Current commit is bad
git bisect good <commit-hash>     # Known good commit
# Git checks out commits, test each
git bisect good/bad               # Mark as good or bad
git bisect reset                  # Done

# Blame (find who changed lines)
git blame <file>                  # Show line-by-line authors
git blame -L 10,20 <file>        # Specific lines

# Submodules (nested repositories)
git submodule add <repo-url> <path>
git submodule init
git submodule update
git submodule update --remote    # Update to latest

# Worktrees (multiple working directories)
git worktree add ../hotfix-branch hotfix/urgent-fix
git worktree list
git worktree remove ../hotfix-branch

# Rerere (reuse recorded resolution)
git config --global rerere.enabled true
# Automatically reuse conflict resolutions

# Filter-branch (rewrite history)
# Remove sensitive data
git filter-branch --tree-filter 'rm -f passwords.txt' HEAD

# BFG Repo Cleaner (better alternative)
# java -jar bfg.jar --delete-files passwords.txt
```

---

## 💡 Key Concepts

1. **Version Control**: Track changes and collaborate effectively
2. **Branching**: Isolate features and experiments
3. **Merging**: Integrate changes from different branches
4. **Rebasing**: Maintain clean linear history
5. **Conflict Resolution**: Handle overlapping changes
6. **Commit History**: Maintain readable and useful history
7. **Collaboration**: Work effectively with teams
8. **Automation**: Use hooks for quality control

---

## 🛠️ Practical Exercises

### Exercise 1: GitFlow Practice
Implement a complete GitFlow workflow with features, releases, and hotfixes.

### Exercise 2: Conflict Resolution
Create intentional conflicts and practice resolution strategies.

### Exercise 3: Interactive Rebase
Clean up messy commit history with interactive rebase.

### Exercise 4: Git Hooks Setup
Configure pre-commit hooks with linting and testing.

### Exercise 5: Team Collaboration Simulation
Practice forking, PR creation, code review, and merging.

---

## 🎨 Project: Team Collaboration Workflow

Simulate a real-world team development scenario:

**Setup:**
1. Create a repository with main and develop branches
2. Implement GitFlow branching strategy
3. Set up Git hooks for code quality
4. Configure commit message linting
5. Create PR templates

**Workflow:**
1. Feature development with proper branching
2. Create pull requests with descriptions
3. Simulate code reviews with comments
4. Handle merge conflicts
5. Create releases with tags
6. Handle hotfixes properly
7. Maintain clean commit history

**Deliverables:**
- Well-structured repository
- Clean commit history
- Proper branch strategy
- Automated quality checks
- Documentation of workflow

---

## 📖 Resources to Explore

### Documentation
- **Pro Git Book**: https://git-scm.com/book/en/v2
- **GitHub Docs**: https://docs.github.com/
- **Atlassian Git Tutorials**: Comprehensive guides

### Video Tutorials
- **Git & GitHub Complete Course** by freeCodeCamp
- **Advanced Git Tutorial** by The Net Ninja
- **Git for Professionals** by Tobias Günther

### Articles & Guides
- "A successful Git branching model" (GitFlow)
- "GitHub Flow" guide
- "Conventional Commits" specification
- "Git Best Practices"

### Tools
- **GitKraken**: Visual Git client
- **SourceTree**: Free Git GUI
- **Git Extensions**: Windows Git GUI
- **Husky**: Git hooks made easy
- **Commitizen**: Commit message helper

---

## 🔍 Interview Questions

### Basic
1. What is version control and why is it important?
2. Explain the difference between Git and GitHub.
3. What is a commit in Git?
4. What is a branch and why would you use one?
5. How do you resolve a merge conflict?

### Intermediate
1. Explain the difference between merge and rebase.
2. What is GitFlow and when would you use it?
3. How do you undo the last commit?
4. What are Git hooks and how are they useful?
5. Explain the difference between git reset and git revert.
6. What is cherry-picking in Git?
7. How do you keep your fork in sync with upstream?

### Advanced
1. Explain how git bisect works and when to use it.
2. What are the differences between git reset --soft, --mixed, and --hard?
3. How would you remove sensitive data from Git history?
4. Explain the internal structure of Git (objects, refs, etc.).
5. How do you handle a rebase gone wrong?
6. What is git reflog and how can it save you?
7. Explain git worktrees and their use cases.
8. How do you optimize a large Git repository?

### Practical
1. Your colleague force-pushed to main branch. What do you do?
2. You committed sensitive credentials. How do you fix this?
3. Design a branching strategy for a team of 50 developers.
4. How would you migrate from SVN to Git?

---

## ✅ Success Criteria

- [ ] Master basic Git commands and workflows
- [ ] Understand and apply branching strategies
- [ ] Handle merge conflicts confidently
- [ ] Write meaningful commit messages
- [ ] Use interactive rebase effectively
- [ ] Set up and use Git hooks
- [ ] Collaborate using pull requests
- [ ] Review code effectively
- [ ] Maintain clean Git history
- [ ] Troubleshoot Git issues independently

---

## 🚀 Next Steps

Tomorrow (Day 27), you'll learn:
- **CI/CD Pipelines**: Continuous Integration and Deployment
- **Automated testing and deployment**
- **GitHub Actions, GitLab CI, Jenkins**
- **Performance optimization and monitoring**

**Preparation:**
- Understand basic DevOps concepts
- Review testing fundamentals
- Explore cloud platforms (Vercel, Netlify)
- Learn about Docker basics

---

**Remember**: Good version control practices are essential for professional development. Commit often, commit smart! 🔀
