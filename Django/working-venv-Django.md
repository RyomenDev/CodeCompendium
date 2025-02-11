### Setting Up Django Backend with PDM & Virtual Environment

To work with PDM in a virtual environment, let's go step by step:

## 1. Install PDM (If Not Installed)

PDM is a modern Python package manager. You can install it via:

```bash
pip install pdm
or using Homebrew (for macOS/Linux):
```

```bash
brew install pdm
```

Verify installation:

```bash
pdm --version
```

## 2. Create a New Project with PDM

Navigate to your workspace and create a Django project inside a PDM-managed virtual environment:

```bash
pdm init
```

It will ask for metadata (e.g., project name, version).
Choose Python interpreter inside venv when prompted.
Once initialized, activate the environment:

```bash
pdm venv create
pdm venv activate
```

## 3. Install Django & Required Packages

```bash
pdm add django djangorestframework django-cors-headers
```

This will install:

Django (Core framework)
Django REST Framework (DRF) (For APIs)
CORS Headers (For frontend interaction)

```bash
pdm run django-admin startproject backend
cd backend
pdm run django-admin startapp appName
```
