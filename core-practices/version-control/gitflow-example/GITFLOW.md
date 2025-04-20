# GitFlow Branching Strategy

This document outlines the GitFlow branching strategy implementation for software projects.

## Branch Types

### Main Branches
- **main**: Production-ready code
- **develop**: Latest delivered development changes for the next release

### Supporting Branches
- **feature/***: New features development
- **release/***: Release preparation
- **hotfix/***: Urgent fixes for production
- **bugfix/***: Non-urgent bug fixes

## Workflow

### Feature Development
```
# Starting a new feature
git checkout develop
git pull
git checkout -b feature/my-feature

# Work on feature and commit changes
git add .
git commit -m "Add new functionality"

# Finishing a feature
git checkout develop
git pull
git merge --no-ff feature/my-feature
git push origin develop
git branch -d feature/my-feature
```

### Release Process
```
# Starting a release
git checkout develop
git pull
git checkout -b release/1.0.0

# Make release preparations (version bumps, final fixes)
git add .
git commit -m "Bump version to 1.0.0"

# Finishing a release
git checkout main
git pull
git merge --no-ff release/1.0.0
git tag -a v1.0.0 -m "Version 1.0.0"
git push origin main --tags

git checkout develop
git pull
git merge --no-ff release/1.0.0
git push origin develop

git branch -d release/1.0.0
```

### Hotfix Process
```
# Starting a hotfix
git checkout main
git pull
git checkout -b hotfix/critical-bug

# Fix the issue
git add .
git commit -m "Fix critical bug"

# Finishing a hotfix
git checkout main
git pull
git merge --no-ff hotfix/critical-bug
git tag -a v1.0.1 -m "Version 1.0.1"
git push origin main --tags

git checkout develop
git pull
git merge --no-ff hotfix/critical-bug
git push origin develop

git branch -d hotfix/critical-bug
```

## Visual Representation

```
    main    ----o--------o----o----o------------o------>
              /          /    /    \            \
develop -----o----------o----o      o------------o----->
            /          /      \      \            \
feature   -o----------o        \      \            \
                               \      \            \
release                         o------o            \
                                              \
hotfix                                          o----o
```

## When to Use GitFlow

GitFlow works best for:
- Projects with scheduled release cycles
- Teams that need to maintain multiple versions simultaneously
- Larger teams where parallel development is common

For simpler projects or continuous delivery models, consider trunk-based development instead.
