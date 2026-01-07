# SECTIONS 4-6: Git Concepts, CI/CD Fundamentals & GitHub Actions

---

## SECTION 4 — Git Concepts Explained Simply

### 4.1 The Three Areas of Git

```
┌─────────────────────────────────────────────────────────────────┐
│                      GIT WORKFLOW AREAS                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────────┐                                            │
│  │ WORKING          │   Where you write and edit code            │
│  │ DIRECTORY        │   (Your actual files on disk)              │
│  └────────┬─────────┘                                            │
│           │                                                      │
│           │ git add                                              │
│           ▼                                                      │
│  ┌──────────────────┐                                            │
│  │ STAGING          │   Files marked for next commit             │
│  │ AREA             │   (Preparation zone)                       │
│  └────────┬─────────┘                                            │
│           │                                                      │
│           │ git commit                                           │
│           ▼                                                      │
│  ┌──────────────────┐                                            │
│  │ LOCAL            │   Committed history on your machine        │
│  │ REPOSITORY       │   (Permanent snapshots)                    │
│  └────────┬─────────┘                                            │
│           │                                                      │
│           │ git push                                             │
│           ▼                                                      │
│  ┌──────────────────┐                                            │
│  │ REMOTE           │   GitHub/GitLab cloud storage              │
│  │ REPOSITORY       │   (Shared with team)                       │
│  └──────────────────┘                                            │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 4.2 Working Directory

**What It Is:**
The folder on your computer where you edit files.

**Analogy:** Your desk where you're actively working on papers.

**Key Points:**
- Contains actual files you can see and edit
- Changes here are NOT saved to Git yet
- If you delete a file, it's gone (unless committed)

**Commands:**
```bash
# See what changed in working directory
git status

# Discard changes in working directory
git checkout -- filename.ts
```

### 4.3 Staging Area (Index)

**What It Is:**
A preparation zone for files you want to commit.

**Analogy:** An "Outbox" on your desk - papers you've reviewed and ready to file.

**Why It Exists:**
- Pick specific files to commit
- Review changes before committing
- Create focused commits

**Commands:**
```bash
# Add file to staging
git add filename.ts

# Add all files
git add .

# Remove from staging (keep changes in working directory)
git reset filename.ts

# See what's staged
git status
```

### 4.4 Commits

**What It Is:**
A permanent snapshot of staged changes with a message.

**Analogy:** Making a photocopy and filing it. The original paper can change, but the copy is permanent.

**Key Points:**
- Each commit has a unique ID (hash)
- Contains author, date, message, and changes
- You can always go back to any commit

**Commands:**
```bash
# Create a commit
git commit -m "Your message"

# See commit history
git log --oneline

# See details of a commit
git show <commit-hash>
```

**Example Output:**
```
a1b2c3d (HEAD -> feature/login) feat: add login tests
d4e5f6g fix: update locator
g7h8i9j refactor: simplify page object
```

### 4.5 Push vs Pull

**Visual Comparison:**

```
PUSH: Local → Remote (Upload)
┌──────────────────┐               ┌──────────────────┐
│ Your Computer    │  ──────────►  │ GitHub           │
│ (3 new commits)  │    git push   │ (gets updated)   │
└──────────────────┘               └──────────────────┘

