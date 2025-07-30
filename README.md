# 🛠️ Scalable Django + Frontend Project Setup

This guide helps you set up a **decoupled Django REST backend** and a **separate frontend** directory using **vanilla JavaScript**, allowing for clean team collaboration.

---

## 📁 Project Structure

```
project_name/
├── backend/         ← Django project and API app
├── frontend/        ← HTML/CSS/JS static frontend
└── .venv/           ← Python virtual environment
```

---

## 🔢 Step 1: Set Up the Project Root

```bash
mkdir project_name
cd project_name
```

---

## 🔢 Step 2: Create a Virtual Environment

```bash
python -m venv .venv

# For Linux/macOS:
source .venv/bin/activate

# For Windows CMD:
.venv\Scripts\activate
```

---

## 🔢 Step 3: Install Required Packages

```bash
pip install djangorestframework django-cors-headers
pip freeze > requirements.txt
```

> 📝 `django-cors-headers` allows your frontend and backend to communicate when served from different origins.

---

## 🔢 Step 4: Create the Frontend and Backend Structure

```bash
mkdir frontend
django-admin startproject backend
cd backend
python manage.py startapp api
```

---

## 🔢 Step 5: Configure Django Settings

Add the following to `backend/settings.py`:

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
    'corsheaders',
    'api',
]

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',
    ...
]

CORS_ALLOW_ALL_ORIGINS = True  # For development only
```

> 🔐 In production, use:
> ```python
> CORS_ALLOWED_ORIGINS = ["https://your-frontend-domain.com"]
> ```

---

## 🔢 Step 6: Create a Sample API

### 📄 `backend/api/views.py`

```python
from rest_framework.response import Response
from rest_framework.decorators import api_view

@api_view(['GET'])
def hello_world(request):
    return Response({"message": "Hello from Django API!"})
```

### 📄 `backend/api/urls.py`

```python
from django.urls import path
from .views import hello_world

urlpatterns = [
    path('hello/', hello_world),
]
```

### 📄 `backend/urls.py`

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('api.urls')),
]
```

---

## 🔢 Step 7: Run the Backend Server

```bash
python manage.py runserver
```

If you see a message about unapplied migrations, run:

```bash
python manage.py migrate
python manage.py runserver
```

Visit: [http://127.0.0.1:8000/api/hello/](http://127.0.0.1:8000/api/hello/)

✅ You should see something like:

> `{ "message": "Hello from Django API!" }`  
> *(Image 1: backend working properly)*

---

## 🔢 Step 8: Set Up the Frontend

### 📄 `frontend/index.html`

```html
<!DOCTYPE html>
<html>
<head>
  <title>Frontend</title>
</head>
<body>
  <h1 id="message">Loading...</h1>
  <script src="main.js"></script>
</body>
</html>
```

### 📄 `frontend/main.js`

```javascript
fetch("http://127.0.0.1:8000/api/hello/")
  .then(res => res.json())
  .then(data => {
    document.getElementById("message").textContent = data.message;
  })
  .catch(err => {
    document.getElementById("message").textContent = "Error connecting to backend";
    console.error(err);
  });
```

### ▶️ Serve the Frontend Locally

```bash
cd frontend
python -m http.server 5173
```

Visit: [http://localhost:5173](http://localhost:5173)  
✅ You should see your message fetched from Django’s API.

> *(Image 2: frontend working properly)*

---

## ✅ Final Result

- 🟢 Frontend running at: [http://localhost:5173](http://localhost:5173)  
- 🟢 Backend API running at: [http://127.0.0.1:8000/api/hello/](http://127.0.0.1:8000/api/hello/)
- 🔄 Fully decoupled, modern architecture
- 👥 Easy for teams to collaborate without Django knowledge
