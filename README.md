# GitHub CodeQL Matrix Scan Demo (Python + JavaScript)

## Objective

In this lab you will learn how to:

- Create a GitHub repository
- Add Python and JavaScript code
- Configure GitHub CodeQL
- Use Matrix Strategy
- Configure Query Suite
- Configure Scheduled Scans (Cron)
- View Code Scanning Results

**Estimated Time:** 15–20 minutes

---

# Architecture

```text
Developer
    │
    ▼
Git Push
    │
    ▼
GitHub Actions
    │
    ▼
Matrix Strategy
    │
 ┌───────────────┐
 │               │
 ▼               ▼
Python      JavaScript
 │               │
 ▼               ▼
CodeQL       CodeQL
 │               │
 └───────┬───────┘
         ▼
Security Dashboard
```

---

# Prerequisites

- Git installed
- GitHub Account
- VS Code
- GitHub Repository
- GitHub Advanced Security enabled

---

# Step 1 Create Repository

Create a new repository.

```
codeql-two-language-demo
```

Clone it.

```bash
git clone https://github.com/<username>/codeql-two-language-demo.git

cd codeql-two-language-demo
```

---

# Step 2 Create Folder Structure

```
codeql-two-language-demo/

.github/
└── workflows/

python/

javascript/
```

---

# Step 3 Add Python File

Create

```
python/app.py
```

```python
name = input("Enter your name: ")

print("Hello " + name)
```

---

# Step 4 Add JavaScript File

Create

```
javascript/app.js
```

```javascript
const name = "GitHub";

console.log(`Hello ${name}`);
```

---

# Step 5 Create Workflow

Create

```
.github/workflows/codeql.yml
```

Paste the following workflow.

```yaml
name: CodeQL Matrix Scan

on:

  push:
    branches:
      - main

  pull_request:
    branches:
      - main

  schedule:

    # Every Monday at 02:00 UTC
    - cron: '0 2 * * 1'

permissions:

  actions: read
  contents: read
  security-events: write

jobs:

  analyze:

    name: Analyze ${{ matrix.language }}

    runs-on: ubuntu-latest

    strategy:

      fail-fast: false

      matrix:

        language:

          - python
          - javascript

    steps:

      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Initialize CodeQL

        uses: github/codeql-action/init@v3

        with:

          languages: ${{ matrix.language }}

          queries: security-and-quality

      - name: Autobuild

        uses: github/codeql-action/autobuild@v3

      - name: Analyze

        uses: github/codeql-action/analyze@v3
```

---

# Step 6 Understanding the Workflow

## Trigger Events

```yaml
on:

  push:

  pull_request:

  schedule:
```

The workflow runs when:

- Code is pushed
- Pull request is created
- Weekly scheduled scan executes

---

## Scheduled Scan

```yaml
schedule:

- cron: '0 2 * * 1'
```

Meaning:

```
Minute  Hour  Day  Month  Weekday

0       2      *     *       1
```

Runs:

- Every Monday
- 02:00 UTC

---

# Step 7 Matrix Strategy

```yaml
strategy:

  matrix:

    language:

      - python

      - javascript
```

GitHub automatically creates two jobs.

```
Analyze (python)

Analyze (javascript)
```

Both execute simultaneously.

---

# Step 8 Query Suite

```yaml
queries: security-and-quality
```

This enables:

- Security Queries
- Code Quality Queries
- Best Practices
- Reliability Checks

Common query suites:

| Query Suite | Description |
|-------------|-------------|
| security-and-quality | Security + quality checks |
| security-extended | Additional security rules |
| +./custom.qls | Custom query suite |

---

# Step 9 Push Code

```bash
git add .

git commit -m "Initial CodeQL demo"

git branch -M main

git remote add origin https://github.com/<username>/codeql-two-language-demo.git

git push -u origin main
```

---

# Step 10 Watch GitHub Actions

Open

```
Repository

↓

Actions
```

You should see

```
CodeQL Matrix Scan

Analyze (python)

Analyze (javascript)
```

---

# Step 11 Matrix Execution

```
             Matrix

        language

      ┌───────────────┐
      │               │
      ▼               ▼

   Python        JavaScript

      │               │

 Initialize     Initialize

      │               │

 Autobuild      Autobuild

      │               │

 Analyze        Analyze

      │               │

      └───────┬───────┘
              ▼

      Security Dashboard
```

---

# Step 12 View Code Scanning Results

Navigate to:

```
Repository

↓

Security

↓

Code Scanning
```

You will see:

- Active Alerts
- Fixed Alerts
- Severity
- CWE
- Rule
- Language

---

# Step 13 Workflow Summary

```
Push Code
     │
     ▼
GitHub Actions
     │
     ▼
Matrix Strategy
     │
 ┌───────────────┐
 │               │
 ▼               ▼
Python      JavaScript
 │               │
 ▼               ▼
Initialize CodeQL
 │               │
 ▼               ▼
Security & Quality Queries
 │               │
 ▼               ▼
Analyze Code
 │               │
 └───────┬───────┘
         ▼
Code Scanning Results
```

---

# Key Takeaways

- Matrix strategy runs multiple language scans in parallel.
- `security-and-quality` is a good default query suite for most repositories.
- Scheduled scans help identify vulnerabilities even when no new commits are pushed.
- Code scanning results are available under **Security → Code scanning**.
- This pattern scales easily by adding more languages to the matrix.

---

# Expected Outcome

After the workflow completes, you should observe:

- ✅ Two parallel analysis jobs (`python` and `javascript`)
- ✅ Weekly scheduled scan configured
- ✅ Security and quality query suite applied
- ✅ Code scanning results available in the Security tab
- ✅ A simple, production-style multi-language CodeQL workflow### codeql-two-language-demo

References:
https://docs.github.com/en
