# GitFlow
# Gitflow Workflow Guide

## Overview

Gitflow is a branching model that organizes work into long-lived branches for stability and short-lived branches for changes. It's commonly used when you have scheduled releases and want clear separation between day-to-day development and production-ready code.

## Branches

### Long-lived branches

- **`main`** (or `master`): Production-ready code only.
- **`develop`**: Integration branch for the next release.

### Supporting (short-lived) branches

- **`feature/*`**: New features.
- **`release/*`**: Release preparation (stabilization).
- **`hotfix/*`**: Urgent production fixes.

## Branch Rules & Naming

### Feature branches

- **Branch from:** `develop`
- **Merge into:** `develop`
- **Naming:** `feature/<ticket-or-short-name>`
- **Example:** `feature/PROJ-123-add-login`

### Release branches

- **Branch from:** `develop`
- **Merge into:** `main` and `develop`
- **Naming:** `release/<version>`
- **Example:** `release/1.4.0`

### Hotfix branches

- **Branch from:** `main`
- **Merge into:** `main` and `develop` (or the active `release/*` branch)
- **Naming:** `hotfix/<version-or-ticket>`
- **Example:** `hotfix/1.4.1` or `hotfix/PROJ-456-fix-crash`

## Typical Workflows

### 1) Start a Feature

```bash
git checkout develop
git pull
git checkout -b feature/PROJ-123-add-login
```

Work on your feature, commit regularly:

```bash
git add .
git commit -m "feat: add login form component"
git push origin feature/PROJ-123-add-login
```

### 2) Finish a Feature (Merge Back)

```bash
git checkout develop
git pull
git merge --no-ff feature/PROJ-123-add-login
git branch -d feature/PROJ-123-add-login
git push origin develop
```

### 3) Cut a Release

```bash
git checkout develop
git pull
git checkout -b release/1.4.0
```

Stabilize on `release/1.4.0`:

- Bug fixes
- Version bump
- Release notes
- Final QA testing

### 4) Finish a Release

```bash
# Merge to main
git checkout main
git pull
git merge --no-ff release/1.4.0
git tag -a v1.4.0 -m "Release 1.4.0"

# Merge back to develop
git checkout develop
git pull
git merge --no-ff release/1.4.0

# Clean up
git branch -d release/1.4.0

# Push everything
git push origin main develop --tags
```

### 5) Hotfix Production

```bash
git checkout main
git pull
git checkout -b hotfix/1.4.1
```

Apply the fix, then:

```bash
# Merge to main
git checkout main
git merge --no-ff hotfix/1.4.1
git tag -a v1.4.1 -m "Hotfix 1.4.1"

# Merge to develop
git checkout develop
git merge --no-ff hotfix/1.4.1

# Clean up
git branch -d hotfix/1.4.1

# Push everything
git push origin main develop --tags
```

## Commit & PR Conventions (Recommended)

### Commit Messages

Keep commits small and focused. Use conventional commits if your team supports it.

**Examples:**

```
feat: add login endpoint
fix: handle null token
docs: update API examples
refactor: simplify auth middleware
test: add payment integration tests
chore: update dependencies
```

### Pull Requests

- Prefer Pull Requests (PRs) for merging into `develop` and `main`.
- Protect `main` with:
  - Required reviews
  - CI checks
  - Status checks
  - Restricted direct pushes

## Notes / Variations

- Some teams rename `develop` to `integration`.
- Some teams skip `develop` entirely and use trunk-based development.

### Gitflow works best for:

- Scheduled releases
- Larger teams
- Applications with release cycles

### A simplified workflow may be better for:

- Continuous deployment
- Fast-moving startups
- Small teams shipping multiple times per day

## Suggested Best Practices

### Branch Hygiene

- Rebase or sync feature branches regularly.
- Delete merged branches.
- Avoid long-running feature branches.

### CI/CD Integration

Automate:

- Tests
- Linting
- Build validation
- Deployment pipelines

### Versioning

Use semantic versioning: **MAJOR.MINOR.PATCH**

**Example:**

```
1.4.1
│ │ └── Patch fix
│ └──── Minor feature
└────── Major breaking change
```

## Example Repository Structure

```
main
 ├── hotfix/1.4.1
 └── release/1.4.0

develop
 ├── feature/PROJ-101-auth
 ├── feature/PROJ-102-dashboard
 └── feature/PROJ-103-payment
```

## Visual Workflow

```
main      ●─────────────────────●────────────●─────→
          │                     │ v1.0       │ v1.1
          │                     │            │
hotfix    │                     │      ●─────●
          │                     │      │ hotfix/1.0.1
          │                     │      │
release   │               ●─────●──────┘
          │               │ release/1.0
          │               │
develop   ●───────●───────●─────────────●───────────→
          │       │       │             │
feature   └───●───┘       └─────●───────┘
              │ feature/1       │ feature/2
```

## Quick Reference Commands

### Initialize Gitflow

```bash
# Create main branch
git checkout -b main
git push -u origin main

# Create develop branch
git checkout -b develop
git push -u origin develop
```

### Start Feature

```bash
git checkout develop
git pull
git checkout -b feature/STRIPE-001-payment-ui
```

### Finish Feature

```bash
git checkout develop
git pull
git merge --no-ff feature/STRIPE-001-payment-ui
git push origin develop
git branch -d feature/STRIPE-001-payment-ui
```

### Create Release

```bash
git checkout develop
git pull
git checkout -b release/1.0.0
# Update version numbers, test, fix bugs
git checkout main
git merge --no-ff release/1.0.0
git tag -a v1.0.0 -m "Release 1.0.0"
git checkout develop
git merge --no-ff release/1.0.0
git push origin main develop --tags
```

### Emergency Hotfix

```bash
git checkout main
git pull
git checkout -b hotfix/1.0.1
# Fix the bug
git checkout main
git merge --no-ff hotfix/1.0.1
git tag -a v1.0.1 -m "Hotfix 1.0.1"
git checkout develop
git merge --no-ff hotfix/1.0.1
git push origin main develop --tags
```

---

## Resources

- [Original Gitflow Article by Vincent Driessen](https://nvie.com/posts/a-successful-git-branching-model/)
- [Atlassian Gitflow Tutorial](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)
- [Git Flow Cheatsheet](https://danielkummer.github.io/git-flow-cheatsheet/)
