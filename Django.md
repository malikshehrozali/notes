# 🦄 Django Complete Notes — Everything You Need

> A complete reference guide covering every concept, pattern, and feature of Django with code examples and explanations. Built for real-world use.

---

## Table of Contents

1. [What is Django?](#1-what-is-django)
2. [Installation & Project Setup](#2-installation--project-setup)
3. [Project Structure](#3-project-structure)
4. [Settings & Configuration](#4-settings--configuration)
5. [URLs & Routing](#5-urls--routing)
6. [Views — Function Based (FBV)](#6-views--function-based-fbv)
7. [Views — Class Based (CBV)](#7-views--class-based-cbv)
8. [Models & ORM](#8-models--orm)
9. [Migrations](#9-migrations)
10. [Django Admin](#10-django-admin)
11. [Forms](#11-forms)
12. [Templates](#12-templates)
13. [Static & Media Files](#13-static--media-files)
14. [Authentication System](#14-authentication-system)
15. [Middleware](#15-middleware)
16. [Signals](#16-signals)
17. [Django REST Framework (DRF)](#17-django-rest-framework-drf)
18. [DRF — Serializers](#18-drf--serializers)
19. [DRF — Views & ViewSets](#19-drf--views--viewsets)
20. [DRF — Routers](#20-drf--routers)
21. [DRF — Authentication & Permissions](#21-drf--authentication--permissions)
22. [DRF — Filtering, Searching & Ordering](#22-drf--filtering-searching--ordering)
23. [DRF — Pagination](#23-drf--pagination)
24. [DRF — Throttling (Rate Limiting)](#24-drf--throttling-rate-limiting)
25. [JWT Authentication (SimpleJWT)](#25-jwt-authentication-simplejwt)
26. [Custom User Model](#26-custom-user-model)
27. [Email](#27-email)
28. [Caching](#28-caching)
29. [Celery & Task Queues](#29-celery--task-queues)
30. [Django Channels (WebSockets)](#30-django-channels-websockets)
31. [File Uploads](#31-file-uploads)
32. [Testing](#32-testing)
33. [Management Commands](#33-management-commands)
34. [Security Best Practices](#34-security-best-practices)
35. [Deployment](#35-deployment)
36. [Common Patterns & Tips](#36-common-patterns--tips)

---

## 1. What is Django?

Django is a **high-level, batteries-included Python web framework** that encourages rapid development and clean, pragmatic design. It follows the **MTV (Model-Template-View)** pattern — similar to MVC.

### Why Django?

- **Batteries included** — ORM, Admin, Auth, Forms, Templates all built-in
- **Security first** — CSRF, XSS, SQL injection protection out of the box
- **Scalable** — used by Instagram, Pinterest, Disqus, Mozilla
- **DRY principle** — Don't Repeat Yourself
- **Huge ecosystem** — thousands of third-party packages

### Django vs Flask vs FastAPI

| Feature | Django | Flask | FastAPI |
|---------|--------|-------|---------|
| Type | Full-stack | Micro | Async micro |
| ORM | Built-in | External | External |
| Admin Panel | Built-in | External | External |
| Auth | Built-in | External | External |
| Speed | Fast | Fast | Very fast |
| Learning curve | Steeper | Gentle | Medium |
| Best for | Full apps, CMS, APIs | Small APIs | High-perf APIs |

### MTV Pattern

```
Request → URLs (urls.py)
              ↓
           View (views.py)   ←→   Model (models.py) ←→ Database
              ↓
         Template (.html)
              ↓
           Response
```

---

## 2. Installation & Project Setup

### Create a virtual environment

```bash
python -m venv venv

# Activate
source venv/bin/activate       # Linux/Mac
venv\Scripts\activate          # Windows
```

### Install Django

```bash
pip install django

# Verify
python -m django --version
```

### Create a project

```bash
# Creates a project folder
django-admin startproject myproject

# Or create in current directory (recommended)
django-admin startproject myproject .
```

### Create an app

```bash
# An app is a module inside your project (users, posts, products, etc.)
python manage.py startapp users
python manage.py startapp posts
python manage.py startapp products
```

### Run the development server

```bash
python manage.py runserver              # Default: localhost:8000
python manage.py runserver 0.0.0.0:8000 # Accessible from network
python manage.py runserver 5000         # Custom port
```

### Essential manage.py commands

```bash
python manage.py runserver          # Start dev server
python manage.py makemigrations     # Create migration files
python manage.py migrate            # Apply migrations to DB
python manage.py createsuperuser    # Create admin user
python manage.py shell              # Interactive Python shell
python manage.py shell_plus         # Enhanced shell (django-extensions)
python manage.py collectstatic      # Collect static files for production
python manage.py test               # Run tests
python manage.py check              # Check for project issues
python manage.py showmigrations     # Show all migrations and status
python manage.py sqlmigrate app 0001  # Show SQL for a migration
python manage.py dbshell            # Open database shell
python manage.py flush              # Delete all data in DB
python manage.py loaddata file.json # Load fixture data
python manage.py dumpdata app.Model # Export data as JSON
```

### Install common packages

```bash
pip install django djangorestframework django-environ \
            djangorestframework-simplejwt django-cors-headers \
            django-filter pillow celery redis django-redis \
            django-extensions psycopg2-binary gunicorn \
            whitenoise dj-database-url python-decouple

pip freeze > requirements.txt
pip install -r requirements.txt
```

---

## 3. Project Structure

### After `django-admin startproject myproject .`

```
myproject/
├── myproject/
│   ├── __init__.py
│   ├── settings.py          ← All configuration
│   ├── urls.py              ← Root URL config
│   ├── wsgi.py              ← WSGI entry point (Gunicorn)
│   └── asgi.py              ← ASGI entry point (Daphne/Channels)
├── manage.py                ← Management utility
└── requirements.txt
```

### After adding apps (recommended structure)

```
myproject/
├── config/                      ← Rename myproject/ to config/
│   ├── __init__.py
│   ├── settings/
│   │   ├── __init__.py
│   │   ├── base.py              ← Base settings
│   │   ├── development.py       ← Dev settings
│   │   └── production.py        ← Prod settings
│   ├── urls.py
│   ├── wsgi.py
│   └── asgi.py
├── apps/
│   ├── users/
│   │   ├── __init__.py
│   │   ├── admin.py
│   │   ├── apps.py
│   │   ├── models.py
│   │   ├── serializers.py
│   │   ├── views.py
│   │   ├── urls.py
│   │   ├── permissions.py
│   │   ├── signals.py
│   │   ├── tasks.py
│   │   ├── tests/
│   │   │   ├── __init__.py
│   │   │   ├── test_models.py
│   │   │   └── test_views.py
│   │   └── migrations/
│   ├── posts/
│   │   └── ...
│   └── products/
│       └── ...
├── templates/
│   ├── base.html
│   └── users/
│       └── profile.html
├── static/
│   ├── css/
│   ├── js/
│   └── images/
├── media/                       ← User-uploaded files
├── manage.py
├── requirements.txt
└── .env
```

---

## 4. Settings & Configuration

### `config/settings/base.py`

```python
import os
from pathlib import Path
from decouple import config, Csv  # pip install python-decouple

BASE_DIR = Path(__file__).resolve().parent.parent.parent

# Security
SECRET_KEY = config('SECRET_KEY')
DEBUG = config('DEBUG', default=False, cast=bool)
ALLOWED_HOSTS = config('ALLOWED_HOSTS', default='localhost', cast=Csv())

# Apps
DJANGO_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]

THIRD_PARTY_APPS = [
    'rest_framework',
    'rest_framework_simplejwt',
    'corsheaders',
    'django_filters',
    'django_extensions',
]

LOCAL_APPS = [
    'apps.users',
    'apps.posts',
    'apps.products',
]

INSTALLED_APPS = DJANGO_APPS + THIRD_PARTY_APPS + LOCAL_APPS

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',     # Serve static files
    'corsheaders.middleware.CorsMiddleware',          # CORS — must be high up
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

ROOT_URLCONF = 'config.urls'

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

WSGI_APPLICATION = 'config.wsgi.application'
ASGI_APPLICATION = 'config.asgi.application'

# Database
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': config('DB_NAME'),
        'USER': config('DB_USER'),
        'PASSWORD': config('DB_PASSWORD'),
        'HOST': config('DB_HOST', default='localhost'),
        'PORT': config('DB_PORT', default='5432'),
    }
}

# Password validators
AUTH_PASSWORD_VALIDATORS = [
    {'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator'},
    {'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator'},
    {'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator'},
    {'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator'},
]

# Custom user model
AUTH_USER_MODEL = 'users.User'

# Internationalization
LANGUAGE_CODE = 'en-us'
TIME_ZONE = 'UTC'
USE_I18N = True
USE_TZ = True

# Static files
STATIC_URL = '/static/'
STATIC_ROOT = BASE_DIR / 'staticfiles'
STATICFILES_DIRS = [BASE_DIR / 'static']
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

# Media files (user uploads)
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'

# Default primary key
DEFAULT_AUTO_FIELD = 'django.db.models.BigAutoField'

# Email
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = config('EMAIL_HOST', default='smtp.gmail.com')
EMAIL_PORT = config('EMAIL_PORT', default=587, cast=int)
EMAIL_USE_TLS = True
EMAIL_HOST_USER = config('EMAIL_HOST_USER', default='')
EMAIL_HOST_PASSWORD = config('EMAIL_HOST_PASSWORD', default='')
DEFAULT_FROM_EMAIL = config('DEFAULT_FROM_EMAIL', default='noreply@myapp.com')

# CORS
CORS_ALLOWED_ORIGINS = config('CORS_ALLOWED_ORIGINS', default='http://localhost:3000', cast=Csv())
CORS_ALLOW_CREDENTIALS = True

# Redis & Cache
CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': config('REDIS_URL', default='redis://127.0.0.1:6379/1'),
        'OPTIONS': {'CLIENT_CLASS': 'django_redis.client.DefaultClient'},
    }
}

# Session
SESSION_ENGINE = 'django.contrib.sessions.backends.cache'
SESSION_CACHE_ALIAS = 'default'

# Celery
CELERY_BROKER_URL = config('REDIS_URL', default='redis://localhost:6379/0')
CELERY_RESULT_BACKEND = config('REDIS_URL', default='redis://localhost:6379/0')
CELERY_TASK_SERIALIZER = 'json'
CELERY_RESULT_SERIALIZER = 'json'
CELERY_ACCEPT_CONTENT = ['json']
CELERY_TIMEZONE = 'UTC'

# DRF
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ],
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 20,
    'DEFAULT_FILTER_BACKENDS': [
        'django_filters.rest_framework.DjangoFilterBackend',
        'rest_framework.filters.SearchFilter',
        'rest_framework.filters.OrderingFilter',
    ],
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle',
    ],
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/day',
        'user': '1000/day',
    },
}

# File upload size limit (10 MB)
DATA_UPLOAD_MAX_MEMORY_SIZE = 10 * 1024 * 1024
FILE_UPLOAD_MAX_MEMORY_SIZE = 10 * 1024 * 1024

# Logging
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'verbose': {
            'format': '{levelname} {asctime} {module} {process:d} {thread:d} {message}',
            'style': '{',
        },
    },
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
            'formatter': 'verbose',
        },
        'file': {
            'class': 'logging.FileHandler',
            'filename': BASE_DIR / 'logs/django.log',
            'formatter': 'verbose',
        },
    },
    'root': {
        'handlers': ['console'],
        'level': 'WARNING',
    },
    'loggers': {
        'django': {
            'handlers': ['console', 'file'],
            'level': 'INFO',
            'propagate': False,
        },
    },
}
```

### `config/settings/development.py`

```python
from .base import *

DEBUG = True
ALLOWED_HOSTS = ['*']

# Show emails in console during development
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'

# SQLite for quick local dev
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}

# Django Debug Toolbar (pip install django-debug-toolbar)
INSTALLED_APPS += ['debug_toolbar']
MIDDLEWARE += ['debug_toolbar.middleware.DebugToolbarMiddleware']
INTERNAL_IPS = ['127.0.0.1']
```

### `config/settings/production.py`

```python
from .base import *

DEBUG = False

# Security headers
SECURE_HSTS_SECONDS = 31536000
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True
SECURE_SSL_REDIRECT = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
SECURE_BROWSER_XSS_FILTER = True
SECURE_CONTENT_TYPE_NOSNIFF = True
X_FRAME_OPTIONS = 'DENY'
```

### `.env` file

```env
SECRET_KEY=your-very-secret-key-here
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1
DB_NAME=mydb
DB_USER=postgres
DB_PASSWORD=postgres
DB_HOST=localhost
DB_PORT=5432
REDIS_URL=redis://localhost:6379/0
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_HOST_USER=you@gmail.com
EMAIL_HOST_PASSWORD=your-app-password
CORS_ALLOWED_ORIGINS=http://localhost:3000,http://localhost:5173
```

---

## 5. URLs & Routing

### Root URL config (`config/urls.py`)

```python
from django.contrib import admin
from django.urls import path, include
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),

    # API routes
    path('api/v1/auth/', include('apps.users.urls')),
    path('api/v1/posts/', include('apps.posts.urls')),
    path('api/v1/products/', include('apps.products.urls')),

    # Third-party
    path('api/v1/auth/token/', include('rest_framework_simplejwt.urls')),
]

# Serve media files in development
if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
    urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)

    # Debug toolbar
    import debug_toolbar
    urlpatterns = [path('__debug__/', include(debug_toolbar.urls))] + urlpatterns
```

### App URL config (`apps/posts/urls.py`)

```python
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from . import views

# For ViewSets
router = DefaultRouter()
router.register(r'', views.PostViewSet, basename='post')

# For function/class-based views
urlpatterns = [
    # FBV / CBV
    path('', views.PostListView.as_view(), name='post-list'),
    path('<int:pk>/', views.PostDetailView.as_view(), name='post-detail'),
    path('create/', views.PostCreateView.as_view(), name='post-create'),
    path('<int:pk>/update/', views.PostUpdateView.as_view(), name='post-update'),
    path('<int:pk>/delete/', views.PostDeleteView.as_view(), name='post-delete'),

    # Nested routes
    path('<int:post_id>/comments/', views.CommentListView.as_view(), name='comment-list'),

    # ViewSet router URLs
    path('', include(router.urls)),
]
```

### URL Patterns — Converters

```python
# Built-in converters
path('users/<int:pk>/', view)          # Integer
path('users/<str:username>/', view)    # String (no slash)
path('files/<path:filepath>/', view)   # Path (with slashes)
path('orders/<uuid:order_id>/', view)  # UUID
path('items/<slug:slug>/', view)       # Slug (letters, numbers, hyphens)

# Regex patterns (re_path)
from django.urls import re_path
re_path(r'^users/(?P<pk>[0-9]+)/$', view)
re_path(r'^articles/(?P<year>[0-9]{4})/$', view)
```

### Named URLs & Reversing

```python
# In urls.py
path('users/<int:pk>/', views.UserDetail.as_view(), name='user-detail')

# In views.py
from django.urls import reverse
url = reverse('user-detail', kwargs={'pk': 1})   # /users/1/

# In templates
{% url 'user-detail' pk=1 %}

# In DRF serializers
from rest_framework.reverse import reverse
url = reverse('user-detail', kwargs={'pk': 1}, request=request)

# Namespace (when using include)
# urls.py
app_name = 'posts'
# template: {% url 'posts:post-detail' pk=1 %}
# python: reverse('posts:post-detail', kwargs={'pk': 1})
```

---

## 6. Views — Function Based (FBV)

FBVs are simple Python functions. Good for simple logic.

```python
# apps/posts/views.py
from django.shortcuts import render, get_object_or_404, redirect
from django.contrib.auth.decorators import login_required
from django.views.decorators.http import require_http_methods, require_GET, require_POST
from django.http import JsonResponse, HttpResponse, Http404
from django.contrib import messages
import json

from .models import Post
from .forms import PostForm


# Simple view returning JSON
def post_list(request):
    posts = Post.objects.filter(is_published=True).select_related('author')
    data = [{'id': p.id, 'title': p.title, 'author': p.author.username} for p in posts]
    return JsonResponse({'posts': data, 'total': len(data)})


# View with template
def post_list_html(request):
    posts = Post.objects.all().order_by('-created_at')
    context = {
        'posts': posts,
        'title': 'All Posts',
    }
    return render(request, 'posts/list.html', context)


# Detail view — 404 if not found
def post_detail(request, pk):
    post = get_object_or_404(Post, pk=pk, is_published=True)
    return render(request, 'posts/detail.html', {'post': post})


# Create view — login required
@login_required
@require_http_methods(['GET', 'POST'])
def post_create(request):
    if request.method == 'POST':
        form = PostForm(request.POST, request.FILES)
        if form.is_valid():
            post = form.save(commit=False)
            post.author = request.user
            post.save()
            messages.success(request, 'Post created successfully!')
            return redirect('post-detail', pk=post.pk)
    else:
        form = PostForm()

    return render(request, 'posts/create.html', {'form': form})


# Update view
@login_required
def post_update(request, pk):
    post = get_object_or_404(Post, pk=pk, author=request.user)

    if request.method == 'POST':
        form = PostForm(request.POST, request.FILES, instance=post)
        if form.is_valid():
            form.save()
            messages.success(request, 'Post updated!')
            return redirect('post-detail', pk=pk)
    else:
        form = PostForm(instance=post)

    return render(request, 'posts/update.html', {'form': form, 'post': post})


# Delete view
@login_required
@require_POST
def post_delete(request, pk):
    post = get_object_or_404(Post, pk=pk, author=request.user)
    post.delete()
    messages.success(request, 'Post deleted.')
    return redirect('post-list')


# API-style FBV
@require_http_methods(['GET', 'POST'])
def api_posts(request):
    if request.method == 'GET':
        posts = list(Post.objects.values('id', 'title', 'created_at'))
        return JsonResponse({'posts': posts})

    if request.method == 'POST':
        try:
            data = json.loads(request.body)
        except json.JSONDecodeError:
            return JsonResponse({'error': 'Invalid JSON'}, status=400)

        post = Post.objects.create(
            title=data.get('title'),
            content=data.get('content'),
            author=request.user,
        )
        return JsonResponse({'id': post.id, 'title': post.title}, status=201)
```

---

## 7. Views — Class Based (CBV)

CBVs are more powerful and reusable. Django provides many generic views.

### Generic CBVs

```python
from django.views.generic import (
    View, TemplateView, RedirectView,
    ListView, DetailView,
    CreateView, UpdateView, DeleteView,
    FormView,
)
from django.contrib.auth.mixins import LoginRequiredMixin, PermissionRequiredMixin
from django.urls import reverse_lazy
from .models import Post
from .forms import PostForm


# TemplateView — just renders a template
class AboutView(TemplateView):
    template_name = 'about.html'
    extra_context = {'title': 'About Us'}


# ListView — renders a list of objects
class PostListView(ListView):
    model = Post
    template_name = 'posts/list.html'
    context_object_name = 'posts'      # Variable name in template (default: object_list)
    paginate_by = 10
    ordering = ['-created_at']

    def get_queryset(self):
        qs = super().get_queryset().filter(is_published=True)
        search = self.request.GET.get('q')
        if search:
            qs = qs.filter(title__icontains=search)
        return qs

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['title'] = 'All Posts'
        context['total'] = self.get_queryset().count()
        return context


# DetailView — renders a single object
class PostDetailView(DetailView):
    model = Post
    template_name = 'posts/detail.html'
    context_object_name = 'post'

    def get_object(self):
        obj = super().get_object()
        # Increment view count
        Post.objects.filter(pk=obj.pk).update(view_count=obj.view_count + 1)
        return obj


# CreateView — form to create object
class PostCreateView(LoginRequiredMixin, CreateView):
    model = Post
    form_class = PostForm
    template_name = 'posts/form.html'
    success_url = reverse_lazy('post-list')
    login_url = '/login/'

    def form_valid(self, form):
        form.instance.author = self.request.user
        return super().form_valid(form)


# UpdateView — form to edit object
class PostUpdateView(LoginRequiredMixin, UpdateView):
    model = Post
    form_class = PostForm
    template_name = 'posts/form.html'

    def get_success_url(self):
        return reverse_lazy('post-detail', kwargs={'pk': self.object.pk})

    def get_queryset(self):
        # Only allow author to edit their own posts
        return super().get_queryset().filter(author=self.request.user)


# DeleteView — confirm and delete
class PostDeleteView(LoginRequiredMixin, DeleteView):
    model = Post
    template_name = 'posts/confirm_delete.html'
    success_url = reverse_lazy('post-list')

    def get_queryset(self):
        return super().get_queryset().filter(author=self.request.user)


# FormView — handle a form that doesn't directly map to a model
class ContactView(FormView):
    template_name = 'contact.html'
    form_class = ContactForm
    success_url = reverse_lazy('contact-success')

    def form_valid(self, form):
        form.send_email()
        return super().form_valid(form)


# Custom base View
class BaseView(View):
    def get(self, request, *args, **kwargs):
        return HttpResponse('GET response')

    def post(self, request, *args, **kwargs):
        return HttpResponse('POST response')
```

### Mixins

```python
from django.contrib.auth.mixins import (
    LoginRequiredMixin,           # @login_required equivalent
    PermissionRequiredMixin,      # Requires specific permission
    UserPassesTestMixin,          # Custom test function
)

class AdminOnlyView(UserPassesTestMixin, ListView):
    model = Post
    template_name = 'admin/posts.html'

    def test_func(self):
        return self.request.user.is_staff

    def handle_no_permission(self):
        return redirect('home')


class PermissionView(PermissionRequiredMixin, CreateView):
    permission_required = 'posts.add_post'   # app_label.action_modelname
    # or multiple:
    # permission_required = ['posts.add_post', 'posts.change_post']
```

---

## 8. Models & ORM

### Defining Models

```python
# apps/posts/models.py
from django.db import models
from django.contrib.auth import get_user_model
from django.utils.text import slugify
from django.urls import reverse

User = get_user_model()


class Category(models.Model):
    name = models.CharField(max_length=100, unique=True)
    slug = models.SlugField(max_length=100, unique=True, blank=True)
    description = models.TextField(blank=True)
    created_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        verbose_name_plural = 'categories'
        ordering = ['name']

    def __str__(self):
        return self.name

    def save(self, *args, **kwargs):
        if not self.slug:
            self.slug = slugify(self.name)
        super().save(*args, **kwargs)


class Tag(models.Model):
    name = models.CharField(max_length=50, unique=True)

    def __str__(self):
        return self.name


class Post(models.Model):
    STATUS_DRAFT = 'draft'
    STATUS_PUBLISHED = 'published'
    STATUS_ARCHIVED = 'archived'

    STATUS_CHOICES = [
        (STATUS_DRAFT, 'Draft'),
        (STATUS_PUBLISHED, 'Published'),
        (STATUS_ARCHIVED, 'Archived'),
    ]

    # Fields
    title = models.CharField(max_length=200)
    slug = models.SlugField(max_length=200, unique=True, blank=True)
    content = models.TextField()
    excerpt = models.TextField(max_length=500, blank=True)
    cover_image = models.ImageField(upload_to='posts/covers/%Y/%m/', blank=True, null=True)
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default=STATUS_DRAFT)
    is_featured = models.BooleanField(default=False)
    view_count = models.PositiveIntegerField(default=0)
    read_time = models.PositiveSmallIntegerField(default=0, help_text='In minutes')

    # Relationships
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='posts')
    category = models.ForeignKey(Category, on_delete=models.SET_NULL, null=True, blank=True, related_name='posts')
    tags = models.ManyToManyField(Tag, blank=True, related_name='posts')

    # Timestamps
    created_at = models.DateTimeField(auto_now_add=True)   # Set once on creation
    updated_at = models.DateTimeField(auto_now=True)        # Updated on every save
    published_at = models.DateTimeField(null=True, blank=True)

    class Meta:
        ordering = ['-created_at']
        indexes = [
            models.Index(fields=['-created_at']),
            models.Index(fields=['status', '-created_at']),
            models.Index(fields=['author', 'status']),
        ]
        verbose_name = 'Post'
        verbose_name_plural = 'Posts'
        constraints = [
            models.UniqueConstraint(fields=['author', 'slug'], name='unique_author_slug')
        ]

    def __str__(self):
        return self.title

    def get_absolute_url(self):
        return reverse('post-detail', kwargs={'slug': self.slug})

    def save(self, *args, **kwargs):
        if not self.slug:
            self.slug = slugify(self.title)
        # Calculate read time (avg 200 words/min)
        word_count = len(self.content.split())
        self.read_time = max(1, word_count // 200)
        super().save(*args, **kwargs)

    @property
    def is_published(self):
        return self.status == self.STATUS_PUBLISHED

    @property
    def summary(self):
        return self.excerpt or self.content[:200] + '...'


class Comment(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE, related_name='comments')
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='comments')
    parent = models.ForeignKey('self', on_delete=models.CASCADE, null=True, blank=True, related_name='replies')
    content = models.TextField()
    is_approved = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        ordering = ['created_at']

    def __str__(self):
        return f'Comment by {self.author.username} on {self.post.title}'
```

### All Model Field Types

```python
# Text
models.CharField(max_length=200)
models.TextField()
models.SlugField(max_length=200)
models.EmailField()
models.URLField()
models.UUIDField(default=uuid.uuid4, unique=True)
models.IPAddressField()
models.GenericIPAddressField()

# Numbers
models.IntegerField()
models.BigIntegerField()
models.SmallIntegerField()
models.PositiveIntegerField()
models.PositiveBigIntegerField()
models.PositiveSmallIntegerField()
models.FloatField()
models.DecimalField(max_digits=10, decimal_places=2)

# Boolean
models.BooleanField(default=False)
models.NullBooleanField()  # Deprecated — use BooleanField(null=True)

# Date/Time
models.DateField(auto_now=False, auto_now_add=False)
models.TimeField()
models.DateTimeField(auto_now_add=True)   # Set on creation
models.DateTimeField(auto_now=True)        # Updated on every save
models.DurationField()

# File
models.FileField(upload_to='files/')
models.ImageField(upload_to='images/%Y/%m/')
models.FilePathField(path='/home/images')

# Choices
models.CharField(max_length=10, choices=[('A', 'Active'), ('I', 'Inactive')])

# JSON (PostgreSQL native / SQLite 3.38+)
models.JSONField(default=dict)

# Binary
models.BinaryField()

# Auto primary key
models.AutoField()
models.BigAutoField()
models.SmallAutoField()

# Common field options
models.CharField(
    max_length=100,
    null=True,          # Allow NULL in DB
    blank=True,         # Allow empty in forms/serializers
    default='value',    # Default value
    unique=True,        # Must be unique across table
    db_index=True,      # Create DB index
    verbose_name='My Field',
    help_text='Explanation shown in admin/forms',
    editable=False,     # Hide from forms/admin
    choices=[...],      # Restrict to specific values
)
```

### Relationships

```python
# One-to-Many (ForeignKey)
author = models.ForeignKey(
    User,
    on_delete=models.CASCADE,       # Delete post when user deleted
    # on_delete options:
    # CASCADE   — delete related objects
    # PROTECT   — prevent deletion if related objects exist
    # SET_NULL  — set FK to null (requires null=True)
    # SET_DEFAULT — set FK to default value
    # DO_NOTHING — do nothing (may cause integrity errors)
    related_name='posts',            # user.posts.all()
    related_query_name='post',
    null=True,
    blank=True,
)

# Many-to-Many
tags = models.ManyToManyField(
    Tag,
    blank=True,
    related_name='posts',
    through='PostTag',         # Optional: custom through model
    symmetrical=False,         # For self-referential M2M
)

# One-to-One
profile = models.OneToOneField(
    User,
    on_delete=models.CASCADE,
    related_name='profile',
)

# Self-referential (tree structure, replies)
parent = models.ForeignKey(
    'self',
    on_delete=models.CASCADE,
    null=True,
    blank=True,
    related_name='children',
)

# Custom through model for M2M with extra fields
class PostTag(models.Model):
    post = models.ForeignKey(Post, on_delete=models.CASCADE)
    tag = models.ForeignKey(Tag, on_delete=models.CASCADE)
    added_at = models.DateTimeField(auto_now_add=True)
    added_by = models.ForeignKey(User, on_delete=models.SET_NULL, null=True)
```

### QuerySet — Complete ORM Reference

```python
from django.db.models import (
    Q, F, Value, Count, Sum, Avg, Max, Min,
    Case, When, CharField, IntegerField,
    Prefetch, Subquery, OuterRef, Exists,
)

# --- Basic Retrieval ---
Post.objects.all()                         # All posts
Post.objects.get(pk=1)                     # Raises DoesNotExist or MultipleObjectsReturned
Post.objects.get_or_create(title='Hi', defaults={'content': 'World'})
Post.objects.update_or_create(slug='hi', defaults={'title': 'Hello'})
Post.objects.first()
Post.objects.last()
Post.objects.none()                        # Empty queryset

# --- Filtering ---
Post.objects.filter(status='published')
Post.objects.exclude(status='draft')
Post.objects.filter(author__username='john')   # Related model traversal
Post.objects.filter(author__is_active=True)

# Field lookups
Post.objects.filter(title__exact='Hello')
Post.objects.filter(title__iexact='hello')       # Case-insensitive
Post.objects.filter(title__contains='ello')
Post.objects.filter(title__icontains='ello')     # Case-insensitive
Post.objects.filter(title__startswith='He')
Post.objects.filter(title__endswith='lo')
Post.objects.filter(title__in=['Hello', 'World'])
Post.objects.filter(created_at__date=date.today())
Post.objects.filter(created_at__year=2024)
Post.objects.filter(created_at__month=6)
Post.objects.filter(created_at__day=15)
Post.objects.filter(created_at__gte=datetime(2024, 1, 1))
Post.objects.filter(created_at__lte=datetime(2024, 12, 31))
Post.objects.filter(created_at__range=(start_date, end_date))
Post.objects.filter(view_count__gt=100)          # Greater than
Post.objects.filter(view_count__lt=1000)         # Less than
Post.objects.filter(view_count__gte=100)         # Greater than or equal
Post.objects.filter(view_count__lte=1000)        # Less than or equal
Post.objects.filter(cover_image__isnull=True)    # IS NULL
Post.objects.filter(cover_image__isnull=False)   # IS NOT NULL
Post.objects.filter(tags__name='python')         # M2M traversal

# Q objects — complex OR/AND/NOT
Post.objects.filter(Q(status='published') | Q(is_featured=True))
Post.objects.filter(Q(title__icontains='django') & Q(status='published'))
Post.objects.filter(~Q(status='draft'))          # NOT

# --- Ordering ---
Post.objects.order_by('title')             # ASC
Post.objects.order_by('-created_at')       # DESC
Post.objects.order_by('author__username', '-created_at')
Post.objects.order_by('?')                 # Random

# --- Slicing (no negative indexing!)
Post.objects.all()[:10]                    # First 10
Post.objects.all()[10:20]                  # 10–20

# --- Aggregation ---
from django.db.models import Count, Sum, Avg, Max, Min

Post.objects.count()
Post.objects.aggregate(total=Count('id'))
Post.objects.aggregate(avg_views=Avg('view_count'))
Post.objects.aggregate(max_views=Max('view_count'), total=Sum('view_count'))

# --- Annotation (add computed field to each row)
from django.db.models import Count

posts = Post.objects.annotate(
    comment_count=Count('comments'),
    like_count=Count('likes'),
)
# Now each post has .comment_count and .like_count

posts = Post.objects.annotate(
    is_popular=Case(
        When(view_count__gte=1000, then=Value(True)),
        default=Value(False),
        output_field=models.BooleanField(),
    )
)

# --- F Expressions (reference DB column value)
from django.db.models import F

# Increment view_count without race condition
Post.objects.filter(pk=1).update(view_count=F('view_count') + 1)

# Compare two fields
Post.objects.filter(updated_at__gt=F('created_at'))

# --- Select Related (SQL JOIN — reduces queries for FK/O2O)
posts = Post.objects.select_related('author', 'category').all()
# Access post.author.username without extra DB query

# --- Prefetch Related (separate query — for M2M and reverse FK)
posts = Post.objects.prefetch_related('tags', 'comments').all()
posts = Post.objects.prefetch_related(
    Prefetch('comments', queryset=Comment.objects.filter(is_approved=True))
).all()

# --- Values & Values List
Post.objects.values('id', 'title')           # List of dicts
Post.objects.values_list('id', flat=True)    # List of single values
Post.objects.values_list('id', 'title')      # List of tuples

# --- Only & Defer (partial loading)
Post.objects.only('id', 'title')             # Load ONLY these fields
Post.objects.defer('content')                # Load everything EXCEPT content

# --- Distinct
Post.objects.filter(tags__name='python').distinct()

# --- Exists
has_posts = Post.objects.filter(author=user).exists()  # Efficient — no count

# --- Create
post = Post.objects.create(title='Hello', content='World', author=user)

# --- Bulk Create (single query)
posts = [Post(title=f'Post {i}', content='...', author=user) for i in range(100)]
Post.objects.bulk_create(posts, batch_size=50)

# --- Bulk Update (single query)
Post.objects.filter(status='draft').update(is_featured=False)
Post.objects.bulk_update(posts, ['title', 'content'])

# --- Delete
Post.objects.filter(status='draft').delete()
post.delete()

# --- Subqueries
latest_comment = Comment.objects.filter(post=OuterRef('pk')).order_by('-created_at')
posts = Post.objects.annotate(latest_comment_date=Subquery(latest_comment.values('created_at')[:1]))

# Check existence in annotation
posts = Post.objects.annotate(
    has_comments=Exists(Comment.objects.filter(post=OuterRef('pk'), is_approved=True))
)

# --- Raw SQL (use sparingly)
posts = Post.objects.raw('SELECT * FROM posts_post WHERE status = %s', ['published'])

# Django's connection for raw SQL
from django.db import connection
with connection.cursor() as cursor:
    cursor.execute("SELECT COUNT(*) FROM posts_post")
    row = cursor.fetchone()

# --- Transactions
from django.db import transaction

@transaction.atomic
def create_post_with_tags(post_data, tag_names):
    post = Post.objects.create(**post_data)
    for name in tag_names:
        tag, _ = Tag.objects.get_or_create(name=name)
        post.tags.add(tag)
    return post

# Manual transaction control
with transaction.atomic():
    post = Post.objects.create(...)
    if some_error:
        transaction.set_rollback(True)

# Savepoints
with transaction.atomic():
    post = Post.objects.create(...)
    try:
        with transaction.atomic():  # Nested creates a savepoint
            do_risky_operation()
    except Exception:
        pass  # Rolls back to savepoint, outer transaction continues
```

### Custom Managers

```python
class PublishedManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(status='published')

    def featured(self):
        return self.get_queryset().filter(is_featured=True)

    def by_author(self, user):
        return self.get_queryset().filter(author=user)


class Post(models.Model):
    # ...
    objects = models.Manager()           # Default manager
    published = PublishedManager()       # Custom manager

# Usage
Post.published.all()
Post.published.featured()
Post.published.by_author(request.user)
```

### Model Methods & Properties

```python
class Post(models.Model):
    # Instance method
    def get_share_url(self):
        return f'https://mysite.com/posts/{self.slug}'

    # Class method
    @classmethod
    def get_trending(cls, limit=5):
        return cls.published.order_by('-view_count')[:limit]

    # Static method
    @staticmethod
    def allowed_statuses():
        return [s[0] for s in Post.STATUS_CHOICES]

    # Property (no DB query overhead, computed from existing data)
    @property
    def short_title(self):
        return self.title[:50] + '...' if len(self.title) > 50 else self.title

    # Override save
    def save(self, *args, **kwargs):
        if not self.slug:
            self.slug = slugify(self.title)
        super().save(*args, **kwargs)

    # Override delete
    def delete(self, *args, **kwargs):
        # Custom cleanup before deletion
        self.cover_image.delete(save=False)
        super().delete(*args, **kwargs)
```

### Abstract Models (Mixins)

```python
class TimeStampedModel(models.Model):
    """Abstract model to add timestamps to any model."""
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        abstract = True  # ← Never creates a DB table


class SoftDeleteModel(models.Model):
    deleted_at = models.DateTimeField(null=True, blank=True)

    class Meta:
        abstract = True

    def soft_delete(self):
        from django.utils import timezone
        self.deleted_at = timezone.now()
        self.save()

    def restore(self):
        self.deleted_at = None
        self.save()

    @property
    def is_deleted(self):
        return self.deleted_at is not None


# Use them
class Post(TimeStampedModel, SoftDeleteModel):
    title = models.CharField(max_length=200)
    # Automatically gets: created_at, updated_at, deleted_at
```

---

## 9. Migrations

Migrations track changes to your models over time and apply them to the database.

### Workflow

```bash
# 1. Make changes to your models.py

# 2. Generate migration file
python manage.py makemigrations
python manage.py makemigrations posts          # Specific app only
python manage.py makemigrations --name "add_slug_to_post"  # Custom name

# 3. Review migration (optional but recommended)
python manage.py sqlmigrate posts 0002

# 4. Apply migrations
python manage.py migrate
python manage.py migrate posts                 # Specific app
python manage.py migrate posts 0002            # Specific version
python manage.py migrate posts zero            # Revert all migrations in app

# 5. Show migration status
python manage.py showmigrations
python manage.py showmigrations posts
```

### Manual Migration File

```python
# apps/posts/migrations/0003_add_read_time.py
from django.db import migrations, models


class Migration(migrations.Migration):

    dependencies = [
        ('posts', '0002_add_slug'),
    ]

    operations = [
        migrations.AddField(
            model_name='post',
            name='read_time',
            field=models.PositiveSmallIntegerField(default=0),
        ),
        migrations.AlterField(
            model_name='post',
            name='status',
            field=models.CharField(
                choices=[('draft', 'Draft'), ('published', 'Published')],
                default='draft',
                max_length=20,
            ),
        ),
        migrations.RemoveField(
            model_name='post',
            name='old_field',
        ),
        migrations.RenameField(
            model_name='post',
            old_name='body',
            new_name='content',
        ),
        migrations.CreateModel(
            name='Tag',
            fields=[
                ('id', models.BigAutoField(auto_created=True, primary_key=True)),
                ('name', models.CharField(max_length=50, unique=True)),
            ],
        ),
        migrations.DeleteModel(name='OldModel'),
    ]
```

### Data Migration

```python
# Generate empty migration
python manage.py makemigrations --empty posts --name "populate_slugs"

# apps/posts/migrations/0004_populate_slugs.py
from django.db import migrations
from django.utils.text import slugify


def populate_slugs(apps, schema_editor):
    Post = apps.get_model('posts', 'Post')
    for post in Post.objects.filter(slug=''):
        post.slug = slugify(post.title)
        post.save(update_fields=['slug'])


def reverse_slugs(apps, schema_editor):
    Post = apps.get_model('posts', 'Post')
    Post.objects.update(slug='')


class Migration(migrations.Migration):
    dependencies = [('posts', '0003_add_slug_field')]

    operations = [
        migrations.RunPython(populate_slugs, reverse_slugs),
    ]
```

---

## 10. Django Admin

### Basic Registration

```python
# apps/posts/admin.py
from django.contrib import admin
from .models import Post, Category, Tag, Comment


# Simple registration
admin.site.register(Category)
admin.site.register(Tag)


# Custom ModelAdmin
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    # List view
    list_display = ['title', 'author', 'status', 'is_featured', 'view_count', 'created_at']
    list_display_links = ['title']                         # Clickable fields
    list_editable = ['status', 'is_featured']              # Editable in list
    list_filter = ['status', 'is_featured', 'category', 'created_at']
    list_per_page = 25
    list_select_related = ['author', 'category']           # Optimize queries
    search_fields = ['title', 'content', 'author__username']
    date_hierarchy = 'created_at'                          # Date drill-down
    ordering = ['-created_at']
    show_full_result_count = False                         # Better performance

    # Detail view
    fieldsets = [
        ('Content', {
            'fields': ['title', 'slug', 'content', 'excerpt', 'cover_image'],
        }),
        ('Publishing', {
            'fields': ['status', 'is_featured', 'published_at'],
        }),
        ('Relations', {
            'fields': ['author', 'category', 'tags'],
        }),
        ('Metadata', {
            'fields': ['view_count', 'read_time'],
            'classes': ['collapse'],    # Collapsed by default
        }),
    ]
    readonly_fields = ['view_count', 'read_time', 'created_at', 'updated_at']
    prepopulated_fields = {'slug': ('title',)}             # Auto-fill slug from title
    autocomplete_fields = ['author', 'category']           # Search instead of dropdown
    filter_horizontal = ['tags']                           # Nice M2M widget
    raw_id_fields = ['author']                             # For large FK tables

    # Actions
    actions = ['publish_posts', 'unpublish_posts']

    @admin.action(description='Mark selected as published')
    def publish_posts(self, request, queryset):
        updated = queryset.update(status='published')
        self.message_user(request, f'{updated} posts published.')

    @admin.action(description='Mark selected as draft')
    def unpublish_posts(self, request, queryset):
        queryset.update(status='draft')

    # Custom list display method
    @admin.display(description='Short Title', ordering='title')
    def short_title(self, obj):
        return obj.title[:30] + '...' if len(obj.title) > 30 else obj.title

    # Custom column with HTML
    @admin.display(description='Status Badge')
    def status_badge(self, obj):
        from django.utils.html import format_html
        color = 'green' if obj.status == 'published' else 'orange'
        return format_html('<span style="color: {};">{}</span>', color, obj.get_status_display())

    # Limit queryset for admin
    def get_queryset(self, request):
        qs = super().get_queryset(request)
        if not request.user.is_superuser:
            qs = qs.filter(author=request.user)
        return qs

    # Override save
    def save_model(self, request, obj, form, change):
        if not change:  # Only on creation
            obj.author = request.user
        super().save_model(request, obj, form, change)


# Inline admin (show related objects inside parent)
class CommentInline(admin.TabularInline):   # or StackedInline
    model = Comment
    extra = 0                               # Don't show empty forms
    readonly_fields = ['author', 'created_at']
    fields = ['author', 'content', 'is_approved', 'created_at']
    can_delete = True
    max_num = 20


@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    inlines = [CommentInline]
    # ...


# Customize admin site
admin.site.site_header = 'My Project Admin'
admin.site.site_title = 'My Project'
admin.site.index_title = 'Administration'
```

---

## 11. Forms

### Model Forms (most common)

```python
# apps/posts/forms.py
from django import forms
from django.core.exceptions import ValidationError
from .models import Post, Comment


class PostForm(forms.ModelForm):
    # Override a field
    content = forms.CharField(widget=forms.Textarea(attrs={'rows': 10, 'class': 'form-control'}))

    class Meta:
        model = Post
        fields = ['title', 'slug', 'content', 'excerpt', 'cover_image', 'status', 'category', 'tags']
        # Or exclude specific fields:
        # exclude = ['author', 'view_count', 'read_time']

        widgets = {
            'title': forms.TextInput(attrs={'class': 'form-control', 'placeholder': 'Post title'}),
            'excerpt': forms.Textarea(attrs={'class': 'form-control', 'rows': 3}),
            'status': forms.Select(attrs={'class': 'form-select'}),
        }

        labels = {
            'title': 'Post Title',
            'excerpt': 'Short Description',
        }

        help_texts = {
            'slug': 'URL-friendly version of the title. Auto-generated if left blank.',
        }

        error_messages = {
            'title': {
                'required': 'Please enter a title.',
                'max_length': 'Title is too long.',
            }
        }

    # Field-level validation
    def clean_title(self):
        title = self.cleaned_data.get('title')
        if len(title) < 5:
            raise ValidationError('Title must be at least 5 characters.')
        return title.strip()

    # Cross-field validation
    def clean(self):
        cleaned_data = super().clean()
        status = cleaned_data.get('status')
        content = cleaned_data.get('content')

        if status == 'published' and not content:
            raise ValidationError('Published posts must have content.')

        return cleaned_data


class CommentForm(forms.ModelForm):
    class Meta:
        model = Comment
        fields = ['content']
        widgets = {
            'content': forms.Textarea(attrs={'rows': 4, 'class': 'form-control'})
        }


# Plain Form (not tied to a model)
class ContactForm(forms.Form):
    name = forms.CharField(max_length=100, widget=forms.TextInput(attrs={'class': 'form-control'}))
    email = forms.EmailField(widget=forms.EmailInput(attrs={'class': 'form-control'}))
    subject = forms.CharField(max_length=200)
    message = forms.CharField(widget=forms.Textarea(attrs={'rows': 5}))
    rating = forms.ChoiceField(choices=[(i, i) for i in range(1, 6)])
    agree = forms.BooleanField(required=True)

    def clean_email(self):
        email = self.cleaned_data['email']
        if email.endswith('@spam.com'):
            raise ValidationError('Spam emails not allowed.')
        return email

    def send_email(self):
        from django.core.mail import send_mail
        send_mail(
            subject=f"Contact: {self.cleaned_data['subject']}",
            message=self.cleaned_data['message'],
            from_email=self.cleaned_data['email'],
            recipient_list=['admin@mysite.com'],
        )
```

### Using Forms in Views

```python
def contact(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid():
            form.send_email()
            messages.success(request, 'Message sent!')
            return redirect('contact-success')
    else:
        form = ContactForm()

    return render(request, 'contact.html', {'form': form})
```

### Form Widgets

```python
forms.TextInput()
forms.PasswordInput()
forms.Textarea(attrs={'rows': 5})
forms.EmailInput()
forms.URLInput()
forms.NumberInput()
forms.CheckboxInput()
forms.Select()
forms.SelectMultiple()
forms.RadioSelect()
forms.CheckboxSelectMultiple()
forms.DateInput(attrs={'type': 'date'})
forms.DateTimeInput(attrs={'type': 'datetime-local'})
forms.FileInput()
forms.ClearableFileInput()
forms.HiddenInput()
forms.SplitDateTimeWidget()
```

---

## 12. Templates

### Directory & Settings

```python
# settings.py
TEMPLATES = [{
    'BACKEND': 'django.template.backends.django.DjangoTemplates',
    'DIRS': [BASE_DIR / 'templates'],  # Global templates
    'APP_DIRS': True,                  # Also look in app/templates/
    ...
}]
```

### `templates/base.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}My Site{% endblock %}</title>
    <link rel="stylesheet" href="{% static 'css/style.css' %}">
    {% block extra_css %}{% endblock %}
</head>
<body>
    {% include 'partials/navbar.html' %}

    {% if messages %}
        {% for message in messages %}
            <div class="alert alert-{{ message.tags }}">{{ message }}</div>
        {% endfor %}
    {% endif %}

    <main>
        {% block content %}{% endblock %}
    </main>

    {% include 'partials/footer.html' %}
    <script src="{% static 'js/main.js' %}"></script>
    {% block extra_js %}{% endblock %}
</body>
</html>
```

### `templates/posts/list.html`

```html
{% extends 'base.html' %}
{% load static %}

{% block title %}Posts — {{ block.super }}{% endblock %}

{% block content %}
<h1>All Posts</h1>

{# Variables #}
<p>Total: {{ total }}</p>
<p>User: {{ request.user.username }}</p>

{# Filters #}
<p>{{ post.title | upper }}</p>
<p>{{ post.content | truncatewords:50 }}</p>
<p>{{ post.created_at | date:"F j, Y" }}</p>
<p>{{ post.created_at | timesince }} ago</p>
<p>{{ post.content | linebreaksbr }}</p>
<p>{{ post.content | striptags }}</p>
<p>{{ amount | floatformat:2 }}</p>
<p>{{ number | intcomma }}</p>
<p>{{ html_content | safe }}</p>
<p>{{ name | default:"Anonymous" }}</p>
<p>{{ list | join:", " }}</p>
<p>{{ list | length }}</p>

{# Conditionals #}
{% if user.is_authenticated %}
    <a href="{% url 'post-create' %}">Create Post</a>
{% elif user.is_staff %}
    <a href="{% url 'admin:index' %}">Admin</a>
{% else %}
    <a href="{% url 'login' %}">Login</a>
{% endif %}

{# For loop #}
{% for post in posts %}
    <article>
        <h2>
            <a href="{{ post.get_absolute_url }}">{{ post.title }}</a>
        </h2>
        <p>{{ post.excerpt }}</p>
        <small>
            By {{ post.author.get_full_name|default:post.author.username }}
            on {{ post.created_at | date:"M d, Y" }}
        </small>
    </article>
    {% if forloop.last %}<hr>{% endif %}
{% empty %}
    <p>No posts found.</p>
{% endfor %}

{# Pagination #}
{% if is_paginated %}
    <nav>
        {% if page_obj.has_previous %}
            <a href="?page={{ page_obj.previous_page_number }}">Previous</a>
        {% endif %}

        <span>Page {{ page_obj.number }} of {{ page_obj.paginator.num_pages }}</span>

        {% if page_obj.has_next %}
            <a href="?page={{ page_obj.next_page_number }}">Next</a>
        {% endif %}
    </nav>
{% endif %}

{# URL tag #}
<a href="{% url 'post-detail' pk=post.pk %}">Read More</a>
<a href="{% url 'post-update' pk=post.pk %}">Edit</a>

{# Static file #}
<img src="{% static 'images/logo.png' %}" alt="Logo">

{# CSRF token — required in all POST forms #}
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Submit</button>
</form>

{# Block #}
{% block extra_js %}
    <script>console.log('Posts page');</script>
{% endblock %}
{% endblock %}
```

### Custom Template Tags & Filters

```python
# apps/posts/templatetags/__init__.py  (empty file)
# apps/posts/templatetags/post_extras.py

from django import template
from django.utils.html import format_html
from ..models import Post

register = template.Library()


# Simple filter
@register.filter
def truncate_chars(value, arg):
    if len(value) > arg:
        return value[:arg] + '...'
    return value


# Filter with no argument
@register.filter
def is_published(post):
    return post.status == 'published'


# Simple tag
@register.simple_tag
def total_posts():
    return Post.objects.filter(status='published').count()


# Simple tag with request
@register.simple_tag(takes_context=True)
def active_link(context, url_name):
    request = context['request']
    from django.urls import resolve
    if resolve(request.path).url_name == url_name:
        return 'active'
    return ''


# Inclusion tag (renders a template)
@register.inclusion_tag('posts/partials/recent_posts.html')
def recent_posts(count=5):
    posts = Post.objects.filter(status='published').order_by('-created_at')[:count]
    return {'recent_posts': posts}


# HTML-safe tag
@register.simple_tag
def badge(text, color='blue'):
    return format_html('<span class="badge badge-{}">{}</span>', color, text)
```

```html
{# Using custom tags in templates #}
{% load post_extras %}

{# Filter #}
{{ post.title | truncate_chars:30 }}
{% if post | is_published %}Published{% endif %}

{# Simple tag #}
Total Posts: {% total_posts %}
<li class="{% active_link 'post-list' %}">Posts</li>

{# Inclusion tag #}
{% recent_posts count=3 %}
```

---

## 13. Static & Media Files

### Settings

```python
# Static files (CSS, JS, images shipped with the app)
STATIC_URL = '/static/'
STATICFILES_DIRS = [BASE_DIR / 'static']           # Dev source
STATIC_ROOT = BASE_DIR / 'staticfiles'             # Production destination

# Media files (user uploads)
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'
```

### Serving in Development

```python
# config/urls.py
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # ...
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
  + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

### Collecting Static for Production

```bash
python manage.py collectstatic  # Copies all static files to STATIC_ROOT
```

### WhiteNoise (Serve static in production)

```bash
pip install whitenoise
```

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',   # Add right after SecurityMiddleware
    ...
]
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```

---

## 14. Authentication System

Django's built-in auth system provides users, groups, permissions, and sessions.

### Built-in Auth Views

```python
# config/urls.py
from django.contrib.auth import views as auth_views

urlpatterns = [
    path('login/', auth_views.LoginView.as_view(template_name='auth/login.html'), name='login'),
    path('logout/', auth_views.LogoutView.as_view(), name='logout'),
    path('password-change/', auth_views.PasswordChangeView.as_view(), name='password-change'),
    path('password-change/done/', auth_views.PasswordChangeDoneView.as_view(), name='password-change-done'),
    path('password-reset/', auth_views.PasswordResetView.as_view(), name='password-reset'),
    path('password-reset/done/', auth_views.PasswordResetDoneView.as_view(), name='password-reset-done'),
    path('reset/<uidb64>/<token>/', auth_views.PasswordResetConfirmView.as_view(), name='password-reset-confirm'),
    path('reset/done/', auth_views.PasswordResetCompleteView.as_view(), name='password-reset-complete'),
]
```

### Custom Registration View

```python
# apps/users/views.py
from django.contrib.auth import login, logout, authenticate, get_user_model
from django.contrib.auth.decorators import login_required
from django.contrib.auth.forms import UserCreationForm

User = get_user_model()


def register(request):
    if request.user.is_authenticated:
        return redirect('home')

    if request.method == 'POST':
        form = CustomUserCreationForm(request.POST)
        if form.is_valid():
            user = form.save()
            login(request, user)
            messages.success(request, 'Welcome! Your account has been created.')
            return redirect('dashboard')
    else:
        form = CustomUserCreationForm()

    return render(request, 'auth/register.html', {'form': form})


def user_login(request):
    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')
        user = authenticate(request, username=username, password=password)

        if user is not None:
            login(request, user)
            next_url = request.GET.get('next', 'dashboard')
            return redirect(next_url)
        else:
            messages.error(request, 'Invalid username or password.')

    return render(request, 'auth/login.html')


def user_logout(request):
    logout(request)
    return redirect('login')


@login_required
def dashboard(request):
    return render(request, 'dashboard.html', {'user': request.user})
```

### Permissions

```python
# Check in views
from django.contrib.auth.decorators import permission_required

@permission_required('posts.add_post', raise_exception=True)
def create_post(request):
    pass

# In templates
{% if perms.posts.add_post %}
    <a href="{% url 'post-create' %}">Create Post</a>
{% endif %}

# Programmatically
user.has_perm('posts.add_post')
user.has_perms(['posts.add_post', 'posts.change_post'])
user.has_module_perms('posts')

# Assign permissions
from django.contrib.auth.models import Permission
from django.contrib.contenttypes.models import ContentType

content_type = ContentType.objects.get_for_model(Post)
permission = Permission.objects.get(content_type=content_type, codename='add_post')
user.user_permissions.add(permission)

# Groups
from django.contrib.auth.models import Group
editors_group, _ = Group.objects.get_or_create(name='Editors')
editors_group.permissions.add(permission)
user.groups.add(editors_group)
```

### Custom Permissions

```python
class Post(models.Model):
    # ...
    class Meta:
        permissions = [
            ('publish_post', 'Can publish posts'),
            ('feature_post', 'Can feature posts'),
        ]

# Check
user.has_perm('posts.publish_post')
```

---

## 15. Middleware

Middleware is a hook framework of request/response processing. Each middleware component is called for every request.

### Custom Middleware

```python
# apps/core/middleware.py
import time
import logging
from django.http import JsonResponse
from django.utils.deprecation import MiddlewareMixin

logger = logging.getLogger(__name__)


# Modern style (callable class)
class TimingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        start = time.time()

        response = self.get_response(request)  # Calls next middleware/view

        duration = time.time() - start
        response['X-Response-Time'] = f'{duration:.4f}s'
        logger.info(f'{request.method} {request.path} — {response.status_code} ({duration:.4f}s)')

        return response


# With process_view and process_exception
class RequestLoggingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        # Code here runs BEFORE the view
        logger.debug(f'Incoming: {request.method} {request.path}')

        response = self.get_response(request)

        # Code here runs AFTER the view
        logger.debug(f'Outgoing: {response.status_code}')

        return response

    def process_view(self, request, view_func, view_args, view_kwargs):
        # Called just before Django calls the view
        # Return None to continue, or HttpResponse to short-circuit
        pass

    def process_exception(self, request, exception):
        # Called when an unhandled exception occurs in the view
        logger.error(f'Exception in {request.path}: {exception}')
        return None  # None = continue to default exception handling

    def process_template_response(self, request, response):
        # Called if response has render() method (TemplateResponse)
        return response


# API Key authentication middleware
class APIKeyMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
        self.excluded_paths = ['/api/v1/auth/login/', '/api/v1/auth/register/']

    def __call__(self, request):
        if request.path.startswith('/api/') and request.path not in self.excluded_paths:
            api_key = request.headers.get('X-API-Key')
            if not self.is_valid_key(api_key):
                return JsonResponse({'error': 'Invalid or missing API key'}, status=401)

        return self.get_response(request)

    def is_valid_key(self, key):
        from apps.core.models import APIKey
        return APIKey.objects.filter(key=key, is_active=True).exists()


# Add to settings.py
MIDDLEWARE = [
    ...
    'apps.core.middleware.TimingMiddleware',
    'apps.core.middleware.RequestLoggingMiddleware',
]
```

---

## 16. Signals

Signals allow decoupled parts of the application to get notified when certain actions occur.

### Built-in Signals

```python
# Model signals
from django.db.models.signals import (
    pre_save, post_save,
    pre_delete, post_delete,
    m2m_changed,
    pre_init, post_init,
)

# Request signals
from django.core.signals import request_started, request_finished, got_request_exception

# Auth signals
from django.contrib.auth.signals import user_logged_in, user_logged_out, user_login_failed
```

### Defining Signals

```python
# apps/users/signals.py
from django.db.models.signals import post_save, post_delete
from django.contrib.auth.signals import user_logged_in
from django.dispatch import receiver
from django.contrib.auth import get_user_model

User = get_user_model()


# Auto-create profile when user is created
@receiver(post_save, sender=User)
def create_user_profile(sender, instance, created, **kwargs):
    if created:
        from .models import Profile
        Profile.objects.create(user=instance)


# Auto-save profile when user is saved
@receiver(post_save, sender=User)
def save_user_profile(sender, instance, **kwargs):
    if hasattr(instance, 'profile'):
        instance.profile.save()


# Send welcome email on registration
@receiver(post_save, sender=User)
def send_welcome_email(sender, instance, created, **kwargs):
    if created:
        from django.core.mail import send_mail
        send_mail(
            'Welcome!',
            f'Hi {instance.username}, welcome to our platform!',
            'noreply@mysite.com',
            [instance.email],
            fail_silently=True,
        )


# Log login events
@receiver(user_logged_in)
def log_user_login(sender, request, user, **kwargs):
    import logging
    logger = logging.getLogger(__name__)
    logger.info(f'User {user.username} logged in from {request.META.get("REMOTE_ADDR")}')


# Log deletion
@receiver(post_delete, sender=User)
def log_user_deletion(sender, instance, **kwargs):
    print(f'User {instance.username} was deleted.')


# Custom signal
from django.dispatch import Signal

post_published = Signal()  # Define custom signal

# Emit it somewhere
# post_published.send(sender=Post, instance=post, request=request)

# Listen to it
@receiver(post_published)
def handle_post_published(sender, instance, request, **kwargs):
    # Send notification to subscribers
    pass
```

### Register Signals in AppConfig

```python
# apps/users/apps.py
from django.apps import AppConfig


class UsersConfig(AppConfig):
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'apps.users'

    def ready(self):
        import apps.users.signals  # Import signals when app is ready
```

---

## 17. Django REST Framework (DRF)

DRF is the standard way to build REST APIs in Django.

### Install

```bash
pip install djangorestframework
```

```python
# settings.py
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```

### Quick API Example

```python
# apps/posts/views.py
from rest_framework.decorators import api_view
from rest_framework.response import Response
from rest_framework import status
from .models import Post
from .serializers import PostSerializer


@api_view(['GET', 'POST'])
def post_list(request):
    if request.method == 'GET':
        posts = Post.objects.filter(status='published')
        serializer = PostSerializer(posts, many=True)
        return Response(serializer.data)

    if request.method == 'POST':
        serializer = PostSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save(author=request.user)
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


@api_view(['GET', 'PUT', 'PATCH', 'DELETE'])
def post_detail(request, pk):
    post = get_object_or_404(Post, pk=pk)

    if request.method == 'GET':
        serializer = PostSerializer(post)
        return Response(serializer.data)

    elif request.method in ['PUT', 'PATCH']:
        serializer = PostSerializer(post, data=request.data, partial=(request.method == 'PATCH'))
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    elif request.method == 'DELETE':
        post.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```

---

## 18. DRF — Serializers

Serializers convert complex data (QuerySets, model instances) to native Python types (then JSON), and also validate incoming data.

### ModelSerializer (most common)

```python
# apps/posts/serializers.py
from rest_framework import serializers
from django.contrib.auth import get_user_model
from .models import Post, Comment, Tag, Category

User = get_user_model()


class TagSerializer(serializers.ModelSerializer):
    class Meta:
        model = Tag
        fields = ['id', 'name']


class CategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = Category
        fields = ['id', 'name', 'slug']


class AuthorSerializer(serializers.ModelSerializer):
    full_name = serializers.SerializerMethodField()

    class Meta:
        model = User
        fields = ['id', 'username', 'email', 'full_name', 'avatar']

    def get_full_name(self, obj):
        return obj.get_full_name() or obj.username


class CommentSerializer(serializers.ModelSerializer):
    author = AuthorSerializer(read_only=True)
    replies = serializers.SerializerMethodField()

    class Meta:
        model = Comment
        fields = ['id', 'author', 'content', 'created_at', 'replies']
        read_only_fields = ['author', 'created_at']

    def get_replies(self, obj):
        if obj.parent is None:
            qs = obj.replies.filter(is_approved=True)
            return CommentSerializer(qs, many=True).data
        return []


class PostListSerializer(serializers.ModelSerializer):
    """Lightweight serializer for list views."""
    author = AuthorSerializer(read_only=True)
    category = CategorySerializer(read_only=True)
    tags = TagSerializer(many=True, read_only=True)
    comment_count = serializers.IntegerField(source='comments.count', read_only=True)
    is_liked = serializers.SerializerMethodField()
    url = serializers.HyperlinkedIdentityField(view_name='post-detail')

    class Meta:
        model = Post
        fields = [
            'id', 'title', 'slug', 'excerpt', 'cover_image',
            'author', 'category', 'tags', 'status', 'is_featured',
            'view_count', 'read_time', 'comment_count', 'is_liked',
            'created_at', 'url',
        ]

    def get_is_liked(self, obj):
        request = self.context.get('request')
        if request and request.user.is_authenticated:
            return obj.likes.filter(user=request.user).exists()
        return False


class PostDetailSerializer(PostListSerializer):
    """Full serializer for detail views."""
    comments = serializers.SerializerMethodField()

    class Meta(PostListSerializer.Meta):
        fields = PostListSerializer.Meta.fields + ['content', 'comments', 'published_at']

    def get_comments(self, obj):
        qs = obj.comments.filter(is_approved=True, parent=None)
        return CommentSerializer(qs, many=True, context=self.context).data


class PostCreateUpdateSerializer(serializers.ModelSerializer):
    """Serializer for creating/updating posts."""
    tag_ids = serializers.PrimaryKeyRelatedField(
        queryset=Tag.objects.all(),
        many=True,
        write_only=True,
        required=False,
        source='tags',
    )

    class Meta:
        model = Post
        fields = ['title', 'content', 'excerpt', 'cover_image', 'status', 'category', 'tag_ids']

    def validate_title(self, value):
        if len(value) < 5:
            raise serializers.ValidationError('Title must be at least 5 characters.')
        return value.strip()

    def validate(self, attrs):
        if attrs.get('status') == 'published' and not attrs.get('content'):
            raise serializers.ValidationError({'content': 'Content required to publish.'})
        return attrs

    def create(self, validated_data):
        tags = validated_data.pop('tags', [])
        post = Post.objects.create(**validated_data)
        post.tags.set(tags)
        return post

    def update(self, instance, validated_data):
        tags = validated_data.pop('tags', None)
        for attr, value in validated_data.items():
            setattr(instance, attr, value)
        instance.save()
        if tags is not None:
            instance.tags.set(tags)
        return instance


# Plain Serializer (not tied to model)
class LoginSerializer(serializers.Serializer):
    email = serializers.EmailField()
    password = serializers.CharField(write_only=True, min_length=6)

    def validate(self, attrs):
        from django.contrib.auth import authenticate
        user = authenticate(email=attrs['email'], password=attrs['password'])
        if not user:
            raise serializers.ValidationError('Invalid credentials.')
        if not user.is_active:
            raise serializers.ValidationError('Account is deactivated.')
        attrs['user'] = user
        return attrs
```

### Serializer Fields

```python
serializers.CharField(max_length=200)
serializers.IntegerField(min_value=0, max_value=100)
serializers.FloatField()
serializers.DecimalField(max_digits=10, decimal_places=2)
serializers.BooleanField()
serializers.DateField()
serializers.DateTimeField()
serializers.EmailField()
serializers.URLField()
serializers.UUIDField()
serializers.ChoiceField(choices=[...])
serializers.ListField(child=serializers.CharField())
serializers.DictField()
serializers.JSONField()
serializers.ImageField()
serializers.FileField()

# Relationship fields
serializers.PrimaryKeyRelatedField(queryset=Category.objects.all())
serializers.StringRelatedField()              # Uses __str__
serializers.SlugRelatedField(slug_field='name', queryset=Tag.objects.all())
serializers.HyperlinkedRelatedField(view_name='category-detail', read_only=True)
serializers.SerializerMethodField()          # Custom computed field
serializers.HyperlinkedIdentityField(view_name='post-detail')

# Field options
serializers.CharField(
    required=True,           # Default True
    allow_null=True,
    allow_blank=True,
    read_only=True,          # Only for output
    write_only=True,         # Only for input (passwords)
    default='value',
    source='model_field',    # Map to different model field
    validators=[...],
)
```

---

## 19. DRF — Views & ViewSets

### APIView (most control)

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status, permissions


class PostListView(APIView):
    permission_classes = [permissions.IsAuthenticatedOrReadOnly]

    def get(self, request):
        posts = Post.objects.filter(status='published')
        serializer = PostListSerializer(posts, many=True, context={'request': request})
        return Response(serializer.data)

    def post(self, request):
        serializer = PostCreateUpdateSerializer(data=request.data, context={'request': request})
        if serializer.is_valid():
            serializer.save(author=request.user)
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


class PostDetailView(APIView):
    permission_classes = [permissions.IsAuthenticatedOrReadOnly]

    def get_object(self, pk):
        return get_object_or_404(Post, pk=pk)

    def get(self, request, pk):
        post = self.get_object(pk)
        serializer = PostDetailSerializer(post, context={'request': request})
        return Response(serializer.data)

    def put(self, request, pk):
        post = self.get_object(pk)
        serializer = PostCreateUpdateSerializer(post, data=request.data, context={'request': request})
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def patch(self, request, pk):
        post = self.get_object(pk)
        serializer = PostCreateUpdateSerializer(post, data=request.data, partial=True, context={'request': request})
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def delete(self, request, pk):
        post = self.get_object(pk)
        post.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```

### Generic Views (less boilerplate)

```python
from rest_framework import generics, mixins


class PostListCreateView(generics.ListCreateAPIView):
    queryset = Post.objects.filter(status='published').select_related('author', 'category').prefetch_related('tags')
    permission_classes = [permissions.IsAuthenticatedOrReadOnly]

    def get_serializer_class(self):
        if self.request.method == 'POST':
            return PostCreateUpdateSerializer
        return PostListSerializer

    def get_queryset(self):
        qs = super().get_queryset()
        category = self.request.query_params.get('category')
        if category:
            qs = qs.filter(category__slug=category)
        return qs

    def perform_create(self, serializer):
        serializer.save(author=self.request.user)


class PostRetrieveUpdateDestroyView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Post.objects.all()
    lookup_field = 'slug'              # Use slug instead of pk in URL

    def get_serializer_class(self):
        if self.request.method in ['PUT', 'PATCH']:
            return PostCreateUpdateSerializer
        return PostDetailSerializer

    def get_permissions(self):
        if self.request.method in ['PUT', 'PATCH', 'DELETE']:
            return [permissions.IsAuthenticated(), IsAuthorOrReadOnly()]
        return [permissions.AllowAny()]


# Available generic views:
# generics.ListAPIView             GET (list)
# generics.CreateAPIView           POST (create)
# generics.RetrieveAPIView         GET (detail)
# generics.UpdateAPIView           PUT/PATCH
# generics.DestroyAPIView          DELETE
# generics.ListCreateAPIView       GET + POST
# generics.RetrieveUpdateAPIView   GET + PUT/PATCH
# generics.RetrieveDestroyAPIView  GET + DELETE
# generics.RetrieveUpdateDestroyAPIView  GET + PUT/PATCH + DELETE
```

### ViewSets (most DRY — maps to router)

```python
from rest_framework import viewsets
from rest_framework.decorators import action
from rest_framework.response import Response


class PostViewSet(viewsets.ModelViewSet):
    """
    Provides: list, create, retrieve, update, partial_update, destroy
    """
    queryset = Post.objects.all().select_related('author', 'category')
    permission_classes = [permissions.IsAuthenticatedOrReadOnly]

    def get_serializer_class(self):
        if self.action in ['list']:
            return PostListSerializer
        if self.action in ['create', 'update', 'partial_update']:
            return PostCreateUpdateSerializer
        return PostDetailSerializer

    def get_queryset(self):
        qs = super().get_queryset()
        if self.request.user.is_authenticated:
            return qs
        return qs.filter(status='published')

    def perform_create(self, serializer):
        serializer.save(author=self.request.user)

    def perform_destroy(self, instance):
        if instance.author != self.request.user and not self.request.user.is_staff:
            raise PermissionDenied('You can only delete your own posts.')
        instance.delete()

    # Custom actions — adds extra URL endpoints
    @action(detail=True, methods=['post'], permission_classes=[permissions.IsAuthenticated])
    def publish(self, request, pk=None):
        post = self.get_object()
        post.status = 'published'
        post.save()
        return Response({'status': 'published'})

    @action(detail=True, methods=['post'], permission_classes=[permissions.IsAuthenticated])
    def like(self, request, pk=None):
        post = self.get_object()
        like, created = Like.objects.get_or_create(post=post, user=request.user)
        if not created:
            like.delete()
            return Response({'liked': False})
        return Response({'liked': True})

    @action(detail=False, methods=['get'], permission_classes=[permissions.IsAuthenticated])
    def my_posts(self, request):
        posts = Post.objects.filter(author=request.user)
        serializer = self.get_serializer(posts, many=True)
        return Response(serializer.data)

    @action(detail=False, methods=['get'])
    def featured(self, request):
        posts = Post.objects.filter(is_featured=True, status='published')
        serializer = PostListSerializer(posts, many=True, context={'request': request})
        return Response(serializer.data)


# Read-only ViewSet (only list and retrieve)
class CategoryViewSet(viewsets.ReadOnlyModelViewSet):
    queryset = Category.objects.all()
    serializer_class = CategorySerializer
```

---

## 20. DRF — Routers

Routers automatically generate URL patterns for ViewSets.

```python
# apps/posts/urls.py
from rest_framework.routers import DefaultRouter, SimpleRouter
from . import views

router = DefaultRouter()
# Generates: /posts/, /posts/{pk}/
# + custom actions: /posts/{pk}/publish/, /posts/{pk}/like/, /posts/featured/
router.register(r'posts', views.PostViewSet, basename='post')
router.register(r'categories', views.CategoryViewSet, basename='category')
router.register(r'comments', views.CommentViewSet, basename='comment')

urlpatterns = router.urls


# Nested routers (pip install drf-nested-routers)
# from rest_framework_nested import routers
# posts_router = routers.NestedSimpleRouter(router, r'posts', lookup='post')
# posts_router.register(r'comments', CommentViewSet, basename='post-comments')
# urlpatterns += posts_router.urls
```

### Generated URL patterns (DefaultRouter)

```
GET    /posts/              → list
POST   /posts/              → create
GET    /posts/{pk}/         → retrieve
PUT    /posts/{pk}/         → update
PATCH  /posts/{pk}/         → partial_update
DELETE /posts/{pk}/         → destroy
POST   /posts/{pk}/publish/ → publish (custom action, detail=True)
POST   /posts/{pk}/like/    → like (custom action, detail=True)
GET    /posts/featured/     → featured (custom action, detail=False)
GET    /posts/my_posts/     → my_posts (custom action, detail=False)
```

---

## 21. DRF — Authentication & Permissions

### Authentication Classes

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework.authentication.TokenAuthentication',
    ],
}
```

### Permission Classes

```python
# Built-in
from rest_framework.permissions import (
    AllowAny,                   # Anyone
    IsAuthenticated,            # Must be logged in
    IsAdminUser,                # Must be admin/staff
    IsAuthenticatedOrReadOnly,  # Read-only for anon, full for auth
)

# Custom permission
from rest_framework.permissions import BasePermission, SAFE_METHODS


class IsAuthorOrReadOnly(BasePermission):
    """Allow any read access, but only authors can write."""
    message = 'You must be the author to perform this action.'

    def has_permission(self, request, view):
        if request.method in SAFE_METHODS:  # GET, HEAD, OPTIONS
            return True
        return request.user.is_authenticated

    def has_object_permission(self, request, view, obj):
        if request.method in SAFE_METHODS:
            return True
        return obj.author == request.user


class IsOwner(BasePermission):
    def has_object_permission(self, request, view, obj):
        return obj.user == request.user


class IsAdminOrReadOnly(BasePermission):
    def has_permission(self, request, view):
        if request.method in SAFE_METHODS:
            return True
        return request.user.is_staff


# Apply per view
class PostListCreateView(generics.ListCreateAPIView):
    permission_classes = [IsAuthenticatedOrReadOnly]

# Or per viewset action
class PostViewSet(viewsets.ModelViewSet):
    def get_permissions(self):
        if self.action in ['list', 'retrieve']:
            return [AllowAny()]
        if self.action in ['create']:
            return [IsAuthenticated()]
        return [IsAuthenticated(), IsAuthorOrReadOnly()]
```

---

## 22. DRF — Filtering, Searching & Ordering

### Install

```bash
pip install django-filter
```

```python
# settings.py
INSTALLED_APPS = [..., 'django_filters']

REST_FRAMEWORK = {
    'DEFAULT_FILTER_BACKENDS': [
        'django_filters.rest_framework.DjangoFilterBackend',
        'rest_framework.filters.SearchFilter',
        'rest_framework.filters.OrderingFilter',
    ],
}
```

### FilterSet

```python
# apps/posts/filters.py
import django_filters
from .models import Post


class PostFilter(django_filters.FilterSet):
    title = django_filters.CharFilter(lookup_expr='icontains')
    author = django_filters.CharFilter(field_name='author__username', lookup_expr='icontains')
    status = django_filters.ChoiceFilter(choices=Post.STATUS_CHOICES)
    category = django_filters.CharFilter(field_name='category__slug')
    tag = django_filters.CharFilter(field_name='tags__name', lookup_expr='icontains')
    created_after = django_filters.DateFilter(field_name='created_at', lookup_expr='gte')
    created_before = django_filters.DateFilter(field_name='created_at', lookup_expr='lte')
    min_views = django_filters.NumberFilter(field_name='view_count', lookup_expr='gte')
    is_featured = django_filters.BooleanFilter()

    class Meta:
        model = Post
        fields = ['title', 'author', 'status', 'category', 'is_featured']


# In ViewSet
class PostViewSet(viewsets.ModelViewSet):
    filterset_class = PostFilter
    search_fields = ['title', 'content', 'author__username', 'tags__name']
    ordering_fields = ['created_at', 'view_count', 'title']
    ordering = ['-created_at']  # Default ordering

# Usage:
# GET /posts/?title=django
# GET /posts/?status=published&is_featured=true
# GET /posts/?search=python
# GET /posts/?ordering=-view_count
# GET /posts/?created_after=2024-01-01
```

---

## 23. DRF — Pagination

### Config

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 20,
}
```

### Custom Pagination Classes

```python
# apps/core/pagination.py
from rest_framework.pagination import (
    PageNumberPagination,
    LimitOffsetPagination,
    CursorPagination,
)


class StandardPageNumberPagination(PageNumberPagination):
    page_size = 20
    page_size_query_param = 'page_size'  # Allow client to override
    max_page_size = 100
    page_query_param = 'page'

    def get_paginated_response(self, data):
        from rest_framework.response import Response
        return Response({
            'pagination': {
                'count': self.page.paginator.count,
                'total_pages': self.page.paginator.num_pages,
                'current_page': self.page.number,
                'page_size': self.get_page_size(self.request),
                'next': self.get_next_link(),
                'previous': self.get_previous_link(),
            },
            'results': data,
        })


class LargeResultsSetPagination(PageNumberPagination):
    page_size = 100
    max_page_size = 1000


class SmallResultsSetPagination(PageNumberPagination):
    page_size = 5


class PostCursorPagination(CursorPagination):
    """Best for real-time feeds — uses cursor instead of page number."""
    page_size = 20
    ordering = '-created_at'


# Use per-view
class PostViewSet(viewsets.ModelViewSet):
    pagination_class = StandardPageNumberPagination
```

---

## 24. DRF — Throttling (Rate Limiting)

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle',
    ],
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/day',
        'user': '1000/day',
        'login': '5/minute',
        'uploads': '10/hour',
    }
}


# Custom throttle
from rest_framework.throttling import UserRateThrottle, AnonRateThrottle


class LoginRateThrottle(AnonRateThrottle):
    scope = 'login'


class UploadThrottle(UserRateThrottle):
    scope = 'uploads'


# Apply per view
class LoginView(APIView):
    throttle_classes = [LoginRateThrottle]


class UploadView(APIView):
    throttle_classes = [UploadThrottle]
```

---

## 25. JWT Authentication (SimpleJWT)

### Install

```bash
pip install djangorestframework-simplejwt
```

### Settings

```python
# settings.py
from datetime import timedelta

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=60),
    'REFRESH_TOKEN_LIFETIME': timedelta(days=7),
    'ROTATE_REFRESH_TOKENS': True,           # New refresh token on every refresh
    'BLACKLIST_AFTER_ROTATION': True,        # Old refresh token becomes invalid
    'ALGORITHM': 'HS256',
    'SIGNING_KEY': SECRET_KEY,
    'AUTH_HEADER_TYPES': ('Bearer',),
    'USER_ID_FIELD': 'id',
    'USER_ID_CLAIM': 'user_id',
    'TOKEN_OBTAIN_SERIALIZER': 'apps.users.serializers.CustomTokenObtainPairSerializer',
}

INSTALLED_APPS = [
    ...
    'rest_framework_simplejwt',
    'rest_framework_simplejwt.token_blacklist',  # For blacklisting
]
```

### URLs

```python
# config/urls.py
from rest_framework_simplejwt.views import (
    TokenObtainPairView,
    TokenRefreshView,
    TokenVerifyView,
    TokenBlacklistView,
)

urlpatterns = [
    path('api/v1/auth/token/', TokenObtainPairView.as_view(), name='token-obtain'),
    path('api/v1/auth/token/refresh/', TokenRefreshView.as_view(), name='token-refresh'),
    path('api/v1/auth/token/verify/', TokenVerifyView.as_view(), name='token-verify'),
    path('api/v1/auth/token/blacklist/', TokenBlacklistView.as_view(), name='token-blacklist'),
]
```

### Custom Token with Extra Claims

```python
# apps/users/serializers.py
from rest_framework_simplejwt.serializers import TokenObtainPairSerializer
from rest_framework_simplejwt.views import TokenObtainPairView


class CustomTokenObtainPairSerializer(TokenObtainPairSerializer):
    @classmethod
    def get_token(cls, user):
        token = super().get_token(user)

        # Add custom claims to token payload
        token['username'] = user.username
        token['email'] = user.email
        token['is_admin'] = user.is_staff
        token['roles'] = list(user.groups.values_list('name', flat=True))

        return token

    def validate(self, attrs):
        data = super().validate(attrs)
        # Add extra data to response body
        data['user'] = {
            'id': self.user.id,
            'username': self.user.username,
            'email': self.user.email,
        }
        return data


class CustomTokenObtainPairView(TokenObtainPairView):
    serializer_class = CustomTokenObtainPairSerializer
```

### Manual JWT Usage in Views

```python
from rest_framework_simplejwt.tokens import RefreshToken
from rest_framework_simplejwt.exceptions import TokenError, InvalidToken


def get_tokens_for_user(user):
    refresh = RefreshToken.for_user(user)
    return {
        'refresh': str(refresh),
        'access': str(refresh.access_token),
    }


class RegisterView(APIView):
    permission_classes = [AllowAny]

    def post(self, request):
        serializer = RegisterSerializer(data=request.data)
        if serializer.is_valid():
            user = serializer.save()
            tokens = get_tokens_for_user(user)
            return Response({
                'user': UserSerializer(user).data,
                **tokens,
            }, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


class LogoutView(APIView):
    permission_classes = [IsAuthenticated]

    def post(self, request):
        try:
            refresh_token = request.data['refresh_token']
            token = RefreshToken(refresh_token)
            token.blacklist()
            return Response({'message': 'Successfully logged out.'})
        except (TokenError, KeyError):
            return Response({'error': 'Invalid token.'}, status=status.HTTP_400_BAD_REQUEST)
```

---

## 26. Custom User Model

**Always define a custom user model before your first migration.** This is a best practice you cannot easily fix later.

### Define the Model

```python
# apps/users/models.py
from django.contrib.auth.models import AbstractUser, BaseUserManager
from django.db import models
import uuid


class UserManager(BaseUserManager):
    def create_user(self, email, password=None, **extra_fields):
        if not email:
            raise ValueError('Email is required.')
        email = self.normalize_email(email)
        user = self.model(email=email, **extra_fields)
        user.set_password(password)
        user.save(using=self._db)
        return user

    def create_superuser(self, email, password=None, **extra_fields):
        extra_fields.setdefault('is_staff', True)
        extra_fields.setdefault('is_superuser', True)
        extra_fields.setdefault('is_active', True)
        return self.create_user(email, password, **extra_fields)


class User(AbstractUser):
    # Remove username field and use email as identifier
    username = None
    email = models.EmailField(unique=True)

    # Additional fields
    uuid = models.UUIDField(default=uuid.uuid4, editable=False, unique=True)
    avatar = models.ImageField(upload_to='avatars/', blank=True, null=True)
    bio = models.TextField(max_length=500, blank=True)
    phone = models.CharField(max_length=20, blank=True)
    date_of_birth = models.DateField(null=True, blank=True)
    is_email_verified = models.BooleanField(default=False)
    last_seen = models.DateTimeField(null=True, blank=True)

    USERNAME_FIELD = 'email'           # Use email to log in
    REQUIRED_FIELDS = ['first_name', 'last_name']  # Required for createsuperuser

    objects = UserManager()

    def __str__(self):
        return self.email

    def get_full_name(self):
        return f'{self.first_name} {self.last_name}'.strip() or self.email


class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE, related_name='profile')
    website = models.URLField(blank=True)
    location = models.CharField(max_length=100, blank=True)
    twitter = models.CharField(max_length=100, blank=True)
    github = models.CharField(max_length=100, blank=True)

    def __str__(self):
        return f'{self.user.email} Profile'
```

```python
# settings.py
AUTH_USER_MODEL = 'users.User'
```

```python
# apps/users/admin.py
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin as BaseUserAdmin
from .models import User


@admin.register(User)
class UserAdmin(BaseUserAdmin):
    model = User
    list_display = ['email', 'first_name', 'last_name', 'is_staff', 'is_active']
    list_filter = ['is_staff', 'is_active', 'is_superuser']
    search_fields = ['email', 'first_name', 'last_name']
    ordering = ['email']

    fieldsets = (
        (None, {'fields': ('email', 'password')}),
        ('Personal Info', {'fields': ('first_name', 'last_name', 'avatar', 'bio', 'phone')}),
        ('Permissions', {'fields': ('is_active', 'is_staff', 'is_superuser', 'groups', 'user_permissions')}),
        ('Important Dates', {'fields': ('last_login', 'date_joined')}),
    )

    add_fieldsets = (
        (None, {
            'classes': ('wide',),
            'fields': ('email', 'first_name', 'last_name', 'password1', 'password2', 'is_staff', 'is_active'),
        }),
    )
```

---

## 27. Email

```python
# settings.py
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'smtp.gmail.com'
EMAIL_PORT = 587
EMAIL_USE_TLS = True
EMAIL_HOST_USER = 'your@gmail.com'
EMAIL_HOST_PASSWORD = 'app-password'
DEFAULT_FROM_EMAIL = 'My App <noreply@myapp.com>'

# For dev — print to console
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'
```

### Sending Email

```python
from django.core.mail import send_mail, send_mass_mail, EmailMessage, EmailMultiAlternatives


# Simple email
send_mail(
    subject='Welcome to My App',
    message='Hello, welcome!',
    from_email='noreply@myapp.com',
    recipient_list=['user@example.com'],
    fail_silently=False,
)


# HTML email
def send_html_email(user, subject, template_name, context):
    from django.template.loader import render_to_string
    from django.utils.html import strip_tags

    html_message = render_to_string(f'emails/{template_name}.html', context)
    plain_message = strip_tags(html_message)

    msg = EmailMultiAlternatives(
        subject=subject,
        body=plain_message,
        from_email='noreply@myapp.com',
        to=[user.email],
    )
    msg.attach_alternative(html_message, 'text/html')
    msg.send()


# Email with attachments
def send_invoice_email(user, pdf_path):
    email = EmailMessage(
        subject='Your Invoice',
        body='Please find your invoice attached.',
        from_email='billing@myapp.com',
        to=[user.email],
        cc=['accounts@myapp.com'],
        reply_to=['support@myapp.com'],
    )
    email.attach_file(pdf_path)                # Attach file from disk
    email.attach('report.txt', 'Content...', 'text/plain')  # Attach in-memory
    email.send()


# Mass email (single connection)
message1 = ('Subject 1', 'Message 1', 'from@example.com', ['user1@example.com'])
message2 = ('Subject 2', 'Message 2', 'from@example.com', ['user2@example.com'])
send_mass_mail([message1, message2], fail_silently=False)


# Password reset (using itsdangerous-style tokens)
def send_password_reset_email(user):
    from django.contrib.auth.tokens import default_token_generator
    from django.utils.http import urlsafe_base64_encode
    from django.utils.encoding import force_bytes

    uid = urlsafe_base64_encode(force_bytes(user.pk))
    token = default_token_generator.make_token(user)

    send_html_email(
        user=user,
        subject='Password Reset',
        template_name='password_reset',
        context={
            'user': user,
            'uid': uid,
            'token': token,
            'domain': 'myapp.com',
        },
    )
```

---

## 28. Caching

### Setup

```python
# settings.py — Memory cache (dev)
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
    }
}

# Redis cache (production)
CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://127.0.0.1:6379/1',
        'OPTIONS': {
            'CLIENT_CLASS': 'django_redis.client.DefaultClient',
            'SOCKET_CONNECT_TIMEOUT': 5,
            'SOCKET_TIMEOUT': 5,
        },
        'KEY_PREFIX': 'myapp',
        'TIMEOUT': 300,  # Default 5 minutes
    }
}
```

### Cache Usage

```python
from django.core.cache import cache

# Set
cache.set('my_key', 'my_value', timeout=300)    # 5 minutes
cache.set('my_key', {'data': [1, 2, 3]}, 60)    # Any picklable value
cache.set_many({'key1': 'val1', 'key2': 'val2'}, 60)

# Get
value = cache.get('my_key')                      # None if not found
value = cache.get('my_key', default='fallback')
values = cache.get_many(['key1', 'key2'])

# Delete
cache.delete('my_key')
cache.delete_many(['key1', 'key2'])
cache.clear()                                     # Clear all

# Get or set pattern
def get_popular_posts():
    key = 'popular_posts'
    posts = cache.get(key)
    if posts is None:
        posts = list(Post.objects.filter(status='published').order_by('-view_count')[:10].values())
        cache.set(key, posts, 600)
    return posts


# get_or_set shorthand
posts = cache.get_or_set('popular_posts', lambda: list(Post.objects.all()[:10].values()), 600)

# Atomic increment
cache.set('page_views', 0)
cache.incr('page_views')
cache.incr('page_views', 5)
cache.decr('page_views')
```

### Cache Decorators

```python
from django.views.decorators.cache import cache_page, never_cache
from django.utils.decorators import method_decorator


# Cache a view for 15 minutes
@cache_page(60 * 15)
def post_list(request):
    ...


# Class-based view
@method_decorator(cache_page(60 * 15), name='dispatch')
class PostListView(ListView):
    ...


# Never cache (private data)
@never_cache
def profile(request):
    ...


# Cache in URL patterns
from django.views.decorators.cache import cache_page

urlpatterns = [
    path('posts/', cache_page(60 * 15)(PostListView.as_view()), name='post-list'),
]
```

### DRF Cache

```python
from django.utils.decorators import method_decorator
from django.views.decorators.cache import cache_page
from django.views.decorators.vary import vary_on_headers


class PostViewSet(viewsets.ModelViewSet):
    @method_decorator(cache_page(60 * 5))
    @method_decorator(vary_on_headers('Authorization'))
    def list(self, request, *args, **kwargs):
        return super().list(request, *args, **kwargs)
```

---

## 29. Celery & Task Queues

### Install

```bash
pip install celery redis django-celery-results django-celery-beat
```

### Setup

```python
# config/celery.py
import os
from celery import Celery
from celery.schedules import crontab

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'config.settings.development')

app = Celery('config')
app.config_from_object('django.conf:settings', namespace='CELERY')
app.autodiscover_tasks()  # Auto-find tasks.py in all installed apps


# Periodic tasks
app.conf.beat_schedule = {
    'clean-expired-tokens-daily': {
        'task': 'apps.users.tasks.clean_expired_tokens',
        'schedule': crontab(hour=2, minute=0),  # 2am daily
    },
    'send-weekly-digest': {
        'task': 'apps.posts.tasks.send_weekly_digest',
        'schedule': crontab(day_of_week='monday', hour=8),
    },
    'update-trending-every-hour': {
        'task': 'apps.posts.tasks.update_trending_posts',
        'schedule': crontab(minute=0),  # Every hour
    },
}
```

```python
# config/__init__.py
from .celery import app as celery_app
__all__ = ('celery_app',)
```

### Define Tasks

```python
# apps/posts/tasks.py
from celery import shared_task
from celery.utils.log import get_task_logger

logger = get_task_logger(__name__)


@shared_task(bind=True, max_retries=3, default_retry_delay=60)
def send_post_notification(self, post_id, user_id):
    """Send notification email when a post is published."""
    try:
        from .models import Post
        from django.contrib.auth import get_user_model
        from django.core.mail import send_mail

        User = get_user_model()
        post = Post.objects.get(id=post_id)
        user = User.objects.get(id=user_id)

        send_mail(
            subject=f'New post: {post.title}',
            message=f'Check out this new post: {post.get_absolute_url()}',
            from_email='noreply@myapp.com',
            recipient_list=[user.email],
        )
        logger.info(f'Notification sent for post {post_id} to user {user_id}')

    except Exception as exc:
        logger.error(f'Failed to send notification: {exc}')
        raise self.retry(exc=exc)


@shared_task
def process_image(image_id):
    """Resize and optimize an uploaded image."""
    from PIL import Image
    from apps.media.models import MediaFile

    media = MediaFile.objects.get(id=image_id)
    img = Image.open(media.file.path)
    img.thumbnail((1200, 800))
    img.save(media.file.path, optimize=True, quality=85)
    return f'Processed image {image_id}'


@shared_task
def update_trending_posts():
    """Update trending post scores — runs hourly."""
    from .models import Post
    from django.db.models import F

    recent_posts = Post.objects.filter(status='published')
    for post in recent_posts:
        score = post.view_count * 0.5 + post.comments.count() * 2
        post.trending_score = score
        post.save(update_fields=['trending_score'])
    return f'Updated trending scores for {recent_posts.count()} posts'


@shared_task
def clean_expired_tokens():
    """Remove expired JWT tokens from blacklist."""
    from rest_framework_simplejwt.token_blacklist.models import OutstandingToken, BlacklistedToken
    from django.utils import timezone

    expired = OutstandingToken.objects.filter(expires_at__lt=timezone.now())
    count = expired.count()
    expired.delete()
    return f'Cleaned {count} expired tokens'
```

### Using Tasks

```python
# In views or signals
from apps.posts.tasks import send_post_notification, process_image

# Queue immediately (async)
send_post_notification.delay(post_id=post.id, user_id=user.id)

# Queue with delay (countdown in seconds)
send_post_notification.apply_async(
    args=[post.id, user.id],
    countdown=60,  # Run after 60 seconds
)

# Queue at specific time
from datetime import datetime, timedelta
eta = datetime.utcnow() + timedelta(hours=1)
send_post_notification.apply_async(args=[post.id, user.id], eta=eta)

# Run synchronously (for testing)
send_post_notification.apply(args=[post.id, user.id])

# Check task status
result = send_post_notification.delay(post.id, user.id)
print(result.id)          # Task ID
print(result.status)      # PENDING, STARTED, SUCCESS, FAILURE
print(result.result)      # Return value if successful
result.get(timeout=10)    # Wait for result
```

### Run Celery

```bash
# Start worker
celery -A config worker --loglevel=info

# Start beat (scheduler)
celery -A config beat --loglevel=info

# Start flower (monitoring UI)
pip install flower
celery -A config flower --port=5555
```

---

## 30. Django Channels (WebSockets)

### Install

```bash
pip install channels channels-redis daphne
```

### Setup

```python
# settings.py
INSTALLED_APPS = [
    'daphne',       # Must be first
    ...
    'channels',
]

ASGI_APPLICATION = 'config.asgi.application'

CHANNEL_LAYERS = {
    'default': {
        'BACKEND': 'channels_redis.core.RedisChannelLayer',
        'CONFIG': {'hosts': [('127.0.0.1', 6379)]},
    }
}
```

```python
# config/asgi.py
import os
from django.core.asgi import get_asgi_application
from channels.routing import ProtocolTypeRouter, URLRouter
from channels.auth import AuthMiddlewareStack
import apps.chat.routing

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'config.settings.development')

application = ProtocolTypeRouter({
    'http': get_asgi_application(),
    'websocket': AuthMiddlewareStack(
        URLRouter(apps.chat.routing.websocket_urlpatterns)
    ),
})
```

```python
# apps/chat/routing.py
from django.urls import re_path
from . import consumers

websocket_urlpatterns = [
    re_path(r'ws/chat/(?P<room_name>\w+)/$', consumers.ChatConsumer.as_asgi()),
    re_path(r'ws/notifications/$', consumers.NotificationConsumer.as_asgi()),
]
```

### WebSocket Consumer

```python
# apps/chat/consumers.py
import json
from channels.generic.websocket import AsyncWebsocketConsumer
from channels.db import database_sync_to_async


class ChatConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        self.room_name = self.scope['url_route']['kwargs']['room_name']
        self.room_group_name = f'chat_{self.room_name}'
        self.user = self.scope['user']

        if not self.user.is_authenticated:
            await self.close()
            return

        # Join room group
        await self.channel_layer.group_add(self.room_group_name, self.channel_name)
        await self.accept()

        # Notify room of new user
        await self.channel_layer.group_send(
            self.room_group_name,
            {'type': 'user_join', 'username': self.user.username}
        )

    async def disconnect(self, close_code):
        await self.channel_layer.group_discard(self.room_group_name, self.channel_name)

    async def receive(self, text_data):
        data = json.loads(text_data)
        message = data.get('message', '')

        # Save message to DB
        await self.save_message(message)

        # Broadcast to group
        await self.channel_layer.group_send(
            self.room_group_name,
            {
                'type': 'chat_message',
                'message': message,
                'username': self.user.username,
            }
        )

    # Handler for 'chat_message' type events from group_send
    async def chat_message(self, event):
        await self.send(text_data=json.dumps({
            'type': 'message',
            'message': event['message'],
            'username': event['username'],
        }))

    async def user_join(self, event):
        await self.send(text_data=json.dumps({
            'type': 'user_join',
            'username': event['username'],
        }))

    @database_sync_to_async
    def save_message(self, message):
        from .models import Message
        return Message.objects.create(
            room=self.room_name,
            author=self.user,
            content=message,
        )


# Sending from outside consumers (e.g., from a view)
from channels.layers import get_channel_layer
from asgiref.sync import async_to_sync

def send_notification(user_id, message):
    channel_layer = get_channel_layer()
    async_to_sync(channel_layer.group_send)(
        f'notifications_{user_id}',
        {'type': 'send_notification', 'message': message}
    )
```

---

## 31. File Uploads

### Model with FileField/ImageField

```python
# models.py
import os
import uuid
from django.db import models


def upload_to(instance, filename):
    ext = filename.split('.')[-1]
    filename = f'{uuid.uuid4()}.{ext}'
    return os.path.join('uploads', instance.__class__.__name__.lower(), filename)


class Document(models.Model):
    title = models.CharField(max_length=200)
    file = models.FileField(upload_to=upload_to)
    image = models.ImageField(upload_to='images/%Y/%m/')
    uploaded_by = models.ForeignKey(User, on_delete=models.CASCADE)
    created_at = models.DateTimeField(auto_now_add=True)

    def delete(self, *args, **kwargs):
        # Delete file from disk on model delete
        self.file.delete(save=False)
        super().delete(*args, **kwargs)
```

### Upload View (DRF)

```python
# serializers.py
from rest_framework import serializers
from .models import Document


class DocumentSerializer(serializers.ModelSerializer):
    file_url = serializers.SerializerMethodField()

    class Meta:
        model = Document
        fields = ['id', 'title', 'file', 'image', 'file_url', 'created_at']
        read_only_fields = ['uploaded_by', 'created_at']

    def get_file_url(self, obj):
        request = self.context.get('request')
        if obj.file and request:
            return request.build_absolute_uri(obj.file.url)
        return None

    def validate_file(self, value):
        max_size = 10 * 1024 * 1024  # 10 MB
        if value.size > max_size:
            raise serializers.ValidationError('File size cannot exceed 10 MB.')

        allowed_types = ['application/pdf', 'application/msword', 'text/plain']
        if value.content_type not in allowed_types:
            raise serializers.ValidationError('File type not allowed.')

        return value


# views.py
from rest_framework.parsers import MultiPartParser, FormParser


class DocumentUploadView(generics.CreateAPIView):
    serializer_class = DocumentSerializer
    permission_classes = [IsAuthenticated]
    parser_classes = [MultiPartParser, FormParser]  # Required for file uploads

    def perform_create(self, serializer):
        serializer.save(uploaded_by=self.request.user)
```

### Image Processing with Pillow

```python
from PIL import Image
import io
from django.core.files.base import ContentFile


def process_avatar(image_file, size=(200, 200)):
    img = Image.open(image_file)

    # Convert to RGB (handles PNG transparency)
    if img.mode != 'RGB':
        img = img.convert('RGB')

    # Crop to square
    min_dim = min(img.size)
    left = (img.width - min_dim) // 2
    top = (img.height - min_dim) // 2
    img = img.crop((left, top, left + min_dim, top + min_dim))

    # Resize
    img = img.resize(size, Image.LANCZOS)

    # Save to bytes
    output = io.BytesIO()
    img.save(output, format='JPEG', quality=85, optimize=True)
    output.seek(0)

    return ContentFile(output.read(), name='avatar.jpg')
```

---

## 32. Testing

### Setup

```python
# settings/testing.py
from .base import *

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': ':memory:',  # Fast in-memory DB
    }
}

EMAIL_BACKEND = 'django.core.mail.backends.locmem.EmailBackend'
CELERY_TASK_ALWAYS_EAGER = True  # Run tasks synchronously in tests
PASSWORD_HASHERS = ['django.contrib.auth.hashers.MD5PasswordHasher']  # Fast hashing
```

### Unit Tests

```python
# apps/posts/tests/test_models.py
from django.test import TestCase
from django.contrib.auth import get_user_model
from apps.posts.models import Post, Category

User = get_user_model()


class PostModelTest(TestCase):

    @classmethod
    def setUpTestData(cls):
        """Run once for the entire test class — faster than setUp."""
        cls.user = User.objects.create_user(
            email='test@example.com',
            password='testpass123',
        )
        cls.category = Category.objects.create(name='Tech')

    def setUp(self):
        """Run before each test method."""
        self.post = Post.objects.create(
            title='Test Post',
            content='Test content',
            author=self.user,
            category=self.category,
        )

    def test_post_creation(self):
        self.assertEqual(self.post.title, 'Test Post')
        self.assertEqual(self.post.author, self.user)
        self.assertEqual(self.post.status, Post.STATUS_DRAFT)

    def test_slug_auto_generation(self):
        self.assertEqual(self.post.slug, 'test-post')

    def test_read_time_calculation(self):
        long_content = 'word ' * 400  # 400 words
        post = Post.objects.create(
            title='Long Post',
            content=long_content,
            author=self.user,
        )
        self.assertEqual(post.read_time, 2)  # 400/200 = 2 minutes

    def test_is_published_property(self):
        self.assertFalse(self.post.is_published)
        self.post.status = Post.STATUS_PUBLISHED
        self.post.save()
        self.assertTrue(self.post.is_published)

    def test_str_representation(self):
        self.assertEqual(str(self.post), 'Test Post')

    def test_get_absolute_url(self):
        url = self.post.get_absolute_url()
        self.assertIn('test-post', url)
```

### View Tests

```python
# apps/posts/tests/test_views.py
from django.test import TestCase, Client
from django.urls import reverse
from rest_framework.test import APITestCase, APIClient
from rest_framework import status


class PostAPITest(APITestCase):

    def setUp(self):
        self.client = APIClient()
        self.user = User.objects.create_user(email='test@example.com', password='pass123')
        self.other_user = User.objects.create_user(email='other@example.com', password='pass123')

        self.post = Post.objects.create(
            title='My Post',
            content='Content here',
            author=self.user,
            status='published',
        )

        self.url_list = reverse('post-list')
        self.url_detail = reverse('post-detail', kwargs={'pk': self.post.pk})

    def authenticate(self, user=None):
        user = user or self.user
        self.client.force_authenticate(user=user)

    def test_get_post_list_unauthenticated(self):
        response = self.client.get(self.url_list)
        self.assertEqual(response.status_code, status.HTTP_200_OK)

    def test_create_post_requires_auth(self):
        response = self.client.post(self.url_list, {'title': 'New', 'content': 'Content'})
        self.assertEqual(response.status_code, status.HTTP_401_UNAUTHORIZED)

    def test_create_post_authenticated(self):
        self.authenticate()
        data = {'title': 'New Post', 'content': 'Some content', 'status': 'draft'}
        response = self.client.post(self.url_list, data, format='json')
        self.assertEqual(response.status_code, status.HTTP_201_CREATED)
        self.assertEqual(response.data['title'], 'New Post')
        self.assertEqual(response.data['author']['email'], self.user.email)

    def test_update_post_author_only(self):
        self.authenticate(self.other_user)
        response = self.client.patch(self.url_detail, {'title': 'Hacked'}, format='json')
        self.assertEqual(response.status_code, status.HTTP_403_FORBIDDEN)

    def test_delete_post_as_author(self):
        self.authenticate()
        response = self.client.delete(self.url_detail)
        self.assertEqual(response.status_code, status.HTTP_204_NO_CONTENT)
        self.assertFalse(Post.objects.filter(pk=self.post.pk).exists())

    def test_post_list_pagination(self):
        for i in range(25):
            Post.objects.create(title=f'Post {i}', content='...', author=self.user, status='published')
        response = self.client.get(self.url_list)
        self.assertIn('count', response.data)
        self.assertIn('results', response.data)
        self.assertEqual(len(response.data['results']), 20)  # PAGE_SIZE

    def test_filter_by_status(self):
        response = self.client.get(self.url_list, {'status': 'published'})
        self.assertEqual(response.status_code, status.HTTP_200_OK)
        for post in response.data['results']:
            self.assertEqual(post['status'], 'published')


class PostViewTest(TestCase):
    """Testing Django template views."""

    def setUp(self):
        self.client = Client()
        self.user = User.objects.create_user(email='test@example.com', password='pass123')

    def test_post_list_view(self):
        response = self.client.get(reverse('post-list'))
        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, 'posts/list.html')

    def test_login_required_redirect(self):
        response = self.client.get(reverse('post-create'))
        self.assertRedirects(response, f'/login/?next=/posts/create/')

    def test_create_post_as_logged_in_user(self):
        self.client.login(email='test@example.com', password='pass123')
        response = self.client.post(reverse('post-create'), {
            'title': 'New Post',
            'content': 'Content here',
            'status': 'draft',
        })
        self.assertEqual(response.status_code, 302)  # Redirect after success
        self.assertTrue(Post.objects.filter(title='New Post').exists())
```

### Factory Boy (test data generation)

```bash
pip install factory-boy
```

```python
# tests/factories.py
import factory
from django.contrib.auth import get_user_model
from apps.posts.models import Post, Category

User = get_user_model()


class UserFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = User

    email = factory.Sequence(lambda n: f'user{n}@example.com')
    first_name = factory.Faker('first_name')
    last_name = factory.Faker('last_name')
    password = factory.PostGenerationMethodCall('set_password', 'testpass123')


class CategoryFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = Category

    name = factory.Faker('word')


class PostFactory(factory.django.DjangoModelFactory):
    class Meta:
        model = Post

    title = factory.Faker('sentence', nb_words=6)
    content = factory.Faker('text', max_nb_chars=1000)
    author = factory.SubFactory(UserFactory)
    category = factory.SubFactory(CategoryFactory)
    status = 'published'

    @factory.post_generation
    def tags(self, create, extracted, **kwargs):
        if not create or not extracted:
            return
        self.tags.add(*extracted)


# Usage in tests
user = UserFactory()
post = PostFactory(author=user, status='draft')
posts = PostFactory.create_batch(10)  # Create 10 posts
```

### Run Tests

```bash
python manage.py test                         # All tests
python manage.py test apps.posts              # Specific app
python manage.py test apps.posts.tests.test_views  # Specific file
python manage.py test apps.posts.tests.test_views.PostAPITest  # Specific class
python manage.py test apps.posts.tests.test_views.PostAPITest.test_create_post  # Specific method
python manage.py test --verbosity=2           # Verbose output
python manage.py test --keepdb                # Reuse DB (faster)
python manage.py test --failfast              # Stop on first failure

# With pytest-django
pip install pytest-django pytest-cov
pytest
pytest --cov=apps
pytest apps/posts/
```

---

## 33. Management Commands

```python
# apps/posts/management/__init__.py       (empty)
# apps/posts/management/commands/__init__.py  (empty)
# apps/posts/management/commands/seed_posts.py

from django.core.management.base import BaseCommand, CommandError
from django.contrib.auth import get_user_model
from apps.posts.models import Post, Category, Tag
import random

User = get_user_model()


class Command(BaseCommand):
    help = 'Seed the database with sample posts'

    def add_arguments(self, parser):
        parser.add_argument('count', nargs='?', type=int, default=10, help='Number of posts to create')
        parser.add_argument('--clear', action='store_true', help='Clear existing posts first')
        parser.add_argument('--author', type=str, help='Author email')

    def handle(self, *args, **options):
        if options['clear']:
            Post.objects.all().delete()
            self.stdout.write(self.style.WARNING('Cleared all posts.'))

        count = options['count']

        # Get or create author
        if options['author']:
            try:
                author = User.objects.get(email=options['author'])
            except User.DoesNotExist:
                raise CommandError(f"User '{options['author']}' does not exist.")
        else:
            author, _ = User.objects.get_or_create(
                email='seed@example.com',
                defaults={'first_name': 'Seed', 'last_name': 'User'},
            )
            if _:
                author.set_password('password123')
                author.save()

        # Create categories
        categories = []
        for name in ['Tech', 'Science', 'Culture', 'Sports']:
            cat, _ = Category.objects.get_or_create(name=name)
            categories.append(cat)

        # Create posts
        created = 0
        for i in range(count):
            post = Post.objects.create(
                title=f'Sample Post {i + 1}',
                content=f'This is the content of sample post {i + 1}. ' * 50,
                author=author,
                category=random.choice(categories),
                status=random.choice(['draft', 'published', 'published']),
            )
            created += 1

        self.stdout.write(self.style.SUCCESS(f'Successfully created {created} posts.'))
```

```bash
# Usage
python manage.py seed_posts
python manage.py seed_posts 50
python manage.py seed_posts 20 --clear
python manage.py seed_posts --author admin@example.com
```

---

## 34. Security Best Practices

```python
# settings/production.py

# ✅ Always in production
DEBUG = False
ALLOWED_HOSTS = ['yourdomain.com', 'www.yourdomain.com']
SECRET_KEY = os.environ['SECRET_KEY']     # Never hardcode!

# ✅ HTTPS
SECURE_SSL_REDIRECT = True
SECURE_HSTS_SECONDS = 31536000
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True

# ✅ Cookies
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
SESSION_COOKIE_HTTPONLY = True
CSRF_COOKIE_HTTPONLY = True

# ✅ Clickjacking
X_FRAME_OPTIONS = 'DENY'

# ✅ Content sniffing
SECURE_CONTENT_TYPE_NOSNIFF = True

# ✅ XSS
SECURE_BROWSER_XSS_FILTER = True

# ✅ Referrer
SECURE_REFERRER_POLICY = 'strict-origin-when-cross-origin'
```

```python
# Common security patterns

# 1. Use get_user_model() — never import User directly
from django.contrib.auth import get_user_model
User = get_user_model()

# 2. Use Django's ORM — prevents SQL injection automatically
User.objects.filter(email=email)   # ✅ Safe
# Never do this:
# User.objects.raw(f"SELECT * FROM users WHERE email='{email}'")  # ❌ Dangerous

# 3. CSRF token in all forms
# {% csrf_token %} in every POST form

# 4. Validate all uploads
def validate_image(value):
    from django.core.exceptions import ValidationError
    if value.size > 5 * 1024 * 1024:
        raise ValidationError('Image file too large (max 5 MB).')
    if not value.name.lower().endswith(('.png', '.jpg', '.jpeg', '.gif', '.webp')):
        raise ValidationError('Unsupported image format.')

# 5. Use environment variables for secrets
import os
SECRET_KEY = os.environ['SECRET_KEY']
DATABASE_URL = os.environ['DATABASE_URL']

# 6. Rate limit sensitive endpoints
class LoginView(APIView):
    throttle_classes = [LoginRateThrottle]  # 5/min

# 7. Log security events
import logging
logger = logging.getLogger('security')

def login_view(request):
    user = authenticate(...)
    if not user:
        logger.warning(f'Failed login: {request.POST.get("email")} from {request.META.get("REMOTE_ADDR")}')
```

---

## 35. Deployment

### Gunicorn

```bash
pip install gunicorn

# Run
gunicorn config.wsgi:application
gunicorn config.wsgi:application -w 4 -b 0.0.0.0:8000

# For async (Channels)
pip install daphne
daphne -b 0.0.0.0 -p 8000 config.asgi:application
```

### `Procfile`

```
web: gunicorn config.wsgi:application --workers 3 --timeout 120
worker: celery -A config worker --loglevel=info
beat: celery -A config beat --loglevel=info
```

### `Dockerfile`

```dockerfile
FROM python:3.11-slim

WORKDIR /app

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

RUN apt-get update && apt-get install -y \
    build-essential libpq-dev \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

RUN python manage.py collectstatic --noinput

EXPOSE 8000

CMD ["gunicorn", "config.wsgi:application", "--bind", "0.0.0.0:8000", "--workers", "3"]
```

### `docker-compose.yml`

```yaml
version: '3.9'

services:
  web:
    build: .
    command: gunicorn config.wsgi:application --bind 0.0.0.0:8000 --workers 3
    ports:
      - "8000:8000"
    volumes:
      - .:/app
      - static_volume:/app/staticfiles
      - media_volume:/app/media
    env_file:
      - .env
    depends_on:
      - db
      - redis

  db:
    image: postgres:15-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data/
    env_file:
      - .env
    environment:
      - POSTGRES_DB=${DB_NAME}
      - POSTGRES_USER=${DB_USER}
      - POSTGRES_PASSWORD=${DB_PASSWORD}
    ports:
      - "5432:5432"

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  celery_worker:
    build: .
    command: celery -A config worker --loglevel=info --concurrency=4
    volumes:
      - .:/app
    env_file:
      - .env
    depends_on:
      - db
      - redis

  celery_beat:
    build: .
    command: celery -A config beat --loglevel=info --scheduler django_celery_beat.schedulers:DatabaseScheduler
    volumes:
      - .:/app
    env_file:
      - .env
    depends_on:
      - db
      - redis

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
      - static_volume:/app/staticfiles
      - media_volume:/app/media
    depends_on:
      - web

volumes:
  postgres_data:
  static_volume:
  media_volume:
```

### Nginx Config

```nginx
upstream django {
    server web:8000;
}

server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl;
    server_name yourdomain.com www.yourdomain.com;

    # SSL config here

    client_max_body_size 20M;

    location / {
        proxy_pass http://django;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /static/ {
        alias /app/staticfiles/;
        expires 30d;
        add_header Cache-Control "public, immutable";
    }

    location /media/ {
        alias /app/media/;
        expires 7d;
    }

    location /ws/ {
        proxy_pass http://django;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

### Pre-deployment Checklist

```bash
# 1. Set DEBUG=False in production
# 2. Set a strong SECRET_KEY
# 3. Set ALLOWED_HOSTS properly
# 4. Run collectstatic
python manage.py collectstatic --noinput

# 5. Run migrations
python manage.py migrate

# 6. Create superuser
python manage.py createsuperuser

# 7. Check for issues
python manage.py check --deploy

# 8. Test all env variables are set
python manage.py shell -c "from django.conf import settings; print(settings.DEBUG)"
```

---

## 36. Common Patterns & Tips

### Generic API Response

```python
# apps/core/responses.py
from rest_framework.response import Response
from rest_framework import status


def success_response(data=None, message='Success', status_code=status.HTTP_200_OK):
    return Response({'success': True, 'message': message, 'data': data}, status=status_code)


def error_response(message='Error', errors=None, status_code=status.HTTP_400_BAD_REQUEST):
    return Response({'success': False, 'message': message, 'errors': errors}, status=status_code)


def paginated_response(serializer_data, paginator, page):
    return Response({
        'success': True,
        'data': serializer_data,
        'pagination': {
            'count': paginator.count,
            'total_pages': paginator.num_pages,
            'current_page': page.number,
            'next': page.next_page_number() if page.has_next() else None,
            'previous': page.previous_page_number() if page.has_previous() else None,
        }
    })
```

### Select Related Optimization

```python
# ❌ N+1 query problem — runs 1 query per post for author
posts = Post.objects.all()
for post in posts:
    print(post.author.username)  # New query each time!

# ✅ select_related — one JOIN for ForeignKey and OneToOneField
posts = Post.objects.select_related('author', 'category').all()

# ✅ prefetch_related — separate optimized query for M2M and reverse FK
posts = Post.objects.prefetch_related('tags', 'comments').all()

# ✅ Combine both for complex queries
posts = Post.objects.select_related('author', 'category')\
                    .prefetch_related('tags', Prefetch(
                        'comments',
                        queryset=Comment.objects.filter(is_approved=True).select_related('author')
                    ))
```

### Using `only()` and `defer()` for Performance

```python
# Only load needed fields
posts = Post.objects.only('id', 'title', 'slug', 'author_id').all()

# Load everything except heavy content field
posts = Post.objects.defer('content').all()
```

### Signals for Auto-Profile Creation

```python
# This pattern is so common it deserves its own mention
from django.db.models.signals import post_save
from django.dispatch import receiver

@receiver(post_save, sender=User)
def create_user_profile(sender, instance, created, **kwargs):
    if created:
        Profile.objects.create(user=instance)

@receiver(post_save, sender=User)
def save_user_profile(sender, instance, **kwargs):
    instance.profile.save()
```

### Environment-based settings loading

```python
# manage.py / wsgi.py / asgi.py
import os

env = os.environ.get('DJANGO_ENV', 'development')
os.environ.setdefault('DJANGO_SETTINGS_MODULE', f'config.settings.{env}')
```

### Useful Third-Party Packages

```bash
# Enhanced shell (shell_plus, graph_models, etc.)
pip install django-extensions

# Debug toolbar
pip install django-debug-toolbar

# Country/timezone fields
pip install django-countries django-timezone-field

# Phone number field
pip install django-phonenumber-field phonenumbers

# Money fields
pip install django-money

# Soft delete
pip install django-safedelete

# Full-text search
pip install django-watson

# Tree structures (categories, comments)
pip install django-mptt

# Social auth (Google, Facebook, GitHub)
pip install social-auth-app-django

# REST API versioning
pip install djangorestframework-api-key

# Djoser (auth endpoints for DRF — register, login, activate, etc.)
pip install djoser

# DRF Spectacular (OpenAPI/Swagger docs)
pip install drf-spectacular

# Serve static in production without Nginx
pip install whitenoise
```

### DRF Spectacular (Swagger Docs)

```bash
pip install drf-spectacular
```

```python
# settings.py
INSTALLED_APPS = [..., 'drf_spectacular']

REST_FRAMEWORK = {
    'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',
}

SPECTACULAR_SETTINGS = {
    'TITLE': 'My API',
    'DESCRIPTION': 'API documentation',
    'VERSION': '1.0.0',
    'SERVE_INCLUDE_SCHEMA': False,
}

# urls.py
from drf_spectacular.views import SpectacularAPIView, SpectacularSwaggerView, SpectacularRedocView

urlpatterns = [
    path('api/schema/', SpectacularAPIView.as_view(), name='schema'),
    path('api/docs/', SpectacularSwaggerView.as_view(url_name='schema'), name='swagger-ui'),
    path('api/redoc/', SpectacularRedocView.as_view(url_name='schema'), name='redoc'),
]
```

---

> 📌 **Tip**: Bookmark this file and refer to it whenever you build something new in Django. Every concept here is something you will encounter in real-world production projects.

> 🔗 **Official Docs**: [https://docs.djangoproject.com](https://docs.djangoproject.com)
> 🔗 **DRF Docs**: [https://www.django-rest-framework.org](https://www.django-rest-framework.org)
> 🔗 **SimpleJWT Docs**: [https://django-rest-framework-simplejwt.readthedocs.io](https://django-rest-framework-simplejwt.readthedocs.io)
> 🔗 **Celery Docs**: [https://docs.celeryq.dev](https://docs.celeryq.dev)