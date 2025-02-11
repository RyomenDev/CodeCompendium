# To Enable Cors in Django

## 1️⃣ Install django-cors-headers

```
pdm add django-cors-headers
```

## Update settings.py

Edit backend/settings.py to include necessary configurations:

### 2️⃣ Add 'corsheaders' to INSTALLED_APPS

```python
INSTALLED_APPS = [
   ...
    "corsheaders",
   ...
]
```

### 3️⃣ Add 'corsheaders.middleware.CorsMiddleware' to MIDDLEWARE

```
MIDDLEWARE = [
    ...
    "corsheaders.middleware.CorsMiddleware",  # Enable CORS
    ...
]

```

### 4️⃣ Configure CORS Settings

**🔹 Allow All Origins (Not Recommended for Production)**

```
CORS_ALLOW_ALL_ORIGINS = True  # Allow all frontend requests
```

**🔹 Allow Specific Origins (Recommended)**

```python
CORS_ALLOWED_ORIGINS = [
    "http://localhost:3000",  # React/Vite Frontend
    "https://yourdomain.com",  # Your production domain
]
```

**🔹 Allow Specific Origins with Regex**

```python
CORS_ALLOWED_ORIGIN_REGEXES = [
    r"^https://.*\.yourdomain\.com$",
]
```

**🔹 Allow Credentials (For Cookies & Auth Headers)**
If your frontend needs to send credentials (e.g., authentication tokens, cookies), add:

```python
CORS_ALLOW_CREDENTIALS = True
```

**🔹 Allow Specific Headers**

```python
CORS_ALLOW_HEADERS = [
    "authorization",
    "content-type",
    "x-csrf-token",
    "accept",
    "origin",
    "user-agent",
    "x-requested-with",
]
```

## 5️⃣ Restart the Django Server

Run:

```sh
python manage.py runserver
```

Django backend should now support CORS requests! 🚀 😃
