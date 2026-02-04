# Django Complete Notes

## Table of Contents
1. [Introduction to Django](#introduction-to-django)
2. [Installation and Setup](#installation-and-setup)
3. [Project Structure](#project-structure)
4. [Django Architecture (MTV)](#django-architecture-mtv)
5. [Models](#models)
6. [Views](#views)
7. [Templates](#templates)
8. [URLs and Routing](#urls-and-routing)
9. [Forms](#forms)
10. [Admin Interface](#admin-interface)
11. [Authentication](#authentication)
12. [Middleware](#middleware)
13. [Static and Media Files](#static-and-media-files)
14. [Database and ORM](#database-and-orm)
15. [Class-Based Views](#class-based-views)
16. [REST Framework](#rest-framework)
17. [Security](#security)
18. [Testing](#testing)
19. [Deployment](#deployment)
20. [Best Practices](#best-practices)

---

## Introduction to Django

Django is a high-level Python web framework that encourages rapid development and clean, pragmatic design. It follows the "batteries-included" philosophy, providing most things developers need out of the box.

### Key Features
- **Fast Development**: Built-in features reduce development time
- **Secure**: Helps avoid common security mistakes
- **Scalable**: Can handle heavy traffic
- **Versatile**: Used for various types of applications
- **ORM**: Object-Relational Mapping for database operations
- **Admin Interface**: Automatic admin panel generation
- **URL Routing**: Clean and elegant URL design

### Philosophy
- **DRY (Don't Repeat Yourself)**: Reduce code duplication
- **Explicit is better than implicit**: Clear and readable code
- **Loose coupling**: Components should be independent
- **Convention over configuration**: Sensible defaults

---

## Installation and Setup

### Installing Django

```bash
# Install Django using pip
pip install django

# Install specific version
pip install django==4.2

# Verify installation
python -m django --version
```

### Creating a Project

```bash
# Create new Django project
django-admin startproject myproject

# Project structure created:
# myproject/
#     manage.py
#     myproject/
#         __init__.py
#         settings.py
#         urls.py
#         asgi.py
#         wsgi.py
```

### Creating an App

```bash
# Navigate to project directory
cd myproject

# Create new app
python manage.py startapp myapp

# App structure:
# myapp/
#     __init__.py
#     admin.py
#     apps.py
#     models.py
#     tests.py
#     views.py
#     migrations/
```

### Running Development Server

```bash
# Start development server
python manage.py runserver

# Run on specific port
python manage.py runserver 8080

# Run on specific IP and port
python manage.py runserver 0.0.0.0:8000
```

---

## Project Structure

### Main Project Files

**manage.py**: Command-line utility for administrative tasks
- Run development server
- Create migrations
- Run tests
- Create superuser

**settings.py**: Configuration for Django project
- Database settings
- Installed apps
- Middleware
- Templates configuration
- Static files
- Security settings

**urls.py**: URL declarations (URL routing)

**wsgi.py**: Entry point for WSGI-compatible web servers

**asgi.py**: Entry point for ASGI-compatible web servers

### App Structure

**models.py**: Database models
**views.py**: View functions/classes
**admin.py**: Admin interface configuration
**apps.py**: App configuration
**tests.py**: Test cases
**urls.py**: App-specific URL patterns (created manually)
**migrations/**: Database migration files

---

## Django Architecture (MTV)

Django follows the MTV (Model-Template-View) pattern, similar to MVC:

### Model
- Represents data structure
- Handles database operations
- Defines relationships between data
- Contains business logic

### Template
- Handles presentation logic
- HTML files with Django template language
- Renders data to the user

### View
- Handles user requests
- Contains application logic
- Fetches data from models
- Passes data to templates

### Flow:
```
User Request → URLs → View → Model (if needed) → Template → Response
```

---

## Models

Models define the structure of your database tables.

### Basic Model

```python
from django.db import models

class Article(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    published_date = models.DateTimeField(auto_now_add=True)
    updated_date = models.DateTimeField(auto_now=True)
    author = models.ForeignKey('auth.User', on_delete=models.CASCADE)
    is_published = models.BooleanField(default=False)
    
    def __str__(self):
        return self.title
    
    class Meta:
        ordering = ['-published_date']
        verbose_name_plural = "Articles"
```

### Field Types

**Text Fields:**
- `CharField(max_length)`: Short text
- `TextField()`: Long text
- `EmailField()`: Email validation
- `URLField()`: URL validation
- `SlugField()`: URL-friendly text

**Numeric Fields:**
- `IntegerField()`: Integer numbers
- `FloatField()`: Floating point numbers
- `DecimalField(max_digits, decimal_places)`: Precise decimals
- `PositiveIntegerField()`: Positive integers only

**Date/Time Fields:**
- `DateField()`: Date
- `TimeField()`: Time
- `DateTimeField()`: Date and time
- `DurationField()`: Time duration

**Boolean:**
- `BooleanField()`: True/False

**Relationships:**
- `ForeignKey()`: Many-to-one
- `ManyToManyField()`: Many-to-many
- `OneToOneField()`: One-to-one

**Files:**
- `FileField()`: File upload
- `ImageField()`: Image upload
- `FilePathField()`: File path selection

### Field Options

```python
# Common field options
field = models.CharField(
    max_length=100,
    null=True,              # Allow NULL in database
    blank=True,             # Allow blank in forms
    default='default',      # Default value
    unique=True,            # Must be unique
    db_index=True,          # Create database index
    verbose_name='Field Name',
    help_text='Help text',
    choices=[               # Predefined choices
        ('draft', 'Draft'),
        ('published', 'Published'),
    ]
)
```

### Relationships

```python
# One-to-Many (ForeignKey)
class Author(models.Model):
    name = models.CharField(max_length=100)

class Book(models.Model):
    title = models.CharField(max_length=200)
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
    # on_delete options: CASCADE, PROTECT, SET_NULL, SET_DEFAULT, DO_NOTHING

# Many-to-Many
class Student(models.Model):
    name = models.CharField(max_length=100)
    courses = models.ManyToManyField('Course')

class Course(models.Model):
    name = models.CharField(max_length=100)

# One-to-One
class User(models.Model):
    username = models.CharField(max_length=100)

class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField()
```

### Model Methods

```python
class Product(models.Model):
    name = models.CharField(max_length=200)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    discount = models.DecimalField(max_digits=5, decimal_places=2, default=0)
    
    def __str__(self):
        return self.name
    
    def get_absolute_url(self):
        from django.urls import reverse
        return reverse('product-detail', args=[str(self.id)])
    
    def get_discounted_price(self):
        return self.price - (self.price * self.discount / 100)
    
    class Meta:
        ordering = ['name']
        verbose_name = 'Product'
        verbose_name_plural = 'Products'
```

### Migrations

```bash
# Create migrations
python manage.py makemigrations

# Apply migrations
python manage.py migrate

# Show migrations
python manage.py showmigrations

# Create empty migration
python manage.py makemigrations --empty myapp

# Reverse migration
python manage.py migrate myapp 0003  # Migrate to specific migration
```

---

## Views

Views handle the logic of your application.

### Function-Based Views (FBV)

```python
from django.shortcuts import render, get_object_or_404, redirect
from django.http import HttpResponse, JsonResponse
from .models import Article

# Simple view
def home(request):
    return HttpResponse("Hello, World!")

# Render template
def article_list(request):
    articles = Article.objects.all()
    context = {'articles': articles}
    return render(request, 'articles/list.html', context)

# Detail view
def article_detail(request, pk):
    article = get_object_or_404(Article, pk=pk)
    return render(request, 'articles/detail.html', {'article': article})

# Handle POST request
def create_article(request):
    if request.method == 'POST':
        title = request.POST.get('title')
        content = request.POST.get('content')
        article = Article.objects.create(title=title, content=content)
        return redirect('article-detail', pk=article.pk)
    return render(request, 'articles/create.html')

# JSON response
def api_articles(request):
    articles = Article.objects.values('id', 'title', 'published_date')
    return JsonResponse(list(articles), safe=False)
```

### Request Object

```python
def my_view(request):
    # Request methods
    request.method          # GET, POST, PUT, DELETE, etc.
    request.GET             # Query parameters
    request.POST            # Form data
    request.FILES           # Uploaded files
    request.COOKIES         # Cookies
    request.session         # Session data
    request.user            # Current user
    request.META            # HTTP headers
    request.path            # URL path
    request.is_ajax()       # Check if AJAX request
    request.is_secure()     # Check if HTTPS
```

### Response Types

```python
from django.http import (
    HttpResponse, JsonResponse, HttpResponseRedirect,
    HttpResponseNotFound, HttpResponseForbidden
)
from django.shortcuts import render, redirect

# HTML response
return HttpResponse("Hello")

# Render template
return render(request, 'template.html', context)

# Redirect
return redirect('view-name')
return redirect('/absolute/url/')

# JSON response
return JsonResponse({'key': 'value'})

# 404 response
return HttpResponseNotFound("Not found")

# Custom status code
return HttpResponse("Created", status=201)
```

### Decorators

```python
from django.views.decorators.http import require_http_methods, require_GET, require_POST
from django.contrib.auth.decorators import login_required, permission_required
from django.views.decorators.cache import cache_page

# Require specific HTTP methods
@require_http_methods(["GET", "POST"])
def my_view(request):
    pass

@require_GET
def get_only_view(request):
    pass

@require_POST
def post_only_view(request):
    pass

# Require login
@login_required
def protected_view(request):
    pass

# Require specific permission
@permission_required('myapp.can_publish')
def publisher_view(request):
    pass

# Cache view for 15 minutes
@cache_page(60 * 15)
def cached_view(request):
    pass
```

---

## Templates

Templates handle the presentation layer using Django Template Language (DTL).

### Template Syntax

```django
{# This is a comment #}

{# Variables #}
{{ variable }}
{{ object.attribute }}
{{ dictionary.key }}
{{ list.0 }}

{# Filters #}
{{ name|lower }}
{{ text|truncatewords:30 }}
{{ date|date:"Y-m-d" }}
{{ value|default:"nothing" }}

{# Tags #}
{% if condition %}
    <p>True</p>
{% elif other_condition %}
    <p>Other</p>
{% else %}
    <p>False</p>
{% endif %}

{% for item in items %}
    <li>{{ item }}</li>
{% empty %}
    <li>No items</li>
{% endfor %}

{# Template inheritance #}
{% extends "base.html" %}

{% block content %}
    <h1>Page content</h1>
{% endblock %}

{# Include templates #}
{% include "navbar.html" %}

{# URL tag #}
<a href="{% url 'article-detail' article.pk %}">Link</a>

{# Static files #}
{% load static %}
<img src="{% static 'images/logo.png' %}">

{# CSRF token #}
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Submit</button>
</form>
```

### Base Template Example

```django
{# templates/base.html #}
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}My Site{% endblock %}</title>
    {% load static %}
    <link rel="stylesheet" href="{% static 'css/style.css' %}">
    {% block extra_css %}{% endblock %}
</head>
<body>
    {% include "navbar.html" %}
    
    <main>
        {% if messages %}
            {% for message in messages %}
                <div class="alert alert-{{ message.tags }}">
                    {{ message }}
                </div>
            {% endfor %}
        {% endif %}
        
        {% block content %}
        {% endblock %}
    </main>
    
    {% include "footer.html" %}
    
    <script src="{% static 'js/main.js' %}"></script>
    {% block extra_js %}{% endblock %}
</body>
</html>
```

### Child Template Example

```django
{# templates/articles/detail.html #}
{% extends "base.html" %}

{% block title %}{{ article.title }} - {{ block.super }}{% endblock %}

{% block content %}
    <article>
        <h1>{{ article.title }}</h1>
        <p class="meta">
            By {{ article.author }} on {{ article.published_date|date:"F j, Y" }}
        </p>
        <div class="content">
            {{ article.content|linebreaks }}
        </div>
    </article>
{% endblock %}
```

### Common Filters

```django
{# String filters #}
{{ value|lower }}
{{ value|upper }}
{{ value|title }}
{{ value|capfirst }}
{{ value|truncatewords:10 }}
{{ value|truncatechars:50 }}
{{ value|slugify }}

{# Number filters #}
{{ value|add:5 }}
{{ value|floatformat:2 }}

{# Date filters #}
{{ date|date:"Y-m-d" }}
{{ date|time:"H:i" }}
{{ date|timesince }}
{{ date|timeuntil }}

{# List filters #}
{{ list|length }}
{{ list|first }}
{{ list|last }}
{{ list|join:", " }}

{# Safe/escape #}
{{ html_content|safe }}
{{ content|escape }}
```

### Custom Template Tags and Filters

```python
# myapp/templatetags/custom_tags.py
from django import template

register = template.Library()

# Custom filter
@register.filter
def multiply(value, arg):
    return value * arg

# Custom simple tag
@register.simple_tag
def current_time(format_string):
    from datetime import datetime
    return datetime.now().strftime(format_string)

# Custom inclusion tag
@register.inclusion_tag('tags/recent_articles.html')
def show_recent_articles(count=5):
    from myapp.models import Article
    articles = Article.objects.order_by('-published_date')[:count]
    return {'articles': articles}
```

```django
{# Using custom tags #}
{% load custom_tags %}

{{ 5|multiply:3 }}
{% current_time "%Y-%m-%d %H:%M" %}
{% show_recent_articles 10 %}
```

---

## URLs and Routing

URL configuration maps URLs to views.

### Project URLs

```python
# myproject/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('myapp.urls')),
    path('blog/', include('blog.urls', namespace='blog')),
]
```

### App URLs

```python
# myapp/urls.py
from django.urls import path
from . import views

app_name = 'myapp'

urlpatterns = [
    # Simple path
    path('', views.home, name='home'),
    
    # Path with parameter
    path('article/<int:pk>/', views.article_detail, name='article-detail'),
    
    # Path with slug
    path('article/<slug:slug>/', views.article_by_slug, name='article-slug'),
    
    # Multiple parameters
    path('category/<str:category>/article/<int:pk>/', views.category_article, name='category-article'),
    
    # Class-based view
    path('articles/', views.ArticleListView.as_view(), name='article-list'),
]
```

### Path Converters

```python
# Built-in converters
path('article/<int:pk>/')        # int: matches integers
path('article/<str:slug>/')      # str: matches strings (default)
path('article/<slug:slug>/')     # slug: matches slug format
path('article/<uuid:id>/')       # uuid: matches UUID
path('file/<path:filepath>/')    # path: matches any string including slashes
```

### Custom Path Converters

```python
# myapp/converters.py
class YearConverter:
    regex = '[0-9]{4}'
    
    def to_python(self, value):
        return int(value)
    
    def to_url(self, value):
        return str(value)

# Register converter
from django.urls import register_converter
from .converters import YearConverter

register_converter(YearConverter, 'yyyy')

# Use in URLs
path('articles/<yyyy:year>/', views.year_archive, name='year-archive')
```

### URL Reversing

```python
# In views
from django.urls import reverse
from django.shortcuts import redirect

def my_view(request):
    url = reverse('article-detail', args=[5])
    url = reverse('article-detail', kwargs={'pk': 5})
    return redirect('article-list')

# In templates
{% url 'article-detail' article.pk %}
{% url 'article-detail' pk=article.pk %}
```

### Including URLs

```python
# Include with namespace
path('blog/', include('blog.urls', namespace='blog')),

# Multiple patterns
from django.urls import include, path

urlpatterns = [
    path('api/', include([
        path('articles/', views.api_articles),
        path('authors/', views.api_authors),
    ])),
]
```

---

## Forms

Django forms handle form rendering, validation, and processing.

### Form from Scratch

```python
from django import forms

class ContactForm(forms.Form):
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    subject = forms.CharField(max_length=200)
    message = forms.CharField(widget=forms.Textarea)
    
    def clean_name(self):
        name = self.cleaned_data['name']
        if len(name) < 3:
            raise forms.ValidationError("Name must be at least 3 characters")
        return name
```

### ModelForm

```python
from django import forms
from .models import Article

class ArticleForm(forms.ModelForm):
    class Meta:
        model = Article
        fields = ['title', 'content', 'is_published']
        # or exclude = ['author']
        # or fields = '__all__'
        
        widgets = {
            'content': forms.Textarea(attrs={'rows': 10}),
            'title': forms.TextInput(attrs={'class': 'form-control'}),
        }
        
        labels = {
            'is_published': 'Publish this article?',
        }
        
        help_texts = {
            'title': 'Enter a catchy title',
        }
    
    def clean_title(self):
        title = self.cleaned_data['title']
        if 'bad_word' in title.lower():
            raise forms.ValidationError("Title contains inappropriate content")
        return title
```

### Using Forms in Views

```python
from django.shortcuts import render, redirect
from .forms import ArticleForm

def create_article(request):
    if request.method == 'POST':
        form = ArticleForm(request.POST, request.FILES)
        if form.is_valid():
            article = form.save(commit=False)
            article.author = request.user
            article.save()
            return redirect('article-detail', pk=article.pk)
    else:
        form = ArticleForm()
    
    return render(request, 'articles/create.html', {'form': form})

def edit_article(request, pk):
    article = get_object_or_404(Article, pk=pk)
    
    if request.method == 'POST':
        form = ArticleForm(request.POST, instance=article)
        if form.is_valid():
            form.save()
            return redirect('article-detail', pk=article.pk)
    else:
        form = ArticleForm(instance=article)
    
    return render(request, 'articles/edit.html', {'form': form, 'article': article})
```

### Rendering Forms in Templates

```django
{# Render entire form #}
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Submit</button>
</form>

{# Render manually #}
<form method="post">
    {% csrf_token %}
    
    {{ form.non_field_errors }}
    
    <div class="form-group">
        {{ form.title.label_tag }}
        {{ form.title }}
        {{ form.title.errors }}
    </div>
    
    <div class="form-group">
        {{ form.content.label_tag }}
        {{ form.content }}
        {{ form.content.errors }}
    </div>
    
    <button type="submit">Submit</button>
</form>

{# Loop through fields #}
<form method="post">
    {% csrf_token %}
    {% for field in form %}
        <div class="form-group">
            {{ field.label_tag }}
            {{ field }}
            {% if field.help_text %}
                <small>{{ field.help_text }}</small>
            {% endif %}
            {{ field.errors }}
        </div>
    {% endfor %}
    <button type="submit">Submit</button>
</form>
```

### Form Validation

```python
class ArticleForm(forms.ModelForm):
    class Meta:
        model = Article
        fields = '__all__'
    
    # Field-level validation
    def clean_title(self):
        title = self.cleaned_data['title']
        if Article.objects.filter(title=title).exists():
            raise forms.ValidationError("This title already exists")
        return title
    
    # Form-level validation
    def clean(self):
        cleaned_data = super().clean()
        title = cleaned_data.get('title')
        content = cleaned_data.get('content')
        
        if title and content and title in content:
            raise forms.ValidationError("Title should not appear in content")
        
        return cleaned_data
```

### Form Widgets

```python
from django import forms

class MyForm(forms.Form):
    # Text inputs
    name = forms.CharField(widget=forms.TextInput(attrs={'class': 'form-control'}))
    password = forms.CharField(widget=forms.PasswordInput())
    
    # Textarea
    message = forms.CharField(widget=forms.Textarea(attrs={'rows': 5}))
    
    # Checkboxes and radios
    agree = forms.BooleanField(widget=forms.CheckboxInput())
    choice = forms.ChoiceField(
        choices=[('1', 'Option 1'), ('2', 'Option 2')],
        widget=forms.RadioSelect()
    )
    
    # Select dropdown
    category = forms.ChoiceField(
        choices=[('tech', 'Technology'), ('health', 'Health')],
        widget=forms.Select()
    )
    
    # Multiple select
    tags = forms.MultipleChoiceField(
        choices=[('python', 'Python'), ('django', 'Django')],
        widget=forms.CheckboxSelectMultiple()
    )
    
    # Date/time
    date = forms.DateField(widget=forms.DateInput(attrs={'type': 'date'}))
    time = forms.TimeField(widget=forms.TimeInput(attrs={'type': 'time'}))
    
    # File upload
    file = forms.FileField()
    image = forms.ImageField()
```

### Formsets

```python
from django.forms import formset_factory, modelformset_factory

# Regular formset
ArticleFormSet = formset_factory(ArticleForm, extra=3)

def manage_articles(request):
    if request.method == 'POST':
        formset = ArticleFormSet(request.POST)
        if formset.is_valid():
            for form in formset:
                # Process each form
                pass
    else:
        formset = ArticleFormSet()
    
    return render(request, 'articles/formset.html', {'formset': formset})

# Model formset
ArticleFormSet = modelformset_factory(Article, fields=['title', 'content'], extra=1)

def edit_articles(request):
    if request.method == 'POST':
        formset = ArticleFormSet(request.POST)
        if formset.is_valid():
            formset.save()
    else:
        formset = ArticleFormSet(queryset=Article.objects.all())
    
    return render(request, 'articles/edit_formset.html', {'formset': formset})
```

---

## Admin Interface

Django provides a powerful automatic admin interface.

### Registering Models

```python
# myapp/admin.py
from django.contrib import admin
from .models import Article, Category

# Simple registration
admin.site.register(Article)

# Registration with customization
@admin.register(Category)
class CategoryAdmin(admin.ModelAdmin):
    list_display = ['name', 'slug', 'created_at']
    prepopulated_fields = {'slug': ('name',)}
```

### Customizing Admin

```python
from django.contrib import admin
from .models import Article

@admin.register(Article)
class ArticleAdmin(admin.ModelAdmin):
    # List page
    list_display = ['title', 'author', 'published_date', 'is_published']
    list_filter = ['is_published', 'published_date', 'author']
    search_fields = ['title', 'content']
    list_per_page = 25
    list_editable = ['is_published']
    
    # Form page
    fields = ['title', 'content', 'author', 'is_published']
    readonly_fields = ['published_date']
    
    # Fieldsets for organizing fields
    fieldsets = (
        ('Basic Information', {
            'fields': ('title', 'content')
        }),
        ('Metadata', {
            'fields': ('author', 'is_published', 'published_date'),
            'classes': ('collapse',)
        }),
    )
    
    # Prepopulate fields
    prepopulated_fields = {'slug': ('title',)}
    
    # Filters
    date_hierarchy = 'published_date'
    
    # Actions
    actions = ['make_published', 'make_draft']
    
    def make_published(self, request, queryset):
        queryset.update(is_published=True)
    make_published.short_description = "Mark selected as published"
    
    def make_draft(self, request, queryset):
        queryset.update(is_published=False)
    make_draft.short_description = "Mark selected as draft"
```

### Inline Models

```python
from django.contrib import admin
from .models import Author, Book

class BookInline(admin.TabularInline):  # or admin.StackedInline
    model = Book
    extra = 1
    fields = ['title', 'published_date']

@admin.register(Author)
class AuthorAdmin(admin.ModelAdmin):
    list_display = ['name', 'email']
    inlines = [BookInline]
```

### Custom Admin Actions

```python
from django.contrib import admin
from django.http import HttpResponse
import csv

@admin.register(Article)
class ArticleAdmin(admin.ModelAdmin):
    actions = ['export_as_csv']
    
    def export_as_csv(self, request, queryset):
        meta = self.model._meta
        field_names = [field.name for field in meta.fields]
        
        response = HttpResponse(content_type='text/csv')
        response['Content-Disposition'] = 'attachment; filename={}.csv'.format(meta)
        writer = csv.writer(response)
        
        writer.writerow(field_names)
        for obj in queryset:
            writer.writerow([getattr(obj, field) for field in field_names])
        
        return response
    
    export_as_csv.short_description = "Export Selected as CSV"
```

### Customizing Admin Site

```python
# myproject/admin.py
from django.contrib import admin

admin.site.site_header = "My Site Administration"
admin.site.site_title = "My Site Admin"
admin.site.index_title = "Welcome to My Site Administration"
```

---

## Authentication

Django provides a complete authentication system.

### User Model

```python
from django.contrib.auth.models import User

# Create user
user = User.objects.create_user('john', 'john@example.com', 'password123')
user.first_name = 'John'
user.last_name = 'Doe'
user.save()

# Create superuser (via command)
# python manage.py createsuperuser

# Authenticate
from django.contrib.auth import authenticate
user = authenticate(username='john', password='password123')
```

### Login/Logout Views

```python
from django.contrib.auth import login, logout, authenticate
from django.shortcuts import render, redirect

def login_view(request):
    if request.method == 'POST':
        username = request.POST['username']
        password = request.POST['password']
        user = authenticate(request, username=username, password=password)
        
        if user is not None:
            login(request, user)
            return redirect('home')
        else:
            return render(request, 'login.html', {'error': 'Invalid credentials'})
    
    return render(request, 'login.html')

def logout_view(request):
    logout(request)
    return redirect('home')
```

### Built-in Authentication Views

```python
# urls.py
from django.contrib.auth import views as auth_views

urlpatterns = [
    path('login/', auth_views.LoginView.as_view(template_name='login.html'), name='login'),
    path('logout/', auth_views.LogoutView.as_view(next_page='home'), name='logout'),
    path('password-change/', auth_views.PasswordChangeView.as_view(), name='password_change'),
    path('password-reset/', auth_views.PasswordResetView.as_view(), name='password_reset'),
]
```

### Protecting Views

```python
from django.contrib.auth.decorators import login_required, permission_required

@login_required
def protected_view(request):
    return render(request, 'protected.html')

@login_required(login_url='/accounts/login/')
def another_protected_view(request):
    return render(request, 'protected.html')

@permission_required('myapp.can_publish')
def publisher_view(request):
    return render(request, 'publish.html')
```

### Custom User Model

```python
# models.py
from django.contrib.auth.models import AbstractUser

class CustomUser(AbstractUser):
    bio = models.TextField(blank=True)
    birth_date = models.DateField(null=True, blank=True)
    
    def __str__(self):
        return self.username

# settings.py
AUTH_USER_MODEL = 'myapp.CustomUser'
```

### User Registration

```python
from django.contrib.auth.forms import UserCreationForm
from django.shortcuts import render, redirect

def register(request):
    if request.method == 'POST':
        form = UserCreationForm(request.POST)
        if form.is_valid():
            user = form.save()
            login(request, user)
            return redirect('home')
    else:
        form = UserCreationForm()
    
    return render(request, 'register.html', {'form': form})
```

### Checking Permissions

```python
# In views
if request.user.is_authenticated:
    # User is logged in
    pass

if request.user.has_perm('myapp.can_publish'):
    # User has specific permission
    pass

# In templates
{% if user.is_authenticated %}
    <p>Welcome, {{ user.username }}</p>
{% else %}
    <a href="{% url 'login' %}">Login</a>
{% endif %}

{% if perms.myapp.can_publish %}
    <a href="{% url 'publish' %}">Publish</a>
{% endif %}
```

---

## Middleware

Middleware is a framework of hooks into Django's request/response processing.

### Built-in Middleware

```python
# settings.py
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

### Custom Middleware

```python
# myapp/middleware.py

# Function-based middleware
def simple_middleware(get_response):
    # One-time configuration and initialization
    
    def middleware(request):
        # Code executed before the view
        
        response = get_response(request)
        
        # Code executed after the view
        
        return response
    
    return middleware

# Class-based middleware
class CustomMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        # Before view
        request.custom_attribute = 'value'
        
        response = self.get_response(request)
        
        # After view
        response['X-Custom-Header'] = 'Value'
        
        return response
    
    def process_exception(self, request, exception):
        # Handle exceptions
        return None

# Register in settings.py
MIDDLEWARE = [
    # ...
    'myapp.middleware.CustomMiddleware',
]
```

### Middleware Examples

```python
# Logging middleware
class RequestLogMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        import logging
        logger = logging.getLogger('django.request')
        logger.info(f'{request.method} {request.path}')
        
        response = self.get_response(request)
        return response

# Time tracking middleware
import time

class TimingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
    
    def __call__(self, request):
        start_time = time.time()
        
        response = self.get_response(request)
        
        duration = time.time() - start_time
        response['X-Page-Generation-Duration-Ms'] = int(duration * 1000)
        
        return response
```

---

## Static and Media Files

### Static Files (CSS, JavaScript, Images)

```python
# settings.py
STATIC_URL = '/static/'
STATIC_ROOT = BASE_DIR / 'staticfiles'

STATICFILES_DIRS = [
    BASE_DIR / 'static',
]

# Collect static files for production
# python manage.py collectstatic
```

### Using Static Files in Templates

```django
{% load static %}

<link rel="stylesheet" href="{% static 'css/style.css' %}">
<script src="{% static 'js/main.js' %}"></script>
<img src="{% static 'images/logo.png' %}">
```

### Media Files (User Uploads)

```python
# settings.py
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'

# urls.py (for development)
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # ... your patterns
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

### File Upload Example

```python
# models.py
class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    avatar = models.ImageField(upload_to='avatars/')
    document = models.FileField(upload_to='documents/')

# views.py
def upload_file(request):
    if request.method == 'POST' and request.FILES['file']:
        uploaded_file = request.FILES['file']
        # Process file
        profile = Profile.objects.get(user=request.user)
        profile.avatar = uploaded_file
        profile.save()
    
    return render(request, 'upload.html')

# template
<form method="post" enctype="multipart/form-data">
    {% csrf_token %}
    <input type="file" name="file">
    <button type="submit">Upload</button>
</form>
```

---

## Database and ORM

Django's ORM provides a powerful way to interact with databases.

### QuerySets

```python
from myapp.models import Article

# Get all objects
articles = Article.objects.all()

# Filter
published = Article.objects.filter(is_published=True)
recent = Article.objects.filter(published_date__gte='2024-01-01')

# Exclude
drafts = Article.objects.exclude(is_published=True)

# Get single object
article = Article.objects.get(pk=1)
article = Article.objects.get(title='My Title')

# Get or create
article, created = Article.objects.get_or_create(
    title='My Title',
    defaults={'content': 'Default content'}
)

# Update or create
article, created = Article.objects.update_or_create(
    title='My Title',
    defaults={'content': 'Updated content'}
)

# Order
articles = Article.objects.order_by('-published_date')
articles = Article.objects.order_by('author', '-published_date')

# Limit
first_10 = Article.objects.all()[:10]
article_5_to_10 = Article.objects.all()[5:10]

# Count
count = Article.objects.count()

# Exists
exists = Article.objects.filter(title='Test').exists()

# Delete
Article.objects.filter(is_published=False).delete()
```

### Field Lookups

```python
# Exact match
Article.objects.filter(title__exact='Hello')
Article.objects.filter(title='Hello')  # Same as above

# Case-insensitive exact
Article.objects.filter(title__iexact='hello')

# Contains
Article.objects.filter(title__contains='Django')
Article.objects.filter(title__icontains='django')

# Starts with / Ends with
Article.objects.filter(title__startswith='Hello')
Article.objects.filter(title__endswith='World')

# In
Article.objects.filter(id__in=[1, 2, 3])

# Greater than / Less than
Article.objects.filter(views__gt=100)
Article.objects.filter(views__gte=100)
Article.objects.filter(views__lt=100)
Article.objects.filter(views__lte=100)

# Range
Article.objects.filter(published_date__range=('2024-01-01', '2024-12-31'))

# Null checks
Article.objects.filter(content__isnull=True)

# Date lookups
Article.objects.filter(published_date__year=2024)
Article.objects.filter(published_date__month=1)
Article.objects.filter(published_date__day=1)
```

### Complex Queries with Q Objects

```python
from django.db.models import Q

# OR conditions
Article.objects.filter(Q(title__contains='Django') | Q(content__contains='Django'))

# AND conditions
Article.objects.filter(Q(is_published=True) & Q(views__gt=100))

# NOT conditions
Article.objects.filter(~Q(is_published=True))

# Complex combinations
Article.objects.filter(
    Q(title__contains='Django') | Q(content__contains='Django'),
    is_published=True
)
```

### Aggregation and Annotation

```python
from django.db.models import Count, Avg, Sum, Max, Min

# Aggregation (returns dictionary)
from myapp.models import Article

result = Article.objects.aggregate(
    total_articles=Count('id'),
    avg_views=Avg('views'),
    max_views=Max('views'),
)
# {'total_articles': 100, 'avg_views': 250.5, 'max_views': 1000}

# Annotation (adds field to each object)
from myapp.models import Author

authors = Author.objects.annotate(
    article_count=Count('article'),
    avg_article_views=Avg('article__views')
)

for author in authors:
    print(f"{author.name}: {author.article_count} articles")
```

### F Expressions

```python
from django.db.models import F

# Reference field values
Article.objects.filter(views__gt=F('likes'))

# Arithmetic
Article.objects.update(views=F('views') + 1)

# Across relationships
Article.objects.filter(author__age__gt=F('reviewer__age'))
```

### Selecting Related Data

```python
# select_related (for ForeignKey and OneToOne)
# Reduces queries by using SQL JOIN
articles = Article.objects.select_related('author').all()

for article in articles:
    print(article.author.name)  # No additional query

# prefetch_related (for ManyToMany and reverse ForeignKey)
# Uses separate query then joins in Python
authors = Author.objects.prefetch_related('articles').all()

for author in authors:
    for article in author.articles.all():  # No additional queries
        print(article.title)
```

### Raw SQL

```python
# Raw queries
articles = Article.objects.raw('SELECT * FROM myapp_article WHERE is_published = %s', [True])

# Execute custom SQL
from django.db import connection

with connection.cursor() as cursor:
    cursor.execute("UPDATE myapp_article SET views = views + 1 WHERE id = %s", [article_id])
    cursor.execute("SELECT * FROM myapp_article WHERE views > %s", [100])
    results = cursor.fetchall()
```

### Transactions

```python
from django.db import transaction

# Atomic decorator
@transaction.atomic
def create_article_with_tags(title, content, tags):
    article = Article.objects.create(title=title, content=content)
    for tag_name in tags:
        tag, created = Tag.objects.get_or_create(name=tag_name)
        article.tags.add(tag)
    return article

# Atomic context manager
def my_view(request):
    try:
        with transaction.atomic():
            article = Article.objects.create(title='Test')
            # If any error occurs, transaction is rolled back
            raise Exception('Oops')
    except Exception:
        pass
```

---

## Class-Based Views

Class-based views provide an object-oriented way to handle views.

### Generic Views

```python
from django.views.generic import (
    ListView, DetailView, CreateView, UpdateView, DeleteView, TemplateView
)
from django.urls import reverse_lazy
from .models import Article

# Template view
class HomeView(TemplateView):
    template_name = 'home.html'
    
    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['latest_articles'] = Article.objects.order_by('-published_date')[:5]
        return context

# List view
class ArticleListView(ListView):
    model = Article
    template_name = 'articles/list.html'
    context_object_name = 'articles'
    paginate_by = 10
    
    def get_queryset(self):
        return Article.objects.filter(is_published=True).order_by('-published_date')

# Detail view
class ArticleDetailView(DetailView):
    model = Article
    template_name = 'articles/detail.html'
    context_object_name = 'article'

# Create view
class ArticleCreateView(CreateView):
    model = Article
    template_name = 'articles/create.html'
    fields = ['title', 'content', 'is_published']
    success_url = reverse_lazy('article-list')
    
    def form_valid(self, form):
        form.instance.author = self.request.user
        return super().form_valid(form)

# Update view
class ArticleUpdateView(UpdateView):
    model = Article
    template_name = 'articles/update.html'
    fields = ['title', 'content', 'is_published']
    
    def get_success_url(self):
        return reverse_lazy('article-detail', kwargs={'pk': self.object.pk})

# Delete view
class ArticleDeleteView(DeleteView):
    model = Article
    template_name = 'articles/delete.html'
    success_url = reverse_lazy('article-list')
```

### Using Class-Based Views in URLs

```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.HomeView.as_view(), name='home'),
    path('articles/', views.ArticleListView.as_view(), name='article-list'),
    path('articles/<int:pk>/', views.ArticleDetailView.as_view(), name='article-detail'),
    path('articles/create/', views.ArticleCreateView.as_view(), name='article-create'),
    path('articles/<int:pk>/edit/', views.ArticleUpdateView.as_view(), name='article-update'),
    path('articles/<int:pk>/delete/', views.ArticleDeleteView.as_view(), name='article-delete'),
]
```

### Mixins

```python
from django.contrib.auth.mixins import LoginRequiredMixin, PermissionRequiredMixin
from django.views.generic import CreateView

class ArticleCreateView(LoginRequiredMixin, CreateView):
    model = Article
    fields = ['title', 'content']
    login_url = '/login/'

class PublishArticleView(PermissionRequiredMixin, UpdateView):
    model = Article
    fields = ['is_published']
    permission_required = 'myapp.can_publish'
```

### Custom Mixins

```python
class AuthorRequiredMixin:
    """Verify that the current user is the author of the object"""
    def dispatch(self, request, *args, **kwargs):
        obj = self.get_object()
        if obj.author != request.user:
            return HttpResponseForbidden()
        return super().dispatch(request, *args, **kwargs)

class ArticleUpdateView(AuthorRequiredMixin, UpdateView):
    model = Article
    fields = ['title', 'content']
```

---

## REST Framework

Django REST Framework (DRF) is a powerful toolkit for building Web APIs.

### Installation

```bash
pip install djangorestframework
```

```python
# settings.py
INSTALLED_APPS = [
    # ...
    'rest_framework',
]
```

### Serializers

```python
# serializers.py
from rest_framework import serializers
from .models import Article

class ArticleSerializer(serializers.ModelSerializer):
    author_name = serializers.CharField(source='author.username', read_only=True)
    
    class Meta:
        model = Article
        fields = ['id', 'title', 'content', 'author', 'author_name', 'published_date']
        read_only_fields = ['author', 'published_date']
    
    def validate_title(self, value):
        if len(value) < 5:
            raise serializers.ValidationError("Title must be at least 5 characters")
        return value
```

### API Views

```python
# views.py
from rest_framework import viewsets, status
from rest_framework.decorators import action, api_view
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticated
from .models import Article
from .serializers import ArticleSerializer

# ViewSet
class ArticleViewSet(viewsets.ModelViewSet):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
    permission_classes = [IsAuthenticated]
    
    def get_queryset(self):
        queryset = Article.objects.all()
        is_published = self.request.query_params.get('is_published')
        if is_published is not None:
            queryset = queryset.filter(is_published=is_published)
        return queryset
    
    def perform_create(self, serializer):
        serializer.save(author=self.request.user)
    
    @action(detail=True, methods=['post'])
    def publish(self, request, pk=None):
        article = self.get_object()
        article.is_published = True
        article.save()
        return Response({'status': 'article published'})

# Function-based view
@api_view(['GET', 'POST'])
def article_list(request):
    if request.method == 'GET':
        articles = Article.objects.all()
        serializer = ArticleSerializer(articles, many=True)
        return Response(serializer.data)
    
    elif request.method == 'POST':
        serializer = ArticleSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save(author=request.user)
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

### URLs

```python
# urls.py
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from . import views

router = DefaultRouter()
router.register(r'articles', views.ArticleViewSet)

urlpatterns = [
    path('api/', include(router.urls)),
    path('api-auth/', include('rest_framework.urls')),
]
```

### Permissions

```python
from rest_framework import permissions

class IsAuthorOrReadOnly(permissions.BasePermission):
    def has_object_permission(self, request, view, obj):
        # Read permissions for any request
        if request.method in permissions.SAFE_METHODS:
            return True
        
        # Write permissions only for author
        return obj.author == request.user

# Use in view
class ArticleViewSet(viewsets.ModelViewSet):
    permission_classes = [IsAuthorOrReadOnly]
```

### Authentication

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.TokenAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticatedOrReadOnly',
    ],
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10
}

# Token authentication setup
INSTALLED_APPS = [
    # ...
    'rest_framework.authtoken',
]

# Create tokens
from rest_framework.authtoken.models import Token
token = Token.objects.create(user=user)
```

---

## Security

Django provides multiple security features out of the box.

### CSRF Protection

```python
# Enabled by default via middleware
MIDDLEWARE = [
    'django.middleware.csrf.CsrfViewMiddleware',
]

# In templates
<form method="post">
    {% csrf_token %}
    <!-- form fields -->
</form>

# Exempt view from CSRF
from django.views.decorators.csrf import csrf_exempt

@csrf_exempt
def my_view(request):
    pass
```

### SQL Injection Protection

```python
# SAFE - Django ORM automatically escapes
Article.objects.filter(title=user_input)

# SAFE - Parameterized queries
Article.objects.raw('SELECT * FROM article WHERE title = %s', [user_input])

# UNSAFE - Never do this
Article.objects.raw(f'SELECT * FROM article WHERE title = {user_input}')
```

### XSS Protection

```django
{# Django templates auto-escape by default #}
{{ user_input }}  {# Automatically escaped #}

{# Mark as safe only when necessary #}
{{ trusted_html|safe }}

{# Escape in JavaScript #}
<script>
    var data = "{{ data|escapejs }}";
</script>
```

### Security Settings

```python
# settings.py

# HTTPS
SECURE_SSL_REDIRECT = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True

# HSTS
SECURE_HSTS_SECONDS = 31536000
SECURE_HSTS_INCLUDE_SUBDOMAINS = True
SECURE_HSTS_PRELOAD = True

# Other security
SECURE_CONTENT_TYPE_NOSNIFF = True
SECURE_BROWSER_XSS_FILTER = True
X_FRAME_OPTIONS = 'DENY'

# Allowed hosts
ALLOWED_HOSTS = ['yourdomain.com', 'www.yourdomain.com']

# Secret key (keep secret in production)
SECRET_KEY = 'your-secret-key-here'  # Use environment variable in production
```

### Password Validation

```python
# settings.py
AUTH_PASSWORD_VALIDATORS = [
    {
        'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
        'OPTIONS': {
            'min_length': 8,
        }
    },
    {
        'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
    },
]
```

---

## Testing

Django provides a comprehensive testing framework.

### Writing Tests

```python
# tests.py
from django.test import TestCase, Client
from django.urls import reverse
from django.contrib.auth.models import User
from .models import Article

class ArticleModelTest(TestCase):
    def setUp(self):
        self.user = User.objects.create_user('testuser', 'test@example.com', 'password')
        self.article = Article.objects.create(
            title='Test Article',
            content='Test content',
            author=self.user
        )
    
    def test_article_creation(self):
        self.assertTrue(isinstance(self.article, Article))
        self.assertEqual(self.article.__str__(), self.article.title)
    
    def test_article_list_view(self):
        response = self.client.get(reverse('article-list'))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, 'Test Article')
    
    def test_article_detail_view(self):
        response = self.client.get(reverse('article-detail', args=[self.article.pk]))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, self.article.title)

class ArticleViewTest(TestCase):
    def setUp(self):
        self.client = Client()
        self.user = User.objects.create_user('testuser', 'test@example.com', 'password')
    
    def test_create_article_requires_login(self):
        response = self.client.get(reverse('article-create'))
        self.assertEqual(response.status_code, 302)  # Redirect to login
    
    def test_create_article_authenticated(self):
        self.client.login(username='testuser', password='password')
        response = self.client.post(reverse('article-create'), {
            'title': 'New Article',
            'content': 'New content',
        })
        self.assertEqual(Article.objects.count(), 1)
```

### Running Tests

```bash
# Run all tests
python manage.py test

# Run specific app tests
python manage.py test myapp

# Run specific test class
python manage.py test myapp.tests.ArticleModelTest

# Run specific test method
python manage.py test myapp.tests.ArticleModelTest.test_article_creation

# Run with verbosity
python manage.py test --verbosity=2

# Keep test database
python manage.py test --keepdb
```

### Test Client

```python
from django.test import Client

client = Client()

# GET request
response = client.get('/articles/')
response = client.get(reverse('article-list'))

# POST request
response = client.post('/articles/create/', {
    'title': 'Test',
    'content': 'Content'
})

# Login
client.login(username='user', password='pass')

# Force login (bypass authentication)
from django.contrib.auth.models import User
user = User.objects.get(username='testuser')
client.force_login(user)

# Check response
self.assertEqual(response.status_code, 200)
self.assertContains(response, 'Expected text')
self.assertTemplateUsed(response, 'template.html')
```

### Fixtures

```python
# Create fixture
python manage.py dumpdata myapp --indent=2 > myapp/fixtures/initial_data.json

# Load fixture in tests
class MyTest(TestCase):
    fixtures = ['initial_data.json']
    
    def test_something(self):
        # Data from fixture is available
        pass
```

---

## Deployment

### Production Settings

```python
# settings/production.py
from .base import *

DEBUG = False

ALLOWED_HOSTS = ['yourdomain.com', 'www.yourdomain.com']

# Database
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'your_db',
        'USER': 'your_user',
        'PASSWORD': os.environ.get('DB_PASSWORD'),
        'HOST': 'localhost',
        'PORT': '5432',
    }
}

# Static files
STATIC_ROOT = '/var/www/yourdomain.com/static/'
MEDIA_ROOT = '/var/www/yourdomain.com/media/'

# Security
SECURE_SSL_REDIRECT = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
```

### Environment Variables

```python
# Use python-decouple or django-environ
import os
from decouple import config

SECRET_KEY = config('SECRET_KEY')
DEBUG = config('DEBUG', default=False, cast=bool)
DATABASE_URL = config('DATABASE_URL')
```

### Static Files Collection

```bash
# Collect all static files to STATIC_ROOT
python manage.py collectstatic
```

### Gunicorn Setup

```bash
# Install
pip install gunicorn

# Run
gunicorn myproject.wsgi:application --bind 0.0.0.0:8000
```

### Nginx Configuration

```nginx
server {
    listen 80;
    server_name yourdomain.com;
    
    location /static/ {
        alias /var/www/yourdomain.com/static/;
    }
    
    location /media/ {
        alias /var/www/yourdomain.com/media/;
    }
    
    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### Database Migrations

```bash
# On production server
python manage.py migrate --settings=myproject.settings.production
```

### Logging

```python
# settings.py
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'file': {
            'level': 'ERROR',
            'class': 'logging.FileHandler',
            'filename': '/var/log/django/error.log',
        },
    },
    'loggers': {
        'django': {
            'handlers': ['file'],
            'level': 'ERROR',
            'propagate': True,
        },
    },
}
```

---

## Best Practices

### Project Organization

```
myproject/
├── manage.py
├── myproject/
│   ├── __init__.py
│   ├── settings/
│   │   ├── __init__.py
│   │   ├── base.py
│   │   ├── development.py
│   │   └── production.py
│   ├── urls.py
│   ├── wsgi.py
│   └── asgi.py
├── apps/
│   ├── blog/
│   ├── users/
│   └── api/
├── templates/
├── static/
├── media/
├── requirements/
│   ├── base.txt
│   ├── development.txt
│   └── production.txt
└── tests/
```

### Code Quality

1. **Follow PEP 8**: Use consistent code style
2. **Use meaningful names**: Clear variable and function names
3. **Keep views thin**: Move logic to models or services
4. **DRY principle**: Don't repeat yourself
5. **Use class-based views**: When appropriate for cleaner code
6. **Write tests**: Aim for high test coverage
7. **Document code**: Use docstrings and comments

### Performance

1. **Use select_related and prefetch_related**: Reduce database queries
2. **Add database indexes**: For frequently queried fields
3. **Use caching**: Django's caching framework
4. **Optimize queries**: Use Django Debug Toolbar
5. **Pagination**: For large datasets
6. **Lazy loading**: Use QuerySets efficiently

### Security

1. **Keep Django updated**: Security patches
2. **Use environment variables**: For sensitive data
3. **Validate user input**: Always validate and sanitize
4. **Use HTTPS**: In production
5. **Regular security audits**: Check for vulnerabilities
6. **Limit user permissions**: Principle of least privilege

### Database

1. **Use migrations**: Never modify database manually
2. **Backup regularly**: Automate database backups
3. **Use transactions**: For data integrity
4. **Index frequently queried fields**: Improve performance
5. **Avoid N+1 queries**: Use select_related/prefetch_related

---

## Additional Resources

### Documentation
- Official Django Documentation: https://docs.djangoproject.com/
- Django REST Framework: https://www.django-rest-framework.org/
- Django Packages: https://djangopackages.org/

### Common Django Packages
- **django-debug-toolbar**: Development debugging
- **django-extensions**: Additional management commands
- **django-crispy-forms**: Better form rendering
- **django-filter**: Advanced filtering
- **celery**: Asynchronous task queue
- **django-storages**: Cloud storage backends
- **pillow**: Image processing
- **django-cors-headers**: CORS handling for APIs

### Management Commands

```bash
# Create superuser
python manage.py createsuperuser

# Shell
python manage.py shell

# Database shell
python manage.py dbshell

# Show migrations
python manage.py showmigrations

# Check for problems
python manage.py check

# Clear cache
python manage.py clear_cache

# Create custom command
# myapp/management/commands/mycommand.py
```

---

This comprehensive guide covers the fundamental and advanced concepts of Django. Practice these concepts by building projects, and refer back to this guide as needed. Happy coding!