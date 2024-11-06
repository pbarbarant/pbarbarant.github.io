---
title: "Git Troubleshooting Guide"
date: 2024-11-06
lastmod: 2024-11-06
tags: ["Git","Github", "Programming"]
author: "Pierre-Louis Barbarant"
description: "This graduate course presents classical results in Romance philology." 
summary: "A short tutorial to solve your typical git problems." 
cover:
    image: "cover.png"
    relative: false
showToc: true
disableAnchoredHeadings: false

---

## Diagnostic Tools

### Git Status
Git status is your first line of defense when troubleshooting. It shows the current state of your working directory and staging area.

#### Command
```bash
git status
```

#### What It Shows
* Untracked files
* Changes staged for commit
* Changes not staged for commit
* Current branch information

#### Example Output
```bash
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   example.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        new-file.txt
```

### Git Diff
Git diff helps you review changes between different Git states.

#### Common Commands
* `git diff` - Shows unstaged changes
* `git diff --staged` - Shows staged changes
* `git diff <commit-hash>` - Compares working directory with a specific commit

#### Example Output
```bash
diff --git a/example.txt b/example.txt
index d3b073..8c3a9c 100644
--- a/example.txt
+++ b/example.txt
@@ -1 +1 @@
-Old line
+New line
```

---

## Common Issues and Solutions

### Recovering from Unwanted Local Commits

#### Scenario
You've committed changes locally that you don't want to push.

#### Solution Steps

1. **Unstage the commit but keep changes**
   ```bash
   git reset HEAD~1
   ```
   This removes the commit but preserves modifications in your working directory.

2. **Optional: Discard changes completely**
   ```bash
   git checkout -- <file-name>
   ```
   This returns the file to its state in the previous commit.

#### Example Workflow
```bash
# Initial unwanted commit
git commit -m "Added unwanted_file.txt"

# Undo the commit
git reset HEAD~1

# Optional: Remove changes
git checkout -- unwanted_file.txt
```

### Reverting Pushed Changes

#### Scenario 1: Safe Revert
When you need to undo changes that are already pushed.

```bash
# Find the commit to revert
git log --oneline
1234abc Commit to revert

# Create a reverting commit
git revert 1234abc
```

#### Scenario 2: Hard Reset (Use with Caution)
For cases where you need to completely remove recent commits.

```bash
# Find the safe commit
git log --oneline
7890def Newest commit (to remove)
4567abc Safe commit

# Reset and force push
git reset --hard 4567abc
git push origin main --force
```

⚠️ **Warning**: Force pushing rewrites history and can impact collaborators. Use only on private branches or after team coordination.

---

## Quick Reference

| Action | Command |
|--------|---------|
| Check status | `git status` |
| View unstaged changes | `git diff` |
| View staged changes | `git diff --staged` |
| Undo last commit (keep changes) | `git reset HEAD~1` |
| Discard file changes | `git checkout -- <file>` |
| Revert commit (safe) | `git revert <commit-hash>` |
| Reset to commit (destructive) | `git reset --hard <commit-hash>` |

## Best Practices

1. **Pre-Commit Checklist**
   * Run `git status` to verify changed files
   * Use `git diff` to review modifications
   * Commit related changes together

2. **Safe Collaboration**
   * Avoid force pushing to shared branches
   * Use feature branches for isolation
   * Make small, focused commits

3. **Regular Maintenance**
   * Keep commits atomic and well-described
   * Review changes before pushing
   * Communicate with team before destructive operations

4. **Emergency Procedures**
   * Always have a backup before major operations
   * Document steps taken during troubleshooting
   * Test recovery procedures on a separate branch first

---

## Working with .gitignore

### What is .gitignore?
A `.gitignore` file tells Git which files or directories to ignore in your project. This is useful for:
* Build artifacts and compiled code
* Dependencies and package directories
* Environment and configuration files
* System files
* IDE and editor specific files
* Log files and databases
* Personal configuration files

### Basic Syntax
```bash
# Single file
example.txt

# File pattern
*.log

# Directory
node_modules/
dist/

# Negation (don't ignore)
!important.log

# Match any depth
**/temp

# Match specific depth
foo/**/bar
```

