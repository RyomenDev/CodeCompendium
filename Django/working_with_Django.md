#### Here’s how you can implement the backend in Django using Django REST Framework (DRF).

## 1. Install Required Dependencies

Run the following command to install Django, DRF, and necessary tools:

```bash
pip install django djangorestframework django-cors-headers pillow
```

## 2. Create Django Project & App

```bash
django-admin startproject backend
cd backend
django-admin startapp videos
```

## 3. Configure settings.py

Modify backend/settings.py to include necessary settings:

```python
INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    ...
]
```

```
MIDDLEWARE = [
    "django.middleware.security.SecurityMiddleware",
    "django.contrib.sessions.middleware.SessionMiddleware",
    "django.middleware.common.CommonMiddleware",
    "corsheaders.middleware.CorsMiddleware",  # Enable CORS
   ...
]
```

```
CORS_ALLOW_ALL_ORIGINS = True  # Allow all front-end requests
```

```
MEDIA_URL = "/media/"
MEDIA_ROOT = BASE_DIR / "media"
```

## 4. Define Video Model

In videos/models.py, define the model to store video file data:

```python
from django.db import models
...
...
```

**Run migrations:**

```bash
python manage.py makemigrations videos
python manage.py migrate
```

## 5. Create Serializers

In videos/serializers.py, create a serializer to handle API responses:

```python
from rest_framework import serializers
from .models import Video

class VideoSerializer(serializers.ModelSerializer):
class Meta:
model = Video
fields = "**all**"
```

## 6. Create API Views

In videos/views.py, define views for video upload and retrieval:

```python
from rest_framework import generics
from rest_framework.response import Response
from rest_framework import status

class VideoUploadView(generics.CreateAPIView):

    def post(self, request, *args, **kwargs):
        if ():
            return Response({"message": "...", "data": data}, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

class VideoListView(generics.ListAPIView):
...
...
```

## 7. Define URLs

In videos/urls.py:

```python
from django.urls import path
from .views import VideoUploadView, VideoListView

urlpatterns = [
path("upload/", VideoUploadView.as_view(), name=""),
path("videos/", VideoListView.as_view(), name=""),
]
```

**Include it in backend/urls.py:**

```python
from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
path("admin/", admin.site.urls),
path("api/", include("")),
]

if settings.DEBUG:
urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

## 8. Run the Server

```bash
python manage.py runserver
```

**Your API will now be accessible at:**

Upload video: POST http://localhost:8000/api/upload/
Get all videos: GET http://localhost:8000/api/videos/ 9. Frontend Example Request
**To send a request from your React frontend:**

```javascript
const formData = new FormData();

fetch("http://localhost:8000/api/upload/", {
  method: "POST",
  body: ...,
})
  .then((response) => response.json())
  .then((data) => console.log("Upload successful", data))
  .catch((error) => console.error("Error uploading video:", error));
```

Final Notes
You can extend it with AWS S3 or Cloudinary for remote storage.

## 🚀

````

```

```
````