PULL: Remote → Local (Download)
┌──────────────────┐               ┌──────────────────┐
│ GitHub           │  ──────────►  │ Your Computer    │
│ (5 new commits)  │    git pull   │ (gets updated)   │
└──────────────────┘               └──────────────────┘
```

| Aspect | Push | Pull |
|--------|------|------|
| Direction | Local to Remote | Remote to Local |
| Purpose | Share your work | Get team's work |
| When | After committing | Before starting work |
| Command | `git push origin branch` | `git pull origin branch` |

### 4.6 Fetch vs Pull

```
FETCH: Only downloads, doesn't merge
┌──────────────────┐               ┌──────────────────┐
│ GitHub           │  ──────────►  │ Your Computer    │
│                  │    git fetch  │ (downloads but   │
│                  │               │  doesn't merge)  │
└──────────────────┘               └──────────────────┘

PULL: Downloads AND merges
┌──────────────────┐               ┌──────────────────┐
│ GitHub           │  ──────────►  │ Your Computer    │
│                  │    git pull   │ (downloads AND   │
│                  │               │  merges)         │
└──────────────────┘               └──────────────────┘
```

| Aspect | Fetch | Pull |
|--------|-------|------|
| Downloads changes | ✅ Yes | ✅ Yes |
| Merges changes | ❌ No | ✅ Yes |
| Safe | Safer | Can cause conflicts |
| Use case | Review first | Get latest quickly |

**When to Use Fetch:**
```bash
git fetch origin
git log origin/develop    # See what's new
git merge origin/develop  # Then merge if happy
```

### 4.7 Merge vs Rebase

**Merge: Creates a merge commit**
```
Before merge:
main:    A───B───C
              \
feature:       D───E

After merge:
main:    A───B───C───M (merge commit)
              \     /
feature:       D───E
```

**Rebase: Moves commits to new base**
```
Before rebase:
main:    A───B───C
              \
feature:       D───E

After rebase:
main:    A───B───C
                  \
feature:           D'───E' (re-applied commits)
```

| Aspect | Merge | Rebase |
|--------|-------|--------|
| History | Preserves all branches | Linear history |
| Commit | Creates merge commit | No extra commits |
| Safety | Safer, non-destructive | Rewrites history |
| Team | Better for shared branches | Better for local branches |
| Command | `git merge branch` | `git rebase main` |

**Enterprise Recommendation:**
- Use **merge** for shared branches (develop, main)
- Use **rebase** for local feature branches before PR

### 4.8 Conflict Resolution

**What is a Conflict?**
When Git can't automatically merge because the same lines were changed.

**When Conflicts Happen:**
```
You:      Changed line 10 to "Hello"
Teammate: Changed line 10 to "Hi"
Git:      🤷 Which one to keep?
```

**Conflict Markers:**
```typescript
<<<<<<< HEAD
// Your version
const greeting = "Hello";
=======
// Their version
const greeting = "Hi";
>>>>>>> feature/other-branch
```

**How to Resolve:**

1. **Find conflicted files:**
```bash
git status    # Shows files with conflicts
```

2. **Open file and decide which version to keep:**
```typescript
// After resolution - pick one or combine
const greeting = "Hello World";
```

3. **Remove conflict markers:**
Delete the `<<<<<<<`, `=======`, and `>>>>>>>` lines.

4. **Stage and commit:**
```bash
git add filename.ts
git commit -m "fix: resolve merge conflict in greeting"
```

**Conflict Resolution Tips:**

| Tip | Description |
|-----|-------------|
| Communicate | Talk to teammate who made the change |
| Understand both | Know why each change was made |
| Test after | Run tests after resolving |
| Small commits | Smaller PRs = fewer conflicts |
| Pull often | Frequent pulls reduce conflicts |

### 4.9 Why Force Push is Dangerous

**What is Force Push?**
```bash
git push --force    # ⚠️ DANGEROUS
git push -f         # ⚠️ SAME THING
```

**Why It's Dangerous:**

```
BEFORE your force push:
Remote: A───B───C───D───E (teammate's work)
Your:   A───B───X───Y

AFTER your force push:
Remote: A───B───X───Y     (D and E are GONE!)
```

**When Force Push Destroys Work:**
1. Teammate pushed commits D and E
2. You didn't pull their changes
3. You force pushed your version
4. Their commits D and E are **permanently deleted**

**Safe Alternatives:**

| Instead of | Use | Why |
|------------|-----|-----|
| `git push --force` | `git push --force-with-lease` | Fails if remote has new commits |
| Force pushing to shared branch | Merge or rebase properly | Don't rewrite shared history |

**Enterprise Rule:**
> ⛔ NEVER force push to `main` or `develop` branches
> ⚠️ Only force push to your own feature branches when necessary

---

## SECTION 5 — CI/CD Fundamentals (Beginner Friendly)

### 5.1 What is CI/CD?

```
┌─────────────────────────────────────────────────────────────────┐
│                         CI/CD EXPLAINED                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  CI = Continuous Integration                                     │
│  ───────────────────────────                                     │
│  "Continuously combine everyone's code and test it together"    │
│                                                                  │
│  CD = Continuous Delivery/Deployment                             │
│  ──────────────────────────────────                              │
│  "Continuously prepare or release tested code"                   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Simple Analogy:**

| Concept | Analogy |
|---------|---------|
| CI | Restaurant kitchen automatically checking every dish before serving |
| CD | Automatically preparing dishes to be served when ready |

### 5.2 Why Enterprises Need CI/CD

**Without CI/CD:**
```
Developer A pushes code ──► Nobody tests it
Developer B pushes code ──► Nobody tests it
Developer C pushes code ──► Nobody tests it
                               │
                               ▼
           ONE MONTH LATER: Everything is broken 😱
```

**With CI/CD:**
```
Developer A pushes code ──► ✅ Tests run automatically
Developer B pushes code ──► ❌ Tests fail, gets notified
Developer C pushes code ──► ✅ Tests run automatically
                               │
                               ▼
                    CODE IS ALWAYS HEALTHY ✓
```

**Benefits for Enterprises:**

| Benefit | Description |
|---------|-------------|
| Early Detection | Find bugs immediately, not after a month |
| Consistent Testing | Same tests run every time, in same environment |
| Time Savings | No manual test execution needed |
| Quality Assurance | Code can't merge if tests fail |
| Team Visibility | Everyone sees test results |
| Audit Trail | Complete history of all test runs |

### 5.3 Local Execution vs CI Execution

**Local Execution:**
```
Your Computer
┌─────────────────────────────────────────┐
│  npm run test                           │
│      │                                  │
│      ▼                                  │
│  Tests run on YOUR machine              │
│      │                                  │
│      ▼                                  │
│  Results visible only to YOU            │
└─────────────────────────────────────────┘
```

**CI Execution:**
```
GitHub Cloud Server
┌─────────────────────────────────────────┐
│  Code pushed to GitHub                  │
│      │                                  │
│      ▼                                  │
│  CI server spins up fresh environment   │
│      │                                  │
│      ▼                                  │
│  Installs all dependencies              │
│      │                                  │
│      ▼                                  │
│  Runs all tests                         │
│      │                                  │
│      ▼                                  │
│  Reports results to ENTIRE TEAM         │
│      │                                  │
│      ▼                                  │
│  Stores reports as downloadable files   │
└─────────────────────────────────────────┘
```

**Key Differences:**

| Aspect | Local | CI |
|--------|-------|-----|
| Environment | Your machine | Fresh cloud server |
| Dependencies | Already installed | Installed fresh each time |
| Consistency | Varies by machine | Same every time |
| Visibility | Only you | Entire team |
| Reports | Local folder | Cloud artifacts |
| Speed | Faster (no setup) | Slower (includes setup) |

### 5.4 What Happens When Code is Pushed

```
┌─────────────────────────────────────────────────────────────────┐
│                  PUSH TRIGGER FLOW                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. Developer runs: git push                                     │
│         │                                                        │
│         ▼                                                        │
│  2. GitHub receives the code                                     │
│         │                                                        │
│         ▼                                                        │
│  3. GitHub checks: "Any workflow files match this trigger?"      │
│         │                                                        │
│     ┌───┴───┐                                                    │
│     │       │                                                    │
│    Yes     No ──► Nothing happens                                │
│     │                                                            │
│     ▼                                                            │
│  4. GitHub allocates a runner (cloud server)                     │
│         │                                                        │
│         ▼                                                        │
│  5. Runner executes workflow steps                               │
│         │                                                        │
│         ▼                                                        │
│  6. Results posted back to GitHub                                │
│         │                                                        │
│         ▼                                                        │
│  7. Status shown: ✅ Pass or ❌ Fail                              │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 5.5 What Happens When PR is Created

```
┌─────────────────────────────────────────────────────────────────┐
│                  PULL REQUEST TRIGGER FLOW                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. Developer creates Pull Request                               │
│         │                                                        │
│         ▼                                                        │
│  2. GitHub triggers CI pipeline                                  │
│         │                                                        │
│         ▼                                                        │
│  3. Tests run against the PR branch                              │
│         │                                                        │
│     ┌───┴───┐                                                    │
│     │       │                                                    │
│   Pass    Fail                                                   │
│     │       │                                                    │
│     ▼       ▼                                                    │
│  ✅ Green  ❌ Red check on PR                                    │
│  check       │                                                   │
│     │        └──► PR cannot be merged (if protected)             │
│     ▼                                                            │
│  4. Ready for code review                                        │
│         │                                                        │
│         ▼                                                        │
│  5. Reviewer approves                                            │
│         │                                                        │
│         ▼                                                        │
│  6. PR merged to target branch                                   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 5.6 What Happens When PR is Merged

```
┌─────────────────────────────────────────────────────────────────┐
│                  MERGE TRIGGER FLOW                              │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. PR is merged to develop                                      │
│         │                                                        │
│         ▼                                                        │
│  2. Push event triggered on develop branch                       │
│         │                                                        │
│         ▼                                                        │
│  3. Full test suite runs                                         │
│         │                                                        │
│         ▼                                                        │
│  4. Regression tests confirm nothing broke                       │
│         │                                                        │
│         ▼                                                        │
│  5. Reports generated and stored                                 │
│         │                                                        │
│         ▼                                                        │
│  6. Team notified of results                                     │
│         │                                                        │
│     ┌───┴───┐                                                    │
│     │       │                                                    │
│   Pass    Fail                                                   │
│     │       │                                                    │
│     ▼       ▼                                                    │
│  Ready    Investigation needed                                   │
│  for      Who broke it?                                          │
│  next     Fix immediately!                                       │
│  stage                                                           │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## SECTION 6 — GitHub Actions (Deep Dive)

### 6.1 What are GitHub Actions?

**Definition:**
GitHub Actions is a CI/CD platform built directly into GitHub that allows you to automate workflows.

**Simple Explanation:**
"GitHub Actions is like a robot assistant that watches your repository and automatically does tasks when certain events happen."

**Examples of Tasks:**
- Run tests when code is pushed
- Build and deploy application
- Send notifications
- Generate reports
- Label issues automatically

### 6.2 Workflow File Structure

**Location:**
```
your-repo/
└── .github/
    └── workflows/
        └── playwright.yml    ← Workflow file
```

**Basic Structure:**
```yaml
name: Workflow Name              # Display name

on:                              # Triggers
  push:
    branches: [main]

jobs:                            # What to do
  job-name:
    runs-on: ubuntu-latest
    steps:
      - name: Step 1
        run: echo "Hello"
```

### 6.3 Complete Workflow Anatomy

```yaml
# ════════════════════════════════════════════════════════════════
# NAME: What appears in GitHub Actions tab
# ════════════════════════════════════════════════════════════════
name: Playwright Tests

# ════════════════════════════════════════════════════════════════
# ON: When should this workflow run?
# ════════════════════════════════════════════════════════════════
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]
  workflow_dispatch:
  schedule:
    - cron: '0 0 * * *'

# ════════════════════════════════════════════════════════════════
# JOBS: What work should be done?
# ════════════════════════════════════════════════════════════════
jobs:
  test:
    name: Run Tests
    runs-on: ubuntu-latest
    timeout-minutes: 60
    
    # ══════════════════════════════════════════════════════════
    # STEPS: Individual tasks in sequence
    # ══════════════════════════════════════════════════════════
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
          
      - name: Install dependencies
        run: npm ci
        
      - name: Run tests
        run: npx playwright test
        env:
          BASE_URL: ${{ secrets.BASE_URL }}
          
      - name: Upload report
        uses: actions/upload-artifact@v4
        if: always()
        with:
          name: playwright-report
          path: playwright-report/
```

### 6.4 Jobs Explained

**What is a Job?**
A job is a set of steps that execute on the same runner (server).

```yaml
jobs:
  # Job 1: Run tests
  test:
    runs-on: ubuntu-latest
    steps:
      - run: npx playwright test
  
  # Job 2: Deploy (only if tests pass)
  deploy:
    needs: test              # Wait for 'test' job
    runs-on: ubuntu-latest
    steps:
      - run: npm run deploy
```

**Job Properties:**

| Property | Description | Example |
|----------|-------------|---------|
| `runs-on` | Operating system | `ubuntu-latest`, `windows-latest` |
| `timeout-minutes` | Max job duration | `60` |
| `needs` | Job dependencies | `needs: test` |
| `if` | Conditional execution | `if: success()` |
| `env` | Environment variables | `env: MY_VAR: value` |
| `strategy` | Matrix configuration | Browser matrix |

### 6.5 Steps Explained

**What is a Step?**
A single task within a job. Steps run in sequence.

**Two Types of Steps:**

```yaml
steps:
  # TYPE 1: Action (uses)
  - name: Checkout code
    uses: actions/checkout@v4
    
  # TYPE 2: Command (run)
  - name: Run tests
    run: npx playwright test
```

**Step Properties:**

| Property | Description | Example |
|----------|-------------|---------|
| `name` | Display name | `Install dependencies` |
| `uses` | Action to use | `actions/checkout@v4` |
| `run` | Command to execute | `npm install` |
| `with` | Action inputs | `node-version: 20` |
| `env` | Environment variables | `CI: true` |
| `if` | Conditional | `if: failure()` |
| `continue-on-error` | Don't fail job | `true` |

### 6.6 Runners Explained

**What is a Runner?**
A server (virtual machine) that executes your workflow.

**Types of Runners:**

| Type | Description | Cost |
|------|-------------|------|
| GitHub-hosted | Managed by GitHub | Free (limits apply) |
| Self-hosted | Your own servers | Your infrastructure |

**Available GitHub-Hosted Runners:**

| Runner | OS | Use Case |
|--------|-----|----------|
| `ubuntu-latest` | Linux | Most common, fastest |
| `windows-latest` | Windows | Windows-specific tests |
| `macos-latest` | macOS | Safari/iOS testing |

### 6.7 Secrets Explained

**What are Secrets?**
Encrypted environment variables for sensitive data.

**How to Create Secrets:**
1. Go to repository → Settings
2. Click Secrets and variables → Actions
3. Click "New repository secret"
4. Add name and value

**Using Secrets in Workflow:**

```yaml
steps:
  - name: Run tests
    run: npx playwright test
    env:
      TEST_USERNAME: ${{ secrets.TEST_USERNAME }}
      TEST_PASSWORD: ${{ secrets.TEST_PASSWORD }}
      API_KEY: ${{ secrets.API_KEY }}
```

**Secret Levels:**

| Level | Scope | Use Case |
|-------|-------|----------|
| Repository | Single repo | Repo-specific credentials |
| Organization | All repos in org | Shared credentials |
| Environment | Specific env | Dev vs Prod secrets |

### 6.8 Environment Variables

**Two Ways to Set:**

```yaml
# Level 1: Workflow level
env:
  NODE_ENV: test

jobs:
  test:
    # Level 2: Job level
    env:
      DEBUG: pw:api
      
    steps:
      - name: Run tests
        # Level 3: Step level
        env:
          HEADLESS: true
        run: npx playwright test
```

**Built-in Variables:**

| Variable | Description |
|----------|-------------|
| `${{ github.repository }}` | Owner/repo name |
| `${{ github.ref }}` | Branch/tag ref |
| `${{ github.sha }}` | Commit SHA |
| `${{ github.actor }}` | Username who triggered |
| `${{ github.run_id }}` | Unique run ID |

### 6.9 Triggers Deep Dive

#### Push Trigger
```yaml
on:
  push:
    branches:
      - main
      - develop
      - 'feature/**'        # Wildcard: all feature branches
    paths:
      - 'tests/**'          # Only if tests changed
      - '!docs/**'          # Ignore docs changes
```

#### Pull Request Trigger
```yaml
on:
  pull_request:
    branches:
      - main
    types:                  # Types of PR events
      - opened
      - synchronize         # New commits pushed
      - reopened
```

#### Schedule Trigger (Cron)
```yaml
on:
  schedule:
    - cron: '0 0 * * *'     # Daily at midnight UTC
    - cron: '0 9 * * 1'     # Every Monday at 9 AM UTC
```

**Cron Syntax:**
```
┌───────────── minute (0 - 59)
│ ┌───────────── hour (0 - 23)
│ │ ┌───────────── day of month (1 - 31)
│ │ │ ┌───────────── month (1 - 12)
│ │ │ │ ┌───────────── day of week (0 - 6, Sun-Sat)
│ │ │ │ │
* * * * *
```

| Schedule | Cron | Description |
|----------|------|-------------|
| Daily midnight | `0 0 * * *` | Every day at 00:00 UTC |
| Weekdays 9 AM | `0 9 * * 1-5` | Mon-Fri at 9 AM UTC |
| Hourly | `0 * * * *` | Every hour |
| Weekly Monday | `0 0 * * 1` | Every Monday midnight |

#### Manual Trigger (workflow_dispatch)
```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment to test'
        required: true
        default: 'staging'
        type: choice
        options:
          - development
          - staging
          - production
      browser:
        description: 'Browser to use'
        required: false
        default: 'chromium'
```

**Using Input Values:**
```yaml
steps:
  - name: Run tests
    run: npx playwright test --project=${{ inputs.browser }}
```

### 6.10 YAML Keywords Summary

| Keyword | Purpose | Example |
|---------|---------|---------|
| `name` | Display name | `name: CI Pipeline` |
| `on` | Triggers | `on: push` |
| `jobs` | Job definitions | `jobs: test:` |
| `runs-on` | Runner OS | `runs-on: ubuntu-latest` |
| `steps` | Job steps | `steps: - run: test` |
| `uses` | Use an action | `uses: actions/checkout@v4` |
| `run` | Run command | `run: npm test` |
| `with` | Action inputs | `with: node-version: 20` |
| `env` | Environment vars | `env: CI: true` |
| `if` | Conditional | `if: failure()` |
| `needs` | Job dependency | `needs: build` |
| `strategy` | Matrix/parallel | `strategy: matrix:` |
| `secrets` | Access secrets | `secrets.TOKEN` |

---

*Continue to Section 7-9...*