### Common Patterns
```gitignore
# Dependencies
node_modules/
vendor/
.virtualenv/

# Build output
dist/
build/
*.o
*.exe

# Environment files
.env
.env.local
config.private.json

# IDE files
.idea/
.vscode/
*.sublime-project
*.sublime-workspace

# System files
.DS_Store
Thumbs.db

# Logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Database
*.sqlite
*.db

# Coverage and test
coverage/
.nyc_output/
```

### Some common issues

#### Fixing Already Tracked Files
If files are already tracked by Git:
```bash
# Remove from Git tracking but keep locally
git rm --cached filename

# For directories
git rm -r --cached directory/
```

#### File Still Being Tracked
```bash
# Clear Git cache
git rm -r --cached .

# Re-add all files
git add .

# Commit changes
git commit -m "Apply new .gitignore rules"
```

#### Template Examples
Language-specific templates available at:
* https://github.com/github/gitignore
* Use as starting points for your projects

---

## Additional Common Issues and Solutions

### Recovering Deleted Commits

If you ever accidentally delete a commit and want to recover it, you can use a combination of `git reflog`
and `git reset`.

#### Using reflog
```bash
# View reflog
git reflog

# Recover deleted commit
git reset --hard HEAD@{n}
```

### Stashing Changes

#### When to Use
* Need to switch branches with uncommitted changes
* Want to temporarily store changes without committing

#### Common Commands
```bash
# Stash current changes
git stash save "work in progress"

# List stashes
git stash list

# Apply most recent stash
git stash pop

# Apply specific stash
git stash apply stash@{n}

# Drop specific stash
git stash drop stash@{n}
```

### Merge Conflicts

#### What Are Merge Conflicts?
Merge conflicts occur when Git can't automatically resolve differences between two commits.

#### How to Identify
```bash
$ git merge feature-branch
Auto-merging file.txt
CONFLICT (content): Merge conflict in file.txt
Automatic merge failed; fix conflicts and then commit the result.
```

#### Resolving Conflicts
1. **Identify conflicted files**
   ```bash
   git status
   ```

2. **Open the conflicted files**
   Look for conflict markers:
   ```
   <<<<<<< HEAD
   Your changes
   =======
   Their changes
   >>>>>>> feature-branch
   ```

3. **Resolve the conflict**
   * Choose one version
   * Combine both versions
   * Write a completely new solution

4. **Mark as resolved**
   ```bash
   git add <resolved-file>
   git commit -m "Resolve merge conflict"
   ```

### Branch Management Issues

#### Renaming Branches
```bash
# Rename local branch
git branch -m old-name new-name

# Rename remote branch (delete old, push new)
git push origin --delete old-name
git push origin -u new-name
```

#### Deleting Branches
```bash
# Delete local branch
git branch -d branch-name

# Force delete local branch
git branch -D branch-name

# Delete remote branch
git push origin --delete branch-name
```

### Remote Repository Issues

#### Updating Remote URLs
```bash
# View current remote
git remote -v

# Change remote URL
git remote set-url origin new-url
```

#### Handling Failed Pushes
```bash
# Pull with rebase
git pull --rebase origin main

# Force push (use carefully!)
git push --force-with-lease origin branch-name
```

### Clean Up and Maintenance

#### Removing Untracked Files
```bash
# Preview what will be deleted
git clean -n

# Remove untracked files
git clean -f

# Remove untracked files and directories
git clean -fd
```

#### Cleaning Up Local Branches
```bash
# Remove stale remote-tracking branches
git remote prune origin

# List merged branches
git branch --merged

# List unmerged branches
git branch --no-merged
```

### Recovery Commands
```bash
# Recover deleted file
git checkout HEAD~1 -- path/to/deleted/file

# Recover deleted branch
git checkout -b recovered-branch commit-hash
```

---

## Best Practices (Additional)

1. **Commit Message Guidelines**
   * Use present tense ("Add feature" not "Added feature")
   * First line should be under 50 characters
   * Include why the change was made
   * Reference issue numbers if applicable

2. **Branch Management**
   * Delete branches after merging
   * Keep branch names descriptive and consistent
   * Use feature/, bugfix/, hotfix/ prefixes

3. **Collaboration**
   * Pull before pushing
   * Use --force-with-lease instead of --force
   * Tag important releases
   * Write meaningful commit messages

4. **Security**
   * Use .gitignore for sensitive files
   * Avoid committing credentials
   * Regularly audit large files
   * Review public repositories for sensitive data