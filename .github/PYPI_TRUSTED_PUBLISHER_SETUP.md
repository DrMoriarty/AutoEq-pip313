# PyPI Trusted Publisher Setup Guide

This document explains how to configure PyPI Trusted Publisher for automatic package publishing from GitHub Actions.

## What is PyPI Trusted Publisher?

PyPI Trusted Publisher uses OpenID Connect (OIDC) to authenticate GitHub Actions workflows without requiring API tokens or passwords. It's more secure and easier to manage.

## 🔒 Security: Master Branch Only

**IMPORTANT**: For security reasons, PyPI publishing is **only allowed from the `master` branch**.

- Tags from other branches will be **rejected** automatically
- The workflow verifies that tags are created from `master` before publishing
- This prevents accidental or malicious releases from feature branches

## Setup Instructions

### 1. PyPI Website Configuration

1. **Go to PyPI**: https://pypi.org/manage/account/publishing/
2. **Add a new publisher** with the following settings:

   ```
   PyPI Project Name: autoeq-py313
   Owner: 115dkk
   Repository name: AutoEq-pip313
   Workflow name: publish.yml
   Environment name: PyPI
   ```

3. Click **Add**

### 2. GitHub Environment Setup

1. Go to your repository settings: https://github.com/115dkk/AutoEq-pip313/settings/environments
2. Create a new environment named **`PyPI`**
3. **RECOMMENDED**: Add protection rules:
   - **Deployment branches**: Select "Protected branches only" and ensure `master` is protected
   - Required reviewers (optional)
   - Wait timer (optional)

### 3. Test Publishing (Optional)

Before publishing to PyPI, you can test with TestPyPI:

1. Go to TestPyPI: https://test.pypi.org/manage/account/publishing/
2. Add the same configuration with environment name **`TestPyPI`**
3. Run the workflow manually from Actions tab

## Publishing a New Release

### Method 1: Create a Git Tag from Master (Automatic - RECOMMENDED)

```bash
# 1. Make sure you're on master branch
git checkout master
git pull origin master

# 2. Tag the release (must be on master!)
git tag v1.3.0

# 3. Push the tag
git push origin v1.3.0

# 4. The workflow will:
#    - Verify the tag is from master branch
#    - Build the package
#    - Publish to PyPI automatically
```

### Method 2: Manual Trigger (TestPyPI only)

1. Go to Actions tab: https://github.com/115dkk/AutoEq-pip313/actions/workflows/publish.yml
2. Click **Run workflow**
3. Select the branch to build from
4. Click **Run workflow**

**Note**: Manual triggers publish to **TestPyPI only** (not production PyPI)

## Workflow Features

✅ **Master branch only**: Tags must be from `master` branch (verified automatically)
✅ **Automatic building**: Builds the package using Python 3.13 and Hatchling
✅ **Trusted Publishing**: No API tokens needed, uses OIDC authentication
✅ **Multiple environments**: Supports both PyPI and TestPyPI
✅ **Manual trigger**: Can be triggered manually for TestPyPI testing
✅ **Artifact upload**: Build artifacts are preserved for debugging
✅ **Branch verification**: Explicit check prevents publishing from wrong branches

## Configuration Summary

| Setting | Value |
|---------|-------|
| **Repository** | 115dkk/AutoEq-pip313 |
| **Workflow file** | `.github/workflows/publish.yml` |
| **PyPI Environment** | `PyPI` |
| **TestPyPI Environment** | `TestPyPI` |
| **Allowed branch** | `master` only |
| **Trigger** | Git tags matching `v*.*.*` from `master` |
| **Python version** | 3.13 |

## Troubleshooting

### Error: "Tag is not from master branch"

```
ERROR: Tag refs/tags/v1.3.0 is not from master branch!
PyPI publishing is only allowed from master branch for security.
```

**Solution**:
- Make sure you create and push tags from the `master` branch only
- Delete the tag and recreate it from `master`:
  ```bash
  git tag -d v1.3.0
  git push origin :refs/tags/v1.3.0
  git checkout master
  git pull origin master
  git tag v1.3.0
  git push origin v1.3.0
  ```

### Error: "Trusted publishing exchange failure"

- Verify the PyPI configuration matches exactly:
  - Owner: `115dkk`
  - Repository: `AutoEq-pip313`
  - Workflow: `publish.yml`
  - Environment: `PyPI`

### Error: "Environment protection rules"

- Check the GitHub environment settings
- Ensure you have the necessary permissions
- Verify deployment branch rules are correctly configured

### Build Failures

- Check the Actions tab for detailed logs
- Verify `pyproject.toml` is correctly configured
- Ensure all dependencies are available

## Security Benefits

🔒 **No long-lived tokens**: OIDC tokens are short-lived and scoped
🔒 **No secret management**: No API tokens to rotate or secure
🔒 **Master branch only**: Prevents releases from feature/development branches
🔒 **Branch verification**: Double-check that tags come from `master`
🔒 **Audit trail**: All publishes are logged in GitHub Actions
🔒 **Environment protection**: GitHub environments can require approvals

## Additional Resources

- [PyPI Trusted Publishers Guide](https://docs.pypi.org/trusted-publishers/)
- [GitHub Actions OIDC](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect)
- [pypa/gh-action-pypi-publish](https://github.com/pypa/gh-action-pypi-publish)
