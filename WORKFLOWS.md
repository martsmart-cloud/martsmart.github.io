# GitHub Actions Workflow Cleanup Guide

## Current Status
Your repository has **28 workflow files** with mostly failing workflows. Many are template workflows that don't apply to a trading strategy project.

## ✅ Workflows to KEEP

### 1. **codeql.yml** - CodeQL Security Analysis
- **Status**: ✅ Passing
- **Purpose**: Automatic security scanning
- **Action**: Keep and update language matrix to Python only
- **Update**:
  ```yaml
  strategy:
    matrix:
      include:
      - language: python
        build-mode: none
  ```

### 2. **blank.yml** (CI) - Basic CI
- **Status**: ✅ Passing  
- **Purpose**: Can be renamed to trading-ci.yml
- **Action**: Keep but update to test Python strategies

### 3. **static.yml** - GitHub Pages Deployment
- **Status**: ⚠️ Needs setup
- **Purpose**: Deploy documentation
- **Action**: Keep for future documentation site

### 4. **greetings.yml** - New Contributor Greeter
- **Status**: ✅ Passing
- **Purpose**: Welcome new contributors
- **Action**: Keep as-is

### 5. **stale.yml** - Stale Issue Closer
- **Status**: ✅ Passing
- **Purpose**: Auto-close stale issues
- **Action**: Keep as-is

## ❌ Workflows to DELETE

Delete these irrelevant template workflows by removing the files:

```bash
# Mobile/App Development (Not applicable)
.github/workflows/android.yml
.github/workflows/ios.yml
.github/workflows/appknox.yml

# Build Systems (Not needed for Python)
.github/workflows/gradle.yml
.github/workflows/cmake-multi-platform.yml
.github/workflows/webpack.yml

# Languages Not Used
.github/workflows/c-cpp.yml
.github/workflows/node.js.yml
.github/workflows/npm-publish.yml

# Deployment Platforms (Not applicable)
.github/workflows/azure-webapps-node.yml
.github/workflows/openshift.yml
.github/workflows/docker-image.yml

# Build Tools
.github/workflows/jekyll.yml
.github/workflows/jekyll-gh-pages.yml

# Security/Scanning Tools (Too specific)
.github/workflows/jscrambler-code-integrity.yml
.github/workflows/datadog-synthetics.yml
.github/workflows/pylint.yml
.github/workflows/python-package.yml  # Will replace with better version

# Miscellaneous
.github/workflows/manual.yml
.github/workflows/summary.yml
.github/workflows/lock
```

## 📋 Recommended New Workflows

### 1. **trading-ci.yml** - Main Testing Workflow
```yaml
name: Trading Strategy CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.9", "3.10", "3.11", "3.12"]
    
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v4
        with:
          python-version: ${{ matrix.python-version }}
      - run: |
          pip install --upgrade pip
          pip install -r requirements.txt
          pip install pytest pytest-cov flake8
          flake8 strategies/ tests/ --max-line-length=120
          pytest tests/ --cov=strategies/
```

### 2. **backtest.yml** - Strategy Backtesting
```yaml
name: Strategy Backtest

on:
  push:
    paths:
      - 'strategies/**'
  pull_request:
    paths:
      - 'strategies/**'

jobs:
  backtest:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v4
        with:
          python-version: "3.11"
      - run: |
          pip install -r requirements.txt
          python -m strategies.backtest
```

## 🔧 How to Clean Up

### Option 1: Via GitHub UI
1. Go to: https://github.com/martsmart-cloud/martsmart.github.io/tree/main/.github/workflows
2. Open each unwanted workflow
3. Click "Delete" (if you have permission)

### Option 2: Via Git CLI
```bash
git clone https://github.com/martsmart-cloud/martsmart.github.io.git
cd martsmart.github.io

# Delete unwanted workflows
git rm .github/workflows/android.yml
git rm .github/workflows/ios.yml
git rm .github/workflows/gradle.yml
# ... repeat for all files listed above

# Commit and push
git commit -m "Clean up irrelevant workflow templates"
git push origin main
```

### Option 3: Via GitHub Web & Git
1. Edit each workflow directly on GitHub
2. Replace content with minimal placeholder
3. Or let them continue to fail until you're ready to delete

## 📊 Expected Outcome

After cleanup:
- **From**: 28 workflows (23 failing, 5 passing)
- **To**: 5-7 workflows (all passing)
- **Running time**: Significantly faster
- **Cost**: Lower GitHub Actions minutes usage

## Next Steps

1. **Delete** unnecessary workflows
2. **Create** trading-ci.yml for your strategies
3. **Setup** requirements.txt in repository root
4. **Add** tests/ directory with sample tests
5. **Monitor** Actions tab for successful runs

---

**Reference**: [GitHub Actions Docs](https://docs.github.com/en/actions)
