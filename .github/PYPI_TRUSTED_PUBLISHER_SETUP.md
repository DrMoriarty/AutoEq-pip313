# PyPI Trusted Publisher Setup Guide

This document explains how to configure PyPI Trusted Publisher for automatic package publishing from GitHub Actions.

## What is PyPI Trusted Publisher?

PyPI Trusted Publisher uses OpenID Connect (OIDC) to authenticate GitHub Actions workflows without requiring API tokens or passwords. It's more secure and easier to manage.

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
3. (Optional) Add protection rules:
   - Required reviewers
   - Wait timer
   - Deployment branches: only tags matching `v*.*.*`

### 3. Test Publishing (Optional)

Before publishing to PyPI, you can test with TestPyPI:

1. Go to TestPyPI: https://test.pypi.org/manage/account/publishing/
2. Add the same configuration with environment name **`TestPyPI`**
3. Run the workflow manually from Actions tab

## Publishing a New Release

### Method 1: Create a Git Tag (Automatic)

```bash
# Tag the release
git tag v1.3.0
git push origin v1.3.0

# The workflow will automatically trigger and publish to PyPI
```

### Method 2: Manual Trigger

1. Go to Actions tab: https://github.com/115dkk/AutoEq-pip313/actions/workflows/publish.yml
2. Click **Run workflow**
3. Select the branch/tag to publish
4. Click **Run workflow**

This will publish to TestPyPI (for testing)

## Workflow Features

✅ **Automatic building**: Builds the package using Python 3.13 and Hatchling
✅ **Trusted Publishing**: No API tokens needed, uses OIDC authentication
✅ **Multiple environments**: Supports both PyPI and TestPyPI
✅ **Manual trigger**: Can be triggered manually for testing
✅ **Artifact upload**: Build artifacts are preserved for debugging

## Configuration Summary

| Setting | Value |
|---------|-------|
| **Repository** | 115dkk/AutoEq-pip313 |
| **Workflow file** | `.github/workflows/publish.yml` |
| **PyPI Environment** | `PyPI` |
| **TestPyPI Environment** | `TestPyPI` |
| **Trigger** | Git tags matching `v*.*.*` |
| **Python version** | 3.13 |

## Troubleshooting

### Error: "Trusted publishing exchange failure"

- Verify the PyPI configuration matches exactly:
  - Owner: `115dkk`
  - Repository: `AutoEq-pip313`
  - Workflow: `publish.yml`
  - Environment: `PyPI`

### Error: "Environment protection rules"

- Check the GitHub environment settings
- Ensure you have the necessary permissions

### Build Failures

- Check the Actions tab for detailed logs
- Verify `pyproject.toml` is correctly configured
- Ensure all dependencies are available

## Security Benefits

🔒 **No long-lived tokens**: OIDC tokens are short-lived and scoped
🔒 **No secret management**: No API tokens to rotate or secure
🔒 **Audit trail**: All publishes are logged in GitHub Actions
🔒 **Environment protection**: GitHub environments can require approvals

## Additional Resources

- [PyPI Trusted Publishers Guide](https://docs.pypi.org/trusted-publishers/)
- [GitHub Actions OIDC](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect)
- [pypa/gh-action-pypi-publish](https://github.com/pypa/gh-action-pypi-publish)
