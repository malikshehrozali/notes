# 🐍 Flask Complete Notes — Everything You Need

> A complete reference guide covering every concept, pattern, and feature of Flask with code examples and explanations.

---

## Table of Contents

1. [What is Flask?](#1-what-is-flask)
2. [Installation & Project Setup](#2-installation--project-setup)
3. [Project Structure](#3-project-structure)
4. [Application Factory Pattern](#4-application-factory-pattern)
5. [Routing & URL Rules](#5-routing--url-rules)
6. [Request & Response](#6-request--response)
7. [Templates (Jinja2)](#7-templates-jinja2)
8. [Static Files](#8-static-files)
9. [Blueprints (Modular Apps)](#9-blueprints-modular-apps)
10. [Configuration](#10-configuration)
11. [Database with Flask-SQLAlchemy](#11-database-with-flask-sqlalchemy)
12. [Database Migrations (Flask-Migrate)](#12-database-migrations-flask-migrate)
13. [Flask-Marshmallow (Serialization & Validation)](#13-flask-marshmallow-serialization--validation)
14. [Authentication (Flask-JWT-Extended)](#14-authentication-flask-jwt-extended)
15. [Authentication (Flask-Login — Session Based)](#15-authentication-flask-login--session-based)
16. [Forms (Flask-WTF)](#16-forms-flask-wtf)
17. [Error Handling](#17-error-handling)
18. [Middleware & Hooks (Before/After Request)](#18-middleware--hooks-beforeafter-request)
19. [Flask-CORS](#19-flask-cors)
20. [File Upload](#20-file-upload)
21. [Email (Flask-Mail)](#21-email-flask-mail)
22. [Caching (Flask-Caching)](#22-caching-flask-caching)
23. [Rate Limiting (Flask-Limiter)](#23-rate-limiting-flask-limiter)
24. [WebSockets (Flask-SocketIO)](#24-websockets-flask-socketio)
25. [Task Queues (Celery + Redis)](#25-task-queues-celery--redis)
26. [REST API with Flask-RESTful](#26-rest-api-with-flask-restful)
27. [REST API with Flask-RESTX (Swagger included)](#27-rest-api-with-flask-restx-swagger-included)
28. [Testing](#28-testing)
29. [Logging](#29-logging)
30. [CLI Commands](#30-cli-commands)
31. [Deployment](#31-deployment)
32. [Security Best Practices](#32-security-best-practices)
33. [Common Patterns & Tips](#33-common-patterns--tips)

---

## 1. What is Flask?

Flask is a **lightweight, micro web framework** for Python. "Micro" doesn't mean it lacks features — it means Flask keeps the core simple and lets you add what you need via extensions.

### Flask vs Django

| Feature | Flask | Django |
|---------|-------|--------|
| Type | Micro-framework | Full-stack framework |
| ORM | External (SQLAlchemy) | Built-in |
| Admin panel | External | Built-in |
| Flexibility | Very high | More opinionated |
| Learning curve | Gentle | Steeper |
| Best for | APIs, microservices | Full web apps |

### Core Dependencies

Flask is built on top of two libraries:
- **Werkzeug** — WSGI utility library (routing, request/response handling)
- **Jinja2** — Templating engine

---

## 2. Installation & Project Setup

### Create a virtual environment

```bash
# Create virtual environment
python -m venv venv

# Activate it
# Linux/Mac:
source venv/bin/activate
# Windows:
venv\Scripts\activate
```

### Install Flask

```bash
pip install flask
```

### The simplest Flask app

```python
# app.py
from flask import Flask

app = Flask(__name__)

@app.route('/')
def index():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run(debug=True)
```

### Run the app

```bash
# Method 1 — using flask CLI
flask run

# Method 2 — using python directly
python app.py

# With options
flask run --host=0.0.0.0 --port=5000 --debug
```

### Install common packages

```bash
# All-in-one install
pip install flask flask-sqlalchemy flask-migrate flask-marshmallow \
            marshmallow-sqlalchemy flask-jwt-extended flask-cors \
            flask-login flask-wtf flask-mail flask-caching \
            flask-limiter flask-socketio celery redis \
            python-dotenv bcrypt

# Save to requirements.txt
pip freeze > requirements.txt

# Install from requirements.txt
pip install -r requirements.txt
```

---

## 3. Project Structure

### Simple structure (small projects)

```
my-project/
├── app.py
├── config.py
├── requirements.txt
├── .env
├── static/
│   ├── css/
│   ├── js/
│   └── images/
└── templates/
    ├── base.html
    └── index.html
```

### Recommended structure (larger projects with Blueprints)

```
my-project/
├── app/
│   ├── __init__.py          ← Application factory
│   ├── extensions.py        ← Flask extensions (db, jwt, etc.)
│   ├── models/
│   │   ├── __init__.py
│   │   ├── user.py
│   │   └── post.py
│   ├── schemas/             ← Marshmallow schemas
│   │   ├── __init__.py
│   │   └── user_schema.py
│   ├── blueprints/
│   │   ├── auth/
│   │   │   ├── __init__.py
│   │   │   └── routes.py
│   │   ├── users/
│   │   │   ├── __init__.py
│   │   │   └── routes.py
│   │   └── posts/
│   │       ├── __init__.py
│   │       └── routes.py
│   ├── utils/
│   │   ├── __init__.py
│   │   └── helpers.py
│   └── errors/
│       ├── __init__.py
│       └── handlers.py
├── migrations/
├── tests/
│   ├── conftest.py
│   ├── test_auth.py
│   └── test_users.py
├── config.py
├── requirements.txt
├── .env
└── run.py                   ← Entry point
```

---

## 4. Application Factory Pattern

The **factory pattern** is the recommended way to structure Flask apps. It lets you create multiple app instances (useful for testing).

### `app/__init__.py` — Factory

```python
from flask import Flask
from .extensions import db, migrate, jwt, cors, ma
from .blueprints.auth import auth_bp
from .blueprints.users import users_bp
from .blueprints.posts import posts_bp
from .errors.handlers import register_error_handlers
from config import config


def create_app(config_name='default'):
    app = Flask(__name__)

    # Load config
    app.config.from_object(config[config_name])

    # Initialize extensions
    db.init_app(app)
    migrate.init_app(app, db)
    jwt.init_app(app)
    cors.init_app(app)
    ma.init_app(app)

    # Register blueprints
    app.register_blueprint(auth_bp, url_prefix='/api/auth')
    app.register_blueprint(users_bp, url_prefix='/api/users')
    app.register_blueprint(posts_bp, url_prefix='/api/posts')

    # Register error handlers
    register_error_handlers(app)

    return app
```

### `app/extensions.py`

```python
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate
from flask_jwt_extended import JWTManager
from flask_cors import CORS
from flask_marshmallow import Marshmallow

db = SQLAlchemy()
migrate = Migrate()
jwt = JWTManager()
cors = CORS()
ma = Marshmallow()
```

### `run.py` — Entry point

```python
from app import create_app

app = create_app('development')

if __name__ == '__main__':
    app.run()
```

---

## 5. Routing & URL Rules

### Basic Routes

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def index():
    return 'Home Page'

@app.route('/about')
def about():
    return 'About Page'
```

### HTTP Methods

```python
from flask import request

# Single method
@app.route('/users', methods=['GET'])
def get_users():
    return 'List of users'

# Multiple methods
@app.route('/users', methods=['GET', 'POST'])
def users():
    if request.method == 'GET':
        return 'Get users'
    if request.method == 'POST':
        return 'Create user'

# Separate functions per method (cleaner)
@app.get('/cats')
def get_cats():
    return 'Get cats'

@app.post('/cats')
def create_cat():
    return 'Create cat'

@app.put('/cats/<int:id>')
def update_cat(id):
    return f'Update cat {id}'

@app.patch('/cats/<int:id>')
def partial_update_cat(id):
    return f'Patch cat {id}'

@app.delete('/cats/<int:id>')
def delete_cat(id):
    return f'Delete cat {id}'
```

### URL Parameters (Variable Rules)

```python
# String (default)
@app.route('/users/<username>')
def get_user(username):
    return f'User: {username}'

# Integer
@app.route('/posts/<int:post_id>')
def get_post(post_id):
    return f'Post ID: {post_id}'  # post_id is int

# Float
@app.route('/items/<float:price>')
def get_item(price):
    return f'Price: {price}'

# Path (allows slashes)
@app.route('/files/<path:filepath>')
def get_file(filepath):
    return f'File: {filepath}'

# UUID
@app.route('/orders/<uuid:order_id>')
def get_order(order_id):
    return f'Order: {order_id}'

# Multiple parameters
@app.route('/users/<int:user_id>/posts/<int:post_id>')
def get_user_post(user_id, post_id):
    return f'User {user_id}, Post {post_id}'
```

### URL Building

```python
from flask import url_for

with app.test_request_context():
    # Generate URL for a view function
    print(url_for('index'))                        # /
    print(url_for('get_user', username='john'))    # /users/john
    print(url_for('get_post', post_id=5))          # /posts/5
    print(url_for('static', filename='css/style.css'))  # /static/css/style.css

    # With query parameters
    print(url_for('get_users', page=2, limit=10))  # /users?page=2&limit=10
```

### Redirects

```python
from flask import redirect, url_for

@app.route('/old-page')
def old_page():
    return redirect(url_for('new_page'), 301)  # Permanent redirect

@app.route('/new-page')
def new_page():
    return 'New Page'

# Redirect to external URL
@app.route('/google')
def go_google():
    return redirect('https://google.com')
```

---

## 6. Request & Response

### The `request` Object

```python
from flask import request

@app.route('/example', methods=['GET', 'POST'])
def example():
    # URL query parameters: /example?name=John&age=25
    name = request.args.get('name')
    age = request.args.get('age', 0, type=int)  # Default + type conversion
    all_args = request.args.to_dict()

    # Form data (application/x-www-form-urlencoded or multipart/form-data)
    email = request.form.get('email')
    all_form = request.form.to_dict()

    # JSON body (application/json)
    data = request.get_json()                # Returns dict or None
    data = request.get_json(force=True)      # Force parse even without content-type
    data = request.get_json(silent=True)     # Return None on error instead of raising

    # Raw body
    raw = request.data                       # bytes
    raw_str = request.data.decode('utf-8')   # string

    # Headers
    auth = request.headers.get('Authorization')
    content_type = request.content_type
    all_headers = dict(request.headers)

    # Files
    file = request.files.get('photo')

    # Cookies
    session_id = request.cookies.get('session_id')

    # Other useful attributes
    method = request.method           # 'GET', 'POST', etc.
    url = request.url                 # Full URL
    base_url = request.base_url       # URL without query string
    path = request.path               # /example
    host = request.host               # localhost:5000
    remote_addr = request.remote_addr # Client IP
    is_json = request.is_json         # True if Content-Type is application/json
    is_secure = request.is_secure     # True if HTTPS

    return 'OK'
```

### The `response` Object

```python
from flask import jsonify, make_response, Response

# Return JSON (most common for APIs)
@app.route('/api/user')
def get_user():
    return jsonify({'id': 1, 'name': 'John', 'email': 'john@example.com'})

# Return JSON with custom status code
@app.route('/api/user', methods=['POST'])
def create_user():
    return jsonify({'message': 'User created', 'id': 1}), 201

# Return plain text
@app.route('/text')
def plain_text():
    return 'Hello World', 200, {'Content-Type': 'text/plain'}

# Full control with make_response
@app.route('/custom')
def custom_response():
    response = make_response(jsonify({'data': 'value'}), 200)
    response.headers['X-Custom-Header'] = 'MyValue'
    response.set_cookie('token', 'abc123', httponly=True, secure=True, max_age=3600)
    return response

# Stream response
@app.route('/stream')
def stream():
    def generate():
        for i in range(10):
            yield f'data: {i}\n\n'
    return Response(generate(), mimetype='text/event-stream')

# Delete a cookie
@app.route('/logout')
def logout():
    response = make_response(jsonify({'message': 'Logged out'}))
    response.delete_cookie('token')
    return response
```

### Session

```python
from flask import session

app.secret_key = 'your-secret-key'  # Required for sessions

@app.route('/login', methods=['POST'])
def login():
    session['user_id'] = 1
    session['username'] = 'john'
    session.permanent = True  # Use PERMANENT_SESSION_LIFETIME config
    return jsonify({'message': 'Logged in'})

@app.route('/profile')
def profile():
    if 'user_id' not in session:
        return jsonify({'error': 'Not logged in'}), 401
    return jsonify({'user_id': session['user_id']})

@app.route('/logout')
def logout():
    session.clear()  # Clear all session data
    # or session.pop('user_id', None)  # Remove specific key
    return jsonify({'message': 'Logged out'})
```

### `g` — Application Context Global

```python
from flask import g

# g is a temporary storage during a request — cleared after each request
@app.before_request
def load_user():
    g.user = get_current_user()

@app.route('/dashboard')
def dashboard():
    user = g.user  # Available because before_request set it
    return jsonify({'user': user.name})
```

---

## 7. Templates (Jinja2)

### Basic Template Rendering

```python
from flask import render_template

@app.route('/home')
def home():
    return render_template('home.html',
        title='Home Page',
        username='John',
        items=['apple', 'banana', 'cherry'],
    )
```

### `templates/base.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{% block title %}My App{% endblock %}</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='css/style.css') }}">
</head>
<body>
    <nav>
        <a href="{{ url_for('home') }}">Home</a>
        {% if current_user.is_authenticated %}
            <a href="{{ url_for('logout') }}">Logout</a>
        {% else %}
            <a href="{{ url_for('login') }}">Login</a>
        {% endif %}
    </nav>

    {% with messages = get_flashed_messages(with_categories=true) %}
        {% for category, message in messages %}
            <div class="alert alert-{{ category }}">{{ message }}</div>
        {% endfor %}
    {% endwith %}

    <main>
        {% block content %}{% endblock %}
    </main>

    {% block scripts %}{% endblock %}
</body>
</html>
```

### `templates/home.html`

```html
{% extends 'base.html' %}

{% block title %}{{ title }}{% endblock %}

{% block content %}
<h1>Welcome, {{ username }}!</h1>

{# This is a Jinja2 comment #}

{# Variables #}
<p>{{ username }}</p>
<p>{{ username | upper }}</p>          {# Filters #}
<p>{{ username | truncate(10) }}</p>

{# If/else #}
{% if username == 'John' %}
    <p>Hello John!</p>
{% elif username == 'Jane' %}
    <p>Hello Jane!</p>
{% else %}
    <p>Hello stranger!</p>
{% endif %}

{# For loop #}
<ul>
{% for item in items %}
    <li>{{ loop.index }}. {{ item }}</li>  {# loop.index, loop.first, loop.last #}
{% else %}
    <li>No items found</li>
{% endfor %}
</ul>

{# Include another template #}
{% include 'partials/footer.html' %}

{# Set a variable #}
{% set full_name = 'John' + ' ' + 'Doe' %}
<p>{{ full_name }}</p>

{# Raw output (escape HTML) — use | safe to allow HTML #}
<p>{{ "<strong>bold</strong>" | safe }}</p>

{# Macro (reusable template function) #}
{% macro render_field(field) %}
    <div class="field">
        {{ field.label }}
        {{ field() }}
        {% for error in field.errors %}
            <span class="error">{{ error }}</span>
        {% endfor %}
    </div>
{% endmacro %}
{% endblock %}
```

### Jinja2 Filters

```html
{{ name | upper }}              {# JOHN #}
{{ name | lower }}              {# john #}
{{ name | title }}              {# John Doe #}
{{ name | capitalize }}         {# John #}
{{ name | trim }}               {# removes whitespace #}
{{ name | truncate(20) }}       {# Truncate to 20 chars #}
{{ name | replace('a', 'b') }}  {# Replace characters #}
{{ name | default('Guest') }}   {# Default if None/undefined #}
{{ html | safe }}               {# Render raw HTML #}
{{ html | escape }}             {# Escape HTML (default) #}
{{ list | join(', ') }}         {# Join list #}
{{ list | length }}             {# Length #}
{{ list | first }}              {# First element #}
{{ list | last }}               {# Last element #}
{{ list | sort }}               {# Sort list #}
{{ list | reverse }}            {# Reverse list #}
{{ number | round(2) }}         {# Round number #}
{{ number | abs }}              {# Absolute value #}
{{ date | strftime('%Y-%m-%d') }}  {# Format date #}
```

### Custom Template Filters

```python
@app.template_filter('currency')
def currency_filter(value):
    return f'${value:,.2f}'

# Usage in template: {{ 1234.5 | currency }} → $1,234.50

# Register globally
app.jinja_env.filters['my_filter'] = my_filter_function
```

### Context Processors (Inject data into all templates)

```python
@app.context_processor
def inject_globals():
    return {
        'app_name': 'My Flask App',
        'year': datetime.now().year,
        'current_user': get_current_user(),
    }
```

### Flash Messages

```python
from flask import flash, get_flashed_messages

@app.route('/save', methods=['POST'])
def save():
    flash('Saved successfully!', 'success')
    flash('Watch out!', 'warning')
    flash('Something went wrong!', 'error')
    return redirect(url_for('home'))

# In template — already shown in base.html example above
```

---

## 8. Static Files

```python
# Accessing static files via url_for
url_for('static', filename='css/style.css')
url_for('static', filename='js/main.js')
url_for('static', filename='images/logo.png')
```

```html
<!-- In templates -->
<link rel="stylesheet" href="{{ url_for('static', filename='css/style.css') }}">
<script src="{{ url_for('static', filename='js/main.js') }}"></script>
<img src="{{ url_for('static', filename='images/logo.png') }}" alt="Logo">
```

### Serving static files from a custom folder

```python
app = Flask(__name__, static_folder='assets', static_url_path='/assets')
```

---

## 9. Blueprints (Modular Apps)

Blueprints let you split a large application into smaller, reusable components.

### Define a Blueprint

```python
# app/blueprints/users/__init__.py
from flask import Blueprint

users_bp = Blueprint('users', __name__)

from . import routes  # Import routes after creating blueprint
```

```python
# app/blueprints/users/routes.py
from flask import jsonify, request
from . import users_bp
from app.models.user import User
from app.extensions import db

@users_bp.route('/', methods=['GET'])
def get_users():
    users = User.query.all()
    return jsonify([u.to_dict() for u in users])

@users_bp.route('/<int:user_id>', methods=['GET'])
def get_user(user_id):
    user = User.query.get_or_404(user_id)
    return jsonify(user.to_dict())

@users_bp.route('/', methods=['POST'])
def create_user():
    data = request.get_json()
    user = User(**data)
    db.session.add(user)
    db.session.commit()
    return jsonify(user.to_dict()), 201

@users_bp.route('/<int:user_id>', methods=['PUT'])
def update_user(user_id):
    user = User.query.get_or_404(user_id)
    data = request.get_json()
    for key, value in data.items():
        setattr(user, key, value)
    db.session.commit()
    return jsonify(user.to_dict())

@users_bp.route('/<int:user_id>', methods=['DELETE'])
def delete_user(user_id):
    user = User.query.get_or_404(user_id)
    db.session.delete(user)
    db.session.commit()
    return jsonify({'message': 'User deleted'}), 200
```

### Register Blueprint in Factory

```python
# app/__init__.py
from .blueprints.users import users_bp
app.register_blueprint(users_bp, url_prefix='/api/users')
```

### Blueprint with its own templates/static

```python
# Blueprint can have its own templates folder
users_bp = Blueprint('users', __name__, template_folder='templates', static_folder='static')

# Structure:
# app/blueprints/users/
# ├── __init__.py
# ├── routes.py
# ├── templates/
# │   └── users/
# │       └── list.html
# └── static/
#     └── users.css
```

### URL prefix and endpoint names

```python
# When a blueprint has url_prefix='/api/users':
# Route '/' becomes '/api/users/'
# Route '/<id>' becomes '/api/users/<id>'

# url_for uses blueprint name as prefix:
url_for('users.get_users')       # /api/users/
url_for('users.get_user', user_id=1)  # /api/users/1
```

---

## 10. Configuration

### `config.py`

```python
import os
from datetime import timedelta
from dotenv import load_dotenv

load_dotenv()

class Config:
    """Base configuration."""
    SECRET_KEY = os.environ.get('SECRET_KEY') or 'fallback-secret-key'
    DEBUG = False
    TESTING = False

    # Database
    SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL') or 'sqlite:///app.db'
    SQLALCHEMY_TRACK_MODIFICATIONS = False
    SQLALCHEMY_ECHO = False  # Set True to log SQL queries

    # JWT
    JWT_SECRET_KEY = os.environ.get('JWT_SECRET_KEY') or 'jwt-secret'
    JWT_ACCESS_TOKEN_EXPIRES = timedelta(hours=1)
    JWT_REFRESH_TOKEN_EXPIRES = timedelta(days=30)

    # Mail
    MAIL_SERVER = os.environ.get('MAIL_SERVER')
    MAIL_PORT = int(os.environ.get('MAIL_PORT', 587))
    MAIL_USE_TLS = True
    MAIL_USERNAME = os.environ.get('MAIL_USERNAME')
    MAIL_PASSWORD = os.environ.get('MAIL_PASSWORD')
    MAIL_DEFAULT_SENDER = os.environ.get('MAIL_DEFAULT_SENDER')

    # File Upload
    MAX_CONTENT_LENGTH = 16 * 1024 * 1024  # 16 MB max upload
    UPLOAD_FOLDER = os.path.join(os.path.dirname(__file__), 'uploads')
    ALLOWED_EXTENSIONS = {'png', 'jpg', 'jpeg', 'gif', 'pdf'}

    # Session
    PERMANENT_SESSION_LIFETIME = timedelta(days=7)

    # Celery
    CELERY_BROKER_URL = os.environ.get('REDIS_URL') or 'redis://localhost:6379/0'
    CELERY_RESULT_BACKEND = os.environ.get('REDIS_URL') or 'redis://localhost:6379/0'

    # Pagination
    ITEMS_PER_PAGE = 20


class DevelopmentConfig(Config):
    DEBUG = True
    SQLALCHEMY_ECHO = True


class TestingConfig(Config):
    TESTING = True
    SQLALCHEMY_DATABASE_URI = 'sqlite:///:memory:'
    WTF_CSRF_ENABLED = False


class ProductionConfig(Config):
    DEBUG = False
    SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL')


config = {
    'development': DevelopmentConfig,
    'testing': TestingConfig,
    'production': ProductionConfig,
    'default': DevelopmentConfig,
}
```

### `.env` file

```env
FLASK_ENV=development
FLASK_APP=run.py
SECRET_KEY=my-super-secret-key
JWT_SECRET_KEY=my-jwt-secret
DATABASE_URL=postgresql://user:password@localhost/mydb
REDIS_URL=redis://localhost:6379/0
MAIL_SERVER=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=myemail@gmail.com
MAIL_PASSWORD=mypassword
```

### Accessing config in your app

```python
from flask import current_app

@app.route('/info')
def info():
    db_url = current_app.config['SQLALCHEMY_DATABASE_URI']
    debug = current_app.config['DEBUG']
    return jsonify({'db': db_url, 'debug': debug})
```

---

## 11. Database with Flask-SQLAlchemy

### Install

```bash
pip install flask-sqlalchemy psycopg2-binary  # PostgreSQL
# or
pip install flask-sqlalchemy                   # SQLite (built-in)
```

### Define Models

```python
# app/models/user.py
from app.extensions import db
from datetime import datetime

class User(db.Model):
    __tablename__ = 'users'

    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False, index=True)
    email = db.Column(db.String(120), unique=True, nullable=False)
    password_hash = db.Column(db.String(256), nullable=False)
    is_active = db.Column(db.Boolean, default=True, nullable=False)
    is_admin = db.Column(db.Boolean, default=False)
    bio = db.Column(db.Text, nullable=True)
    avatar_url = db.Column(db.String(500), nullable=True)
    created_at = db.Column(db.DateTime, default=datetime.utcnow)
    updated_at = db.Column(db.DateTime, default=datetime.utcnow, onupdate=datetime.utcnow)

    # Relationships
    posts = db.relationship('Post', back_populates='author', lazy='dynamic', cascade='all, delete-orphan')
    profile = db.relationship('Profile', back_populates='user', uselist=False)  # One-to-One

    # Methods
    def set_password(self, password):
        from bcrypt import hashpw, gensalt
        self.password_hash = hashpw(password.encode(), gensalt()).decode()

    def check_password(self, password):
        from bcrypt import checkpw
        return checkpw(password.encode(), self.password_hash.encode())

    def to_dict(self):
        return {
            'id': self.id,
            'username': self.username,
            'email': self.email,
            'is_active': self.is_active,
            'created_at': self.created_at.isoformat(),
        }

    def __repr__(self):
        return f'<User {self.username}>'


class Profile(db.Model):
    __tablename__ = 'profiles'

    id = db.Column(db.Integer, primary_key=True)
    user_id = db.Column(db.Integer, db.ForeignKey('users.id'), unique=True)
    full_name = db.Column(db.String(200))
    phone = db.Column(db.String(20))
    address = db.Column(db.String(500))

    user = db.relationship('User', back_populates='profile')
```

```python
# app/models/post.py
from app.extensions import db
from datetime import datetime

# Many-to-Many association table (no model needed for simple cases)
post_tags = db.Table('post_tags',
    db.Column('post_id', db.Integer, db.ForeignKey('posts.id'), primary_key=True),
    db.Column('tag_id', db.Integer, db.ForeignKey('tags.id'), primary_key=True),
)

class Post(db.Model):
    __tablename__ = 'posts'

    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(200), nullable=False)
    content = db.Column(db.Text, nullable=False)
    slug = db.Column(db.String(200), unique=True)
    is_published = db.Column(db.Boolean, default=False)
    view_count = db.Column(db.Integer, default=0)
    author_id = db.Column(db.Integer, db.ForeignKey('users.id'), nullable=False)
    created_at = db.Column(db.DateTime, default=datetime.utcnow)
    updated_at = db.Column(db.DateTime, default=datetime.utcnow, onupdate=datetime.utcnow)

    author = db.relationship('User', back_populates='posts')
    comments = db.relationship('Comment', back_populates='post', lazy='dynamic', cascade='all, delete-orphan')
    tags = db.relationship('Tag', secondary=post_tags, back_populates='posts')

    def __repr__(self):
        return f'<Post {self.title}>'


class Tag(db.Model):
    __tablename__ = 'tags'

    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(50), unique=True, nullable=False)
    posts = db.relationship('Post', secondary=post_tags, back_populates='tags')


class Comment(db.Model):
    __tablename__ = 'comments'

    id = db.Column(db.Integer, primary_key=True)
    content = db.Column(db.Text, nullable=False)
    author_id = db.Column(db.Integer, db.ForeignKey('users.id'))
    post_id = db.Column(db.Integer, db.ForeignKey('posts.id'))
    created_at = db.Column(db.DateTime, default=datetime.utcnow)

    author = db.relationship('User')
    post = db.relationship('Post', back_populates='comments')
```

### Column Types

```python
db.Column(db.Integer)
db.Column(db.BigInteger)
db.Column(db.Float)
db.Column(db.Numeric(precision=10, scale=2))  # For money
db.Column(db.String(100))     # VARCHAR
db.Column(db.Text)            # TEXT (unlimited)
db.Column(db.Boolean)
db.Column(db.DateTime)
db.Column(db.Date)
db.Column(db.Time)
db.Column(db.JSON)            # JSON column (PostgreSQL native)
db.Column(db.Enum('active', 'inactive', name='status_enum'))
db.Column(db.LargeBinary)     # BLOB
```

### CRUD Operations

```python
from app.extensions import db
from app.models.user import User

# --- CREATE ---
user = User(username='john', email='john@example.com')
user.set_password('secret')
db.session.add(user)
db.session.commit()

# Bulk insert
db.session.add_all([user1, user2, user3])
db.session.commit()

# --- READ ---
# Get by primary key
user = User.query.get(1)                    # None if not found
user = User.query.get_or_404(1)             # Raises 404 if not found

# Filter
user = User.query.filter_by(username='john').first()
user = User.query.filter_by(username='john').first_or_404()
user = User.query.filter(User.email == 'john@example.com').first()

# Multiple filters
users = User.query.filter(
    User.is_active == True,
    User.is_admin == False,
).all()

# Complex queries
from sqlalchemy import or_, and_, not_

users = User.query.filter(
    or_(User.username == 'john', User.username == 'jane')
).all()

users = User.query.filter(
    User.email.like('%@gmail.com')
).all()

users = User.query.filter(
    User.username.in_(['john', 'jane', 'bob'])
).all()

users = User.query.filter(
    User.created_at >= datetime(2024, 1, 1)
).all()

# Ordering
users = User.query.order_by(User.username.asc()).all()
users = User.query.order_by(User.created_at.desc()).all()

# Limit & Offset (Pagination)
users = User.query.offset(10).limit(10).all()

# Count
total = User.query.count()
active_count = User.query.filter_by(is_active=True).count()

# Exists
exists = User.query.filter_by(email='john@example.com').first() is not None

# Join
posts_with_authors = db.session.query(Post, User)\
    .join(User, Post.author_id == User.id)\
    .all()

# With relationships
user = User.query.get(1)
posts = user.posts.all()         # lazy='dynamic' → needs .all()
profile = user.profile           # One-to-One → direct access

# --- UPDATE ---
user = User.query.get(1)
user.username = 'john_doe'
db.session.commit()

# Bulk update
User.query.filter_by(is_active=False).update({'is_admin': False})
db.session.commit()

# --- DELETE ---
user = User.query.get(1)
db.session.delete(user)
db.session.commit()

# Bulk delete
User.query.filter_by(is_active=False).delete()
db.session.commit()

# --- TRANSACTIONS ---
try:
    user = User(username='john', email='john@example.com')
    db.session.add(user)
    post = Post(title='Hello', content='World', author=user)
    db.session.add(post)
    db.session.commit()
except Exception as e:
    db.session.rollback()
    raise e
```

### Pagination

```python
@users_bp.route('/')
def get_users():
    page = request.args.get('page', 1, type=int)
    per_page = request.args.get('per_page', 20, type=int)

    pagination = User.query.order_by(User.created_at.desc()).paginate(
        page=page,
        per_page=per_page,
        error_out=False,  # Return empty list instead of 404 on invalid page
    )

    return jsonify({
        'users': [u.to_dict() for u in pagination.items],
        'meta': {
            'total': pagination.total,
            'pages': pagination.pages,
            'page': pagination.page,
            'per_page': pagination.per_page,
            'has_next': pagination.has_next,
            'has_prev': pagination.has_prev,
        }
    })
```

---

## 12. Database Migrations (Flask-Migrate)

### Install

```bash
pip install flask-migrate
```

### Setup

```python
# extensions.py
from flask_migrate import Migrate
migrate = Migrate()

# __init__.py
migrate.init_app(app, db)
```

### Commands

```bash
# Initialize migrations folder (only once per project)
flask db init

# Generate a migration after changing your models
flask db migrate -m "Add users table"

# Apply migrations to the database
flask db upgrade

# Revert last migration
flask db downgrade

# See current migration version
flask db current

# See migration history
flask db history

# Show SQL that would be executed (without applying)
flask db upgrade --sql
```

### Migration file example

```python
# migrations/versions/abc123_add_users_table.py
from alembic import op
import sqlalchemy as sa

def upgrade():
    op.create_table('users',
        sa.Column('id', sa.Integer(), nullable=False),
        sa.Column('username', sa.String(80), nullable=False),
        sa.Column('email', sa.String(120), nullable=False),
        sa.PrimaryKeyConstraint('id'),
        sa.UniqueConstraint('email'),
        sa.UniqueConstraint('username'),
    )

def downgrade():
    op.drop_table('users')
```

---

## 13. Flask-Marshmallow (Serialization & Validation)

### Install

```bash
pip install flask-marshmallow marshmallow-sqlalchemy marshmallow
```

### Define Schemas

```python
# app/schemas/user_schema.py
from app.extensions import ma
from app.models.user import User
from marshmallow import fields, validate, validates, ValidationError, post_load, pre_load

class UserSchema(ma.SQLAlchemyAutoSchema):
    """Auto-generates fields from the SQLAlchemy model."""
    class Meta:
        model = User
        load_instance = True          # Deserialize to model instances
        include_relationships = False
        exclude = ('password_hash',)  # Exclude sensitive fields

    # Custom fields
    full_name = fields.Method('get_full_name', dump_only=True)

    # Field-level validation
    username = fields.String(
        required=True,
        validate=[
            validate.Length(min=3, max=80),
            validate.Regexp(r'^[a-zA-Z0-9_]+$', error='Only alphanumeric and underscore')
        ]
    )

    email = fields.Email(required=True)

    # Write-only (only for input, not in output)
    password = fields.String(required=True, load_only=True, validate=validate.Length(min=6))

    def get_full_name(self, obj):
        return f'{obj.first_name} {obj.last_name}'

    # Cross-field validation
    @validates('email')
    def validate_email(self, value):
        if User.query.filter_by(email=value).first():
            raise ValidationError('Email already registered.')
        return value

    # Transform data before loading
    @pre_load
    def normalize_data(self, data, **kwargs):
        if 'email' in data:
            data['email'] = data['email'].lower().strip()
        return data

    # After deserialization
    @post_load
    def make_user(self, data, **kwargs):
        return User(**data)


class UserUpdateSchema(ma.SQLAlchemyAutoSchema):
    class Meta:
        model = User
        exclude = ('password_hash', 'created_at')

    username = fields.String(validate=validate.Length(min=3, max=80))
    email = fields.Email()


class PostSchema(ma.SQLAlchemyAutoSchema):
    class Meta:
        model = Post
        include_relationships = True

    author = fields.Nested(UserSchema, only=('id', 'username'), dump_only=True)
    tags = fields.List(fields.Nested('TagSchema', only=('id', 'name')))
    comment_count = fields.Method('get_comment_count', dump_only=True)

    def get_comment_count(self, obj):
        return obj.comments.count()


# Instantiate schemas
user_schema = UserSchema()
users_schema = UserSchema(many=True)
user_update_schema = UserUpdateSchema()
```

### Using Schemas in Routes

```python
from app.schemas.user_schema import user_schema, users_schema, user_update_schema
from marshmallow import ValidationError

@users_bp.route('/', methods=['GET'])
def get_users():
    users = User.query.all()
    return jsonify(users_schema.dump(users))

@users_bp.route('/<int:id>', methods=['GET'])
def get_user(id):
    user = User.query.get_or_404(id)
    return jsonify(user_schema.dump(user))

@users_bp.route('/', methods=['POST'])
def create_user():
    json_data = request.get_json()
    if not json_data:
        return jsonify({'error': 'No input data'}), 400

    try:
        # Validate and deserialize input
        user = user_schema.load(json_data)
    except ValidationError as err:
        return jsonify({'errors': err.messages}), 422

    db.session.add(user)
    db.session.commit()

    return jsonify(user_schema.dump(user)), 201

@users_bp.route('/<int:id>', methods=['PUT'])
def update_user(id):
    user = User.query.get_or_404(id)
    json_data = request.get_json()

    try:
        updated_user = user_update_schema.load(json_data, instance=user, partial=True)
    except ValidationError as err:
        return jsonify({'errors': err.messages}), 422

    db.session.commit()
    return jsonify(user_schema.dump(updated_user))
```

### Common Marshmallow Fields

```python
fields.String()
fields.Integer()
fields.Float()
fields.Boolean()
fields.DateTime(format='iso')
fields.Date()
fields.Email()
fields.URL()
fields.UUID()
fields.List(fields.String())
fields.Dict()
fields.Nested(OtherSchema)
fields.Method('method_name')       # Computed field from a method
fields.Function(lambda obj: ...)   # Computed from lambda

# Field options
fields.String(
    required=True,           # Must be present on load
    allow_none=True,         # Allow null values
    load_only=True,          # Only for input (password)
    dump_only=True,          # Only for output (id, created_at)
    load_default='default',  # Default value on load
    dump_default='N/A',      # Default value on dump
    data_key='fullName',     # Map from different key name
)
```

---

## 14. Authentication (Flask-JWT-Extended)

### Install

```bash
pip install flask-jwt-extended
```

### Setup

```python
# extensions.py
from flask_jwt_extended import JWTManager
jwt = JWTManager()

# config.py
JWT_SECRET_KEY = 'super-secret'
JWT_ACCESS_TOKEN_EXPIRES = timedelta(hours=1)
JWT_REFRESH_TOKEN_EXPIRES = timedelta(days=30)
JWT_TOKEN_LOCATION = ['headers']           # Where to look for tokens
JWT_HEADER_NAME = 'Authorization'
JWT_HEADER_TYPE = 'Bearer'
```

### Auth Routes

```python
# app/blueprints/auth/routes.py
from flask import Blueprint, request, jsonify
from flask_jwt_extended import (
    create_access_token, create_refresh_token,
    jwt_required, get_jwt_identity, get_jwt,
    unset_jwt_cookies, current_user,
)
from app.models.user import User
from app.extensions import db

auth_bp = Blueprint('auth', __name__)


@auth_bp.route('/register', methods=['POST'])
def register():
    data = request.get_json()
    if User.query.filter_by(email=data['email']).first():
        return jsonify({'error': 'Email already registered'}), 409

    user = User(
        username=data['username'],
        email=data['email'],
    )
    user.set_password(data['password'])
    db.session.add(user)
    db.session.commit()

    access_token = create_access_token(identity=user.id)
    refresh_token = create_refresh_token(identity=user.id)

    return jsonify({
        'message': 'Registration successful',
        'user': user.to_dict(),
        'access_token': access_token,
        'refresh_token': refresh_token,
    }), 201


@auth_bp.route('/login', methods=['POST'])
def login():
    data = request.get_json()
    user = User.query.filter_by(email=data['email']).first()

    if not user or not user.check_password(data['password']):
        return jsonify({'error': 'Invalid credentials'}), 401

    if not user.is_active:
        return jsonify({'error': 'Account is deactivated'}), 403

    # Additional claims in the token
    additional_claims = {'roles': ['admin'] if user.is_admin else ['user']}

    access_token = create_access_token(
        identity=user.id,
        additional_claims=additional_claims,
    )
    refresh_token = create_refresh_token(identity=user.id)

    return jsonify({
        'access_token': access_token,
        'refresh_token': refresh_token,
        'user': user.to_dict(),
    })


@auth_bp.route('/refresh', methods=['POST'])
@jwt_required(refresh=True)  # Only accepts refresh tokens
def refresh():
    user_id = get_jwt_identity()
    access_token = create_access_token(identity=user_id)
    return jsonify({'access_token': access_token})


@auth_bp.route('/logout', methods=['DELETE'])
@jwt_required()
def logout():
    # For stateless JWT, logout is handled client-side by deleting the token.
    # For server-side blacklisting:
    jti = get_jwt()['jti']
    # add_to_blocklist(jti)  # Store in Redis/DB
    return jsonify({'message': 'Successfully logged out'})


@auth_bp.route('/me', methods=['GET'])
@jwt_required()
def get_me():
    user_id = get_jwt_identity()
    user = User.query.get(user_id)
    return jsonify(user.to_dict())
```

### Protecting Routes

```python
from flask_jwt_extended import jwt_required, get_jwt_identity, get_jwt

@users_bp.route('/profile', methods=['GET'])
@jwt_required()                            # Requires valid access token
def profile():
    user_id = get_jwt_identity()           # Gets the identity (user.id)
    user = User.query.get(user_id)
    return jsonify(user.to_dict())

@users_bp.route('/optional', methods=['GET'])
@jwt_required(optional=True)              # Works with or without token
def optional():
    user_id = get_jwt_identity()           # None if no token
    return jsonify({'logged_in': user_id is not None})

# Access all claims in the token
@users_bp.route('/admin', methods=['GET'])
@jwt_required()
def admin_only():
    claims = get_jwt()
    if 'admin' not in claims.get('roles', []):
        return jsonify({'error': 'Admins only'}), 403
    return jsonify({'message': 'Welcome, admin!'})
```

### JWT Callbacks (Customization)

```python
# app/__init__.py or extensions setup

@jwt.user_identity_loader
def user_identity_lookup(user):
    return user.id  # What to store in token identity

@jwt.user_lookup_loader
def user_lookup_callback(_jwt_header, jwt_data):
    identity = jwt_data['sub']
    return User.query.get(identity)  # Now you can use current_user directly

@jwt.expired_token_loader
def expired_token_callback(jwt_header, jwt_payload):
    return jsonify({'error': 'Token has expired', 'code': 'TOKEN_EXPIRED'}), 401

@jwt.invalid_token_loader
def invalid_token_callback(error):
    return jsonify({'error': 'Invalid token', 'code': 'INVALID_TOKEN'}), 401

@jwt.unauthorized_loader
def missing_token_callback(error):
    return jsonify({'error': 'Token is missing', 'code': 'AUTHORIZATION_REQUIRED'}), 401

# Token blocklist (for logout blacklisting)
BLOCKLIST = set()  # Use Redis in production

@jwt.token_in_blocklist_loader
def check_if_token_in_blocklist(jwt_header, jwt_payload):
    return jwt_payload['jti'] in BLOCKLIST
```

---

## 15. Authentication (Flask-Login — Session Based)

### Install

```bash
pip install flask-login
```

### Setup

```python
# extensions.py
from flask_login import LoginManager
login_manager = LoginManager()
login_manager.login_view = 'auth.login'       # Redirect to this view if not logged in
login_manager.login_message = 'Please log in.'

# __init__.py
login_manager.init_app(app)

@login_manager.user_loader
def load_user(user_id):
    return User.query.get(int(user_id))
```

### User Model Mixin

```python
from flask_login import UserMixin

class User(db.Model, UserMixin):
    # UserMixin provides:
    # is_authenticated, is_active, is_anonymous, get_id()
    id = db.Column(db.Integer, primary_key=True)
    ...

    # Optional: override is_active
    @property
    def is_active(self):
        return self.active
```

### Login/Logout

```python
from flask_login import login_user, logout_user, login_required, current_user

@auth_bp.route('/login', methods=['POST'])
def login():
    data = request.get_json()
    user = User.query.filter_by(email=data['email']).first()

    if user and user.check_password(data['password']):
        login_user(user, remember=data.get('remember', False))
        return jsonify({'message': 'Logged in', 'user': user.to_dict()})

    return jsonify({'error': 'Invalid credentials'}), 401


@auth_bp.route('/logout')
@login_required
def logout():
    logout_user()
    return jsonify({'message': 'Logged out'})


@users_bp.route('/profile')
@login_required
def profile():
    return jsonify(current_user.to_dict())
```

---

## 16. Forms (Flask-WTF)

### Install

```bash
pip install flask-wtf wtforms
```

### Define a Form

```python
# app/forms.py
from flask_wtf import FlaskForm
from wtforms import (
    StringField, PasswordField, EmailField, TextAreaField,
    BooleanField, SelectField, IntegerField, FloatField,
    SubmitField, FileField, MultipleFileField,
)
from wtforms.validators import (
    DataRequired, Email, EqualTo, Length, NumberRange,
    Optional, URL, ValidationError, Regexp,
)
from app.models.user import User


class RegistrationForm(FlaskForm):
    username = StringField('Username', validators=[
        DataRequired(),
        Length(min=3, max=80),
        Regexp(r'^[a-zA-Z0-9_]+$', message='Only letters, numbers, underscore')
    ])

    email = EmailField('Email', validators=[DataRequired(), Email()])

    password = PasswordField('Password', validators=[
        DataRequired(),
        Length(min=6, message='Password must be at least 6 characters')
    ])

    confirm_password = PasswordField('Confirm Password', validators=[
        DataRequired(),
        EqualTo('password', message='Passwords must match')
    ])

    agree_terms = BooleanField('I agree to terms', validators=[DataRequired()])

    submit = SubmitField('Register')

    # Custom validator: method must be named validate_<fieldname>
    def validate_email(self, field):
        if User.query.filter_by(email=field.data).first():
            raise ValidationError('Email already taken.')

    def validate_username(self, field):
        if User.query.filter_by(username=field.data).first():
            raise ValidationError('Username already taken.')


class LoginForm(FlaskForm):
    email = EmailField('Email', validators=[DataRequired(), Email()])
    password = PasswordField('Password', validators=[DataRequired()])
    remember = BooleanField('Remember Me')
    submit = SubmitField('Login')


class ProfileForm(FlaskForm):
    bio = TextAreaField('Bio', validators=[Optional(), Length(max=500)])
    website = StringField('Website', validators=[Optional(), URL()])
    role = SelectField('Role', choices=[('user', 'User'), ('admin', 'Admin')])
    age = IntegerField('Age', validators=[Optional(), NumberRange(min=0, max=120)])
    submit = SubmitField('Save')
```

### Use Form in Route

```python
@auth_bp.route('/register', methods=['GET', 'POST'])
def register():
    form = RegistrationForm()

    if form.validate_on_submit():  # POST + valid
        user = User(
            username=form.username.data,
            email=form.email.data,
        )
        user.set_password(form.password.data)
        db.session.add(user)
        db.session.commit()
        flash('Account created! Please log in.', 'success')
        return redirect(url_for('auth.login'))

    return render_template('auth/register.html', form=form)
```

### Form in Template

```html
<form method="POST" action="">
    {{ form.hidden_tag() }}  {# CSRF token — required! #}

    <div>
        {{ form.username.label }}
        {{ form.username(class='form-control', placeholder='Enter username') }}
        {% for error in form.username.errors %}
            <span class="error">{{ error }}</span>
        {% endfor %}
    </div>

    <div>
        {{ form.email.label }}
        {{ form.email(class='form-control') }}
        {% for error in form.email.errors %}
            <span class="error">{{ error }}</span>
        {% endfor %}
    </div>

    {{ form.submit(class='btn btn-primary') }}
</form>
```

---

## 17. Error Handling

### HTTP Error Handlers

```python
# app/errors/handlers.py
from flask import jsonify


def register_error_handlers(app):

    @app.errorhandler(400)
    def bad_request(error):
        return jsonify({'error': 'Bad Request', 'message': str(error)}), 400

    @app.errorhandler(401)
    def unauthorized(error):
        return jsonify({'error': 'Unauthorized', 'message': 'Authentication required'}), 401

    @app.errorhandler(403)
    def forbidden(error):
        return jsonify({'error': 'Forbidden', 'message': 'You do not have permission'}), 403

    @app.errorhandler(404)
    def not_found(error):
        return jsonify({'error': 'Not Found', 'message': str(error)}), 404

    @app.errorhandler(405)
    def method_not_allowed(error):
        return jsonify({'error': 'Method Not Allowed'}), 405

    @app.errorhandler(409)
    def conflict(error):
        return jsonify({'error': 'Conflict', 'message': str(error)}), 409

    @app.errorhandler(422)
    def unprocessable_entity(error):
        return jsonify({'error': 'Unprocessable Entity', 'message': str(error)}), 422

    @app.errorhandler(429)
    def too_many_requests(error):
        return jsonify({'error': 'Too Many Requests', 'message': 'Rate limit exceeded'}), 429

    @app.errorhandler(500)
    def internal_server_error(error):
        db.session.rollback()
        return jsonify({'error': 'Internal Server Error'}), 500
```

### Custom Exception Classes

```python
# app/errors/exceptions.py

class APIError(Exception):
    """Base API error."""
    status_code = 500
    message = 'An unexpected error occurred.'

    def __init__(self, message=None, status_code=None, payload=None):
        super().__init__()
        if message:
            self.message = message
        if status_code:
            self.status_code = status_code
        self.payload = payload

    def to_dict(self):
        rv = dict(self.payload or ())
        rv['error'] = self.__class__.__name__
        rv['message'] = self.message
        return rv


class NotFoundError(APIError):
    status_code = 404
    message = 'Resource not found.'


class ValidationError(APIError):
    status_code = 422
    message = 'Validation failed.'


class AuthenticationError(APIError):
    status_code = 401
    message = 'Authentication required.'


class ForbiddenError(APIError):
    status_code = 403
    message = 'Permission denied.'


class ConflictError(APIError):
    status_code = 409
    message = 'Resource already exists.'


# Register custom error handler
@app.errorhandler(APIError)
def handle_api_error(error):
    return jsonify(error.to_dict()), error.status_code

# Usage in routes:
def get_user(id):
    user = User.query.get(id)
    if not user:
        raise NotFoundError(f'User with id {id} not found.')
    return user
```

### `abort()` — Raise HTTP Errors

```python
from flask import abort

@app.route('/users/<int:id>')
def get_user(id):
    user = User.query.get(id)
    if not user:
        abort(404, description='User not found')
    return jsonify(user.to_dict())

abort(400, 'Bad input')
abort(401)
abort(403, 'Admins only')
abort(422, 'Validation failed')
```

---

## 18. Middleware & Hooks (Before/After Request)

```python
from flask import g, request, jsonify
import time

# Runs before every request in the app
@app.before_request
def before_request():
    g.start_time = time.time()
    print(f'→ {request.method} {request.path}')

# Runs after every request (even if exception was raised)
@app.after_request
def after_request(response):
    elapsed = time.time() - g.get('start_time', time.time())
    response.headers['X-Response-Time'] = f'{elapsed:.4f}s'
    response.headers['X-Content-Type-Options'] = 'nosniff'
    print(f'← {response.status_code} ({elapsed:.4f}s)')
    return response

# Runs only if an unhandled exception occurred
@app.teardown_request
def teardown_request(exception=None):
    if exception:
        db.session.rollback()
    db.session.remove()

# Runs when the app context tears down
@app.teardown_appcontext
def teardown_appcontext(exception=None):
    db.session.remove()

# Blueprint-level hooks (only for that blueprint)
@users_bp.before_request
def before_users_request():
    # Runs before any request to /api/users/*
    print('Users blueprint request')
```

### Authentication Middleware Example

```python
@app.before_request
def authenticate():
    # Skip auth for public endpoints
    public_endpoints = ['auth.login', 'auth.register', 'static']
    if request.endpoint in public_endpoints:
        return

    token = request.headers.get('Authorization', '').replace('Bearer ', '')
    if not token:
        return jsonify({'error': 'Token missing'}), 401

    user = verify_token(token)
    if not user:
        return jsonify({'error': 'Invalid token'}), 401

    g.current_user = user
```

---

## 19. Flask-CORS

### Install

```bash
pip install flask-cors
```

### Setup

```python
from flask_cors import CORS

# Allow all origins (development only!)
CORS(app)

# Allow specific origins
CORS(app, origins=['http://localhost:3000', 'https://myapp.com'])

# Fine-grained control
CORS(app,
    origins=['http://localhost:3000'],
    methods=['GET', 'POST', 'PUT', 'PATCH', 'DELETE', 'OPTIONS'],
    allow_headers=['Content-Type', 'Authorization'],
    expose_headers=['X-Total-Count'],
    supports_credentials=True,
    max_age=600,
)

# Per-blueprint CORS
CORS(users_bp, origins=['http://localhost:3000'])

# Per-route CORS
from flask_cors import cross_origin

@app.route('/api/public')
@cross_origin(origins='*')
def public():
    return jsonify({'data': 'public'})
```

---

## 20. File Upload

```python
import os
from flask import request, jsonify, send_from_directory
from werkzeug.utils import secure_filename
import uuid

UPLOAD_FOLDER = 'uploads'
ALLOWED_EXTENSIONS = {'png', 'jpg', 'jpeg', 'gif', 'pdf', 'docx'}
MAX_FILE_SIZE = 16 * 1024 * 1024  # 16 MB

app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER
app.config['MAX_CONTENT_LENGTH'] = MAX_FILE_SIZE


def allowed_file(filename):
    return '.' in filename and \
           filename.rsplit('.', 1)[1].lower() in ALLOWED_EXTENSIONS


@app.route('/upload', methods=['POST'])
def upload_file():
    # Check if the post request has the file part
    if 'file' not in request.files:
        return jsonify({'error': 'No file part'}), 400

    file = request.files['file']

    if file.filename == '':
        return jsonify({'error': 'No file selected'}), 400

    if not allowed_file(file.filename):
        return jsonify({'error': 'File type not allowed'}), 400

    # Secure the filename + add unique ID to avoid collisions
    original_filename = secure_filename(file.filename)
    ext = original_filename.rsplit('.', 1)[1].lower()
    unique_filename = f'{uuid.uuid4().hex}.{ext}'

    # Ensure upload directory exists
    os.makedirs(app.config['UPLOAD_FOLDER'], exist_ok=True)

    filepath = os.path.join(app.config['UPLOAD_FOLDER'], unique_filename)
    file.save(filepath)

    return jsonify({
        'message': 'File uploaded successfully',
        'filename': unique_filename,
        'original_name': original_filename,
        'url': f'/uploads/{unique_filename}',
    }), 201


# Multiple file upload
@app.route('/upload/multiple', methods=['POST'])
def upload_multiple():
    files = request.files.getlist('files')
    uploaded = []

    for file in files:
        if file and allowed_file(file.filename):
            filename = f'{uuid.uuid4().hex}.{file.filename.rsplit(".", 1)[1].lower()}'
            file.save(os.path.join(app.config['UPLOAD_FOLDER'], filename))
            uploaded.append(filename)

    return jsonify({'uploaded': uploaded}), 201


# Serve uploaded files
@app.route('/uploads/<filename>')
def serve_file(filename):
    return send_from_directory(app.config['UPLOAD_FOLDER'], filename)
```

---

## 21. Email (Flask-Mail)

### Install

```bash
pip install flask-mail
```

### Setup

```python
# extensions.py
from flask_mail import Mail
mail = Mail()

# config.py
MAIL_SERVER = 'smtp.gmail.com'
MAIL_PORT = 587
MAIL_USE_TLS = True
MAIL_USERNAME = 'your@gmail.com'
MAIL_PASSWORD = 'your-app-password'
MAIL_DEFAULT_SENDER = 'your@gmail.com'
```

### Send Emails

```python
from flask_mail import Message
from app.extensions import mail

def send_email(to, subject, body, html=None):
    msg = Message(
        subject=subject,
        recipients=[to] if isinstance(to, str) else to,
        body=body,        # Plain text
        html=html,        # HTML version
    )
    mail.send(msg)


def send_welcome_email(user):
    send_email(
        to=user.email,
        subject='Welcome to My App!',
        body=f'Hi {user.username}, welcome!',
        html=f'<h1>Hi {user.username}</h1><p>Welcome to <b>My App</b>!</p>',
    )


def send_password_reset_email(user, token):
    reset_url = url_for('auth.reset_password', token=token, _external=True)
    send_email(
        to=user.email,
        subject='Password Reset Request',
        body=f'Click here to reset your password: {reset_url}',
        html=render_template('email/reset_password.html', user=user, url=reset_url),
    )


# Async email sending (with threading)
from threading import Thread

def send_async_email(app, msg):
    with app.app_context():
        mail.send(msg)

def send_email_async(to, subject, body):
    msg = Message(subject=subject, recipients=[to], body=body)
    thread = Thread(target=send_async_email, args=[current_app._get_current_object(), msg])
    thread.start()
```

---

## 22. Caching (Flask-Caching)

### Install

```bash
pip install flask-caching redis
```

### Setup

```python
# extensions.py
from flask_caching import Cache
cache = Cache()

# config.py
CACHE_TYPE = 'RedisCache'              # or 'SimpleCache' for in-memory
CACHE_REDIS_URL = 'redis://localhost:6379/0'
CACHE_DEFAULT_TIMEOUT = 300            # 5 minutes
```

### Usage

```python
from app.extensions import cache

# Cache a route's return value for 60 seconds
@app.route('/expensive')
@cache.cached(timeout=60)
def expensive_view():
    return jsonify(run_expensive_query())

# Cache with dynamic key (different cache per user)
@app.route('/profile/<int:user_id>')
@cache.cached(timeout=300, key_prefix='profile_%s')
def get_profile(user_id):
    return jsonify(User.query.get(user_id).to_dict())

# Cache memoize (uses function args as key)
@cache.memoize(timeout=600)
def get_user_posts(user_id, page=1):
    return Post.query.filter_by(author_id=user_id).paginate(page=page).items

# Manual cache operations
def get_stats():
    key = 'global_stats'
    stats = cache.get(key)
    if stats is None:
        stats = compute_expensive_stats()
        cache.set(key, stats, timeout=3600)
    return stats

# Invalidate cache
cache.delete('global_stats')
cache.delete_memoized(get_user_posts, user_id=1)
cache.clear()  # Clear ALL cache — use carefully!

# Cache multiple values at once
cache.set_many({'key1': 'val1', 'key2': 'val2'}, timeout=60)
values = cache.get_many('key1', 'key2')
```

---

## 23. Rate Limiting (Flask-Limiter)

### Install

```bash
pip install flask-limiter
```

### Setup

```python
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

limiter = Limiter(
    app=app,
    key_func=get_remote_address,       # Rate limit per IP
    default_limits=['200 per day', '50 per hour'],
    storage_uri='redis://localhost:6379',
)
```

### Usage

```python
from app.extensions import limiter

# Override default limit
@app.route('/api/search')
@limiter.limit('10 per minute')
def search():
    return jsonify({'results': []})

# Stricter limit for auth
@auth_bp.route('/login', methods=['POST'])
@limiter.limit('5 per minute')
def login():
    pass

# Exempt certain routes
@app.route('/health')
@limiter.exempt
def health():
    return 'OK'

# Custom key (per user instead of per IP)
@app.route('/api/users')
@limiter.limit('100 per hour', key_func=lambda: get_jwt_identity())
def get_users():
    pass
```

---

## 24. WebSockets (Flask-SocketIO)

### Install

```bash
pip install flask-socketio eventlet
```

### Setup

```python
from flask_socketio import SocketIO, emit, join_room, leave_room, send

socketio = SocketIO(app, cors_allowed_origins='*', async_mode='eventlet')

# Run with SocketIO
if __name__ == '__main__':
    socketio.run(app, debug=True)
```

### Events

```python
from flask_socketio import SocketIO, emit, join_room, leave_room, disconnect

@socketio.on('connect')
def on_connect():
    print(f'Client connected: {request.sid}')
    emit('connected', {'data': 'Connected to server'})

@socketio.on('disconnect')
def on_disconnect():
    print(f'Client disconnected: {request.sid}')

@socketio.on('message')
def handle_message(data):
    print(f'Received: {data}')
    send({'reply': f'Echo: {data["text"]}'})  # Send back to sender

@socketio.on('chat')
def handle_chat(data):
    room = data.get('room', 'general')
    emit('new_message', {
        'user': data['user'],
        'message': data['message'],
        'timestamp': datetime.utcnow().isoformat(),
    }, room=room)  # Emit to all in room

@socketio.on('join')
def on_join(data):
    room = data['room']
    join_room(room)
    emit('status', {'msg': f'{data["username"]} joined room {room}'}, room=room)

@socketio.on('leave')
def on_leave(data):
    room = data['room']
    leave_room(room)
    emit('status', {'msg': f'{data["username"]} left room {room}'}, room=room)

# Emit from outside a socket handler (e.g., from an HTTP route)
@app.route('/notify')
def notify():
    socketio.emit('notification', {'message': 'Hello everyone!'})
    return 'Notified'
```

---

## 25. Task Queues (Celery + Redis)

### Install

```bash
pip install celery redis
```

### Setup

```python
# celery_worker.py
from celery import Celery
from app import create_app

def make_celery(app):
    celery = Celery(app.import_name)
    celery.conf.update(
        broker_url=app.config['CELERY_BROKER_URL'],
        result_backend=app.config['CELERY_RESULT_BACKEND'],
        task_serializer='json',
        accept_content=['json'],
        result_serializer='json',
        timezone='UTC',
    )

    class ContextTask(celery.Task):
        def __call__(self, *args, **kwargs):
            with app.app_context():
                return self.run(*args, **kwargs)

    celery.Task = ContextTask
    return celery


flask_app = create_app()
celery = make_celery(flask_app)
```

### Define Tasks

```python
# app/tasks.py
from celery_worker import celery
from app.extensions import mail
from flask_mail import Message

@celery.task(bind=True, max_retries=3)
def send_email_task(self, to, subject, body):
    try:
        msg = Message(subject=subject, recipients=[to], body=body)
        mail.send(msg)
    except Exception as exc:
        raise self.retry(exc=exc, countdown=60)  # Retry after 60 seconds


@celery.task
def process_image(filepath):
    # Long-running image processing
    import time
    time.sleep(10)
    return {'status': 'processed', 'file': filepath}


# Periodic task (Celery Beat)
from celery.schedules import crontab

celery.conf.beat_schedule = {
    'cleanup-every-midnight': {
        'task': 'app.tasks.cleanup_expired_tokens',
        'schedule': crontab(hour=0, minute=0),
    },
    'send-weekly-digest': {
        'task': 'app.tasks.send_digest',
        'schedule': crontab(day_of_week=1, hour=8),  # Monday 8am
    },
}

@celery.task
def cleanup_expired_tokens():
    # ... cleanup logic
    pass
```

### Use Tasks in Routes

```python
@app.route('/send-email', methods=['POST'])
def trigger_email():
    data = request.get_json()

    # Queue the task (non-blocking)
    task = send_email_task.delay(data['to'], data['subject'], data['body'])

    return jsonify({'task_id': task.id, 'status': 'queued'}), 202


@app.route('/task/<task_id>')
def get_task_status(task_id):
    task = send_email_task.AsyncResult(task_id)
    return jsonify({
        'task_id': task_id,
        'status': task.status,     # PENDING, STARTED, SUCCESS, FAILURE, RETRY
        'result': task.result if task.ready() else None,
    })
```

### Run Celery

```bash
# Start worker
celery -A celery_worker.celery worker --loglevel=info

# Start beat (for scheduled tasks)
celery -A celery_worker.celery beat --loglevel=info

# Start both (development only)
celery -A celery_worker.celery worker --beat --loglevel=info
```

---

## 26. REST API with Flask-RESTful

### Install

```bash
pip install flask-restful
```

### Usage

```python
from flask import Flask
from flask_restful import Api, Resource, reqparse, fields, marshal_with

app = Flask(__name__)
api = Api(app)

# Output fields (serialization)
user_fields = {
    'id': fields.Integer,
    'username': fields.String,
    'email': fields.String,
    'created_at': fields.DateTime(dt_format='iso8601'),
}

# Request parser (validation)
user_parser = reqparse.RequestParser()
user_parser.add_argument('username', type=str, required=True, help='Username is required')
user_parser.add_argument('email', type=str, required=True, help='Email is required')
user_parser.add_argument('password', type=str, required=True)


class UserListResource(Resource):
    @marshal_with(user_fields)
    def get(self):
        return User.query.all()

    @marshal_with(user_fields)
    def post(self):
        args = user_parser.parse_args()
        user = User(**args)
        db.session.add(user)
        db.session.commit()
        return user, 201


class UserResource(Resource):
    @marshal_with(user_fields)
    def get(self, user_id):
        return User.query.get_or_404(user_id)

    @marshal_with(user_fields)
    def put(self, user_id):
        user = User.query.get_or_404(user_id)
        args = user_parser.parse_args()
        for key, value in args.items():
            setattr(user, key, value)
        db.session.commit()
        return user

    def delete(self, user_id):
        user = User.query.get_or_404(user_id)
        db.session.delete(user)
        db.session.commit()
        return '', 204


api.add_resource(UserListResource, '/api/users')
api.add_resource(UserResource, '/api/users/<int:user_id>')
```

---

## 27. REST API with Flask-RESTX (Swagger included)

### Install

```bash
pip install flask-restx
```

### Usage (auto-generates Swagger UI at `/`)

```python
from flask import Flask
from flask_restx import Api, Resource, fields, Namespace

app = Flask(__name__)
api = Api(app,
    version='1.0',
    title='My API',
    description='A complete API example',
    doc='/docs'  # Swagger UI at /docs
)

# Namespaces = blueprints for RESTX
ns = api.namespace('users', description='User operations')

# Define models for Swagger docs + validation
user_model = api.model('User', {
    'id': fields.Integer(readonly=True),
    'username': fields.String(required=True, min_length=3),
    'email': fields.String(required=True),
    'created_at': fields.DateTime(readonly=True),
})

create_user_model = api.model('CreateUser', {
    'username': fields.String(required=True),
    'email': fields.String(required=True),
    'password': fields.String(required=True),
})


@ns.route('/')
class UserList(Resource):
    @ns.doc('list_users')
    @ns.marshal_list_with(user_model)
    def get(self):
        """List all users"""
        return User.query.all()

    @ns.doc('create_user')
    @ns.expect(create_user_model, validate=True)
    @ns.marshal_with(user_model, code=201)
    def post(self):
        """Create a new user"""
        data = api.payload
        user = User(**data)
        db.session.add(user)
        db.session.commit()
        return user, 201


@ns.route('/<int:id>')
@ns.param('id', 'The user identifier')
@ns.response(404, 'User not found')
class UserItem(Resource):
    @ns.doc('get_user')
    @ns.marshal_with(user_model)
    def get(self, id):
        """Get a user by ID"""
        return User.query.get_or_404(id)

    @ns.doc('delete_user')
    @ns.response(204, 'User deleted')
    def delete(self, id):
        """Delete a user"""
        user = User.query.get_or_404(id)
        db.session.delete(user)
        db.session.commit()
        return '', 204
```

---

## 28. Testing

### Install

```bash
pip install pytest pytest-flask coverage
```

### `tests/conftest.py` — Test Fixtures

```python
import pytest
from app import create_app
from app.extensions import db as _db

@pytest.fixture(scope='session')
def app():
    app = create_app('testing')
    return app

@pytest.fixture(scope='session')
def client(app):
    return app.test_client()

@pytest.fixture(scope='function')
def db(app):
    with app.app_context():
        _db.create_all()
        yield _db
        _db.session.remove()
        _db.drop_all()

@pytest.fixture
def auth_headers(client, db):
    # Register and login to get a token
    client.post('/api/auth/register', json={
        'username': 'testuser',
        'email': 'test@example.com',
        'password': 'password123',
    })
    response = client.post('/api/auth/login', json={
        'email': 'test@example.com',
        'password': 'password123',
    })
    token = response.get_json()['access_token']
    return {'Authorization': f'Bearer {token}'}
```

### Writing Tests

```python
# tests/test_users.py
import pytest
import json

def test_get_users_empty(client, db):
    response = client.get('/api/users/')
    assert response.status_code == 200
    assert response.get_json() == []

def test_create_user(client, db):
    response = client.post('/api/users/', json={
        'username': 'john',
        'email': 'john@example.com',
        'password': 'password123',
    })
    assert response.status_code == 201
    data = response.get_json()
    assert data['username'] == 'john'
    assert data['email'] == 'john@example.com'
    assert 'password' not in data  # Password should not be returned

def test_create_user_duplicate_email(client, db):
    payload = {'username': 'john', 'email': 'john@example.com', 'password': 'pass'}
    client.post('/api/users/', json=payload)
    response = client.post('/api/users/', json=payload)
    assert response.status_code == 409

def test_get_user(client, db):
    # First create
    create_response = client.post('/api/users/', json={
        'username': 'jane',
        'email': 'jane@example.com',
        'password': 'pass',
    })
    user_id = create_response.get_json()['id']

    # Then get
    response = client.get(f'/api/users/{user_id}')
    assert response.status_code == 200
    assert response.get_json()['username'] == 'jane'

def test_get_nonexistent_user(client, db):
    response = client.get('/api/users/9999')
    assert response.status_code == 404

def test_protected_route_requires_auth(client, db):
    response = client.get('/api/users/profile')
    assert response.status_code == 401

def test_protected_route_with_auth(client, db, auth_headers):
    response = client.get('/api/users/profile', headers=auth_headers)
    assert response.status_code == 200

def test_update_user(client, db, auth_headers):
    response = client.patch('/api/users/1', json={'bio': 'Hello!'}, headers=auth_headers)
    assert response.status_code == 200
    assert response.get_json()['bio'] == 'Hello!'
```

### Running Tests

```bash
pytest                         # Run all tests
pytest tests/test_users.py     # Run specific file
pytest -v                      # Verbose output
pytest -k "test_create"        # Run tests matching name
pytest --cov=app               # With coverage report
pytest --cov=app --cov-report=html  # HTML coverage report
```

---

## 29. Logging

```python
import logging
from logging.handlers import RotatingFileHandler
import os

def configure_logging(app):
    if not app.debug:
        # File logging
        if not os.path.exists('logs'):
            os.mkdir('logs')

        file_handler = RotatingFileHandler(
            'logs/app.log',
            maxBytes=10240,    # 10KB
            backupCount=10,    # Keep 10 backup files
        )
        file_handler.setFormatter(logging.Formatter(
            '%(asctime)s %(levelname)s: %(message)s [in %(pathname)s:%(lineno)d]'
        ))
        file_handler.setLevel(logging.INFO)
        app.logger.addHandler(file_handler)

    # Console logging
    stream_handler = logging.StreamHandler()
    stream_handler.setLevel(logging.DEBUG)
    stream_handler.setFormatter(logging.Formatter(
        '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
    ))
    app.logger.addHandler(stream_handler)
    app.logger.setLevel(logging.DEBUG)


# Usage in routes or services
from flask import current_app

@app.route('/example')
def example():
    current_app.logger.debug('This is a debug message')
    current_app.logger.info('User logged in')
    current_app.logger.warning('Disk space low')
    current_app.logger.error('Database connection failed')
    current_app.logger.critical('System crash!')
    return 'OK'
```

---

## 30. CLI Commands

### Built-in Flask CLI

```bash
flask run                        # Start dev server
flask shell                      # Python shell with app context
flask db init                    # Init migrations
flask db migrate -m "message"    # Generate migration
flask db upgrade                 # Apply migrations
flask db downgrade               # Revert migration
flask routes                     # List all registered routes
```

### Custom CLI Commands

```python
# app/__init__.py or a separate cli.py
import click
from flask.cli import with_appcontext

@app.cli.command('create-admin')
@click.option('--username', prompt=True)
@click.option('--email', prompt=True)
@click.option('--password', prompt=True, hide_input=True)
def create_admin(username, email, password):
    """Create an admin user."""
    user = User(username=username, email=email, is_admin=True)
    user.set_password(password)
    db.session.add(user)
    db.session.commit()
    click.echo(f'Admin user {username} created!')


@app.cli.command('seed-db')
@with_appcontext
def seed_db():
    """Seed the database with test data."""
    users = [
        User(username='alice', email='alice@example.com'),
        User(username='bob', email='bob@example.com'),
    ]
    for user in users:
        user.set_password('password123')
        db.session.add(user)
    db.session.commit()
    click.echo(f'Seeded {len(users)} users.')


@app.cli.command('clear-cache')
def clear_cache():
    """Clear the application cache."""
    cache.clear()
    click.echo('Cache cleared.')
```

```bash
# Use custom commands
flask create-admin
flask seed-db
flask clear-cache
```

---

## 31. Deployment

### Gunicorn (Production WSGI Server)

```bash
pip install gunicorn

# Run with Gunicorn
gunicorn run:app

# With options
gunicorn -w 4 -b 0.0.0.0:5000 run:app

# -w: number of workers (usually 2*CPU + 1)
# -b: bind address
# --timeout: worker timeout in seconds
# --access-logfile: log file path

gunicorn -w 4 -b 0.0.0.0:5000 \
         --timeout 120 \
         --access-logfile logs/access.log \
         --error-logfile logs/error.log \
         run:app
```

### `Procfile` (for Heroku / Railway)

```
web: gunicorn run:app
worker: celery -A celery_worker.celery worker --loglevel=info
```

### `Dockerfile`

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

ENV FLASK_ENV=production
ENV FLASK_APP=run.py

EXPOSE 5000

CMD ["gunicorn", "-w", "4", "-b", "0.0.0.0:5000", "run:app"]
```

### `docker-compose.yml`

```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "5000:5000"
    env_file: .env
    depends_on:
      - db
      - redis

  db:
    image: postgres:15
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  celery_worker:
    build: .
    command: celery -A celery_worker.celery worker --loglevel=info
    env_file: .env
    depends_on:
      - db
      - redis

volumes:
  postgres_data:
```

### Nginx Config (Reverse Proxy)

```nginx
server {
    listen 80;
    server_name myapp.com;

    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /static {
        alias /var/www/myapp/static;
        expires 30d;
    }

    location /uploads {
        alias /var/www/myapp/uploads;
    }
}
```

---

## 32. Security Best Practices

```python
from flask_talisman import Talisman

# 1. HTTPS Everywhere
Talisman(app, force_https=True)

# 2. Secure secret key
app.config['SECRET_KEY'] = os.urandom(32)  # Generate randomly

# 3. JWT — short expiry + refresh tokens
JWT_ACCESS_TOKEN_EXPIRES = timedelta(minutes=15)
JWT_REFRESH_TOKEN_EXPIRES = timedelta(days=7)

# 4. Password hashing (never store plain text)
import bcrypt
hashed = bcrypt.hashpw(password.encode(), bcrypt.gensalt())
bcrypt.checkpw(password.encode(), hashed)

# 5. Input validation — always validate and whitelist
@app.route('/users', methods=['POST'])
def create_user():
    data = request.get_json()
    # Use marshmallow schema to validate
    try:
        user = user_schema.load(data)
    except ValidationError as err:
        return jsonify({'errors': err.messages}), 422

# 6. SQL Injection prevention — use SQLAlchemy ORM (parameterized queries)
# SAFE:
User.query.filter_by(username=username).first()
# NEVER DO THIS (raw string concatenation):
db.engine.execute(f"SELECT * FROM users WHERE username = '{username}'")

# 7. CSRF protection (for form-based apps)
from flask_wtf.csrf import CSRFProtect
csrf = CSRFProtect(app)

# 8. Rate limiting (already covered in section 23)

# 9. Security headers
@app.after_request
def security_headers(response):
    response.headers['X-Content-Type-Options'] = 'nosniff'
    response.headers['X-Frame-Options'] = 'DENY'
    response.headers['X-XSS-Protection'] = '1; mode=block'
    response.headers['Strict-Transport-Security'] = 'max-age=31536000; includeSubDomains'
    response.headers['Referrer-Policy'] = 'strict-origin-when-cross-origin'
    return response

# 10. Environment variables — never hardcode secrets
# Use python-dotenv and .env files (add .env to .gitignore!)

# 11. Logging — log security events
@auth_bp.route('/login', methods=['POST'])
def login():
    data = request.get_json()
    user = User.query.filter_by(email=data['email']).first()
    if not user or not user.check_password(data['password']):
        current_app.logger.warning(f'Failed login attempt for {data.get("email")} from {request.remote_addr}')
        return jsonify({'error': 'Invalid credentials'}), 401
```

---

## 33. Common Patterns & Tips

### Generic API Response

```python
# app/utils/response.py
from flask import jsonify

def success_response(data=None, message='Success', status_code=200):
    return jsonify({
        'success': True,
        'message': message,
        'data': data,
    }), status_code

def error_response(message='Error', errors=None, status_code=400):
    return jsonify({
        'success': False,
        'message': message,
        'errors': errors,
    }), status_code

def paginated_response(items, total, page, per_page):
    return jsonify({
        'success': True,
        'data': items,
        'meta': {
            'total': total,
            'page': page,
            'per_page': per_page,
            'total_pages': -(-total // per_page),  # ceiling division
        }
    })
```

### Decorators for DRY Code

```python
# app/utils/decorators.py
from functools import wraps
from flask import jsonify
from flask_jwt_extended import get_jwt_identity, verify_jwt_in_request
from app.models.user import User

def admin_required(fn):
    @wraps(fn)
    def wrapper(*args, **kwargs):
        verify_jwt_in_request()
        user_id = get_jwt_identity()
        user = User.query.get(user_id)
        if not user or not user.is_admin:
            return jsonify({'error': 'Admin access required'}), 403
        return fn(*args, **kwargs)
    return wrapper

def active_user_required(fn):
    @wraps(fn)
    def wrapper(*args, **kwargs):
        verify_jwt_in_request()
        user_id = get_jwt_identity()
        user = User.query.get(user_id)
        if not user or not user.is_active:
            return jsonify({'error': 'Account is deactivated'}), 403
        return fn(*args, **kwargs)
    return wrapper

# Usage
@users_bp.route('/admin-panel')
@jwt_required()
@admin_required
def admin_panel():
    return jsonify({'data': 'Admin stuff'})
```

### Soft Delete Mixin

```python
# app/models/mixins.py
from app.extensions import db
from datetime import datetime

class SoftDeleteMixin:
    deleted_at = db.Column(db.DateTime, nullable=True)

    def soft_delete(self):
        self.deleted_at = datetime.utcnow()
        db.session.commit()

    def restore(self):
        self.deleted_at = None
        db.session.commit()

    @property
    def is_deleted(self):
        return self.deleted_at is not None

    @classmethod
    def query_active(cls):
        return cls.query.filter(cls.deleted_at.is_(None))


class TimestampMixin:
    created_at = db.Column(db.DateTime, default=datetime.utcnow)
    updated_at = db.Column(db.DateTime, default=datetime.utcnow, onupdate=datetime.utcnow)


# Usage
class Post(db.Model, TimestampMixin, SoftDeleteMixin):
    __tablename__ = 'posts'
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(200))
```

### Password Reset with Tokens

```python
# app/utils/tokens.py
from itsdangerous import URLSafeTimedSerializer

def generate_reset_token(email, secret_key, expiration=3600):
    s = URLSafeTimedSerializer(secret_key)
    return s.dumps(email, salt='password-reset')

def verify_reset_token(token, secret_key, expiration=3600):
    s = URLSafeTimedSerializer(secret_key)
    try:
        email = s.loads(token, salt='password-reset', max_age=expiration)
    except Exception:
        return None
    return email

# In routes:
@auth_bp.route('/forgot-password', methods=['POST'])
def forgot_password():
    email = request.get_json().get('email')
    user = User.query.filter_by(email=email).first()
    if user:
        token = generate_reset_token(email, current_app.config['SECRET_KEY'])
        send_password_reset_email(user, token)
    # Always return 200 to avoid email enumeration
    return jsonify({'message': 'If the email exists, a reset link was sent.'})

@auth_bp.route('/reset-password/<token>', methods=['POST'])
def reset_password(token):
    email = verify_reset_token(token, current_app.config['SECRET_KEY'])
    if not email:
        return jsonify({'error': 'Invalid or expired token'}), 400
    user = User.query.filter_by(email=email).first_or_404()
    user.set_password(request.get_json()['password'])
    db.session.commit()
    return jsonify({'message': 'Password reset successful'})
```

### Search with SQLAlchemy

```python
@app.route('/search')
def search():
    query = request.args.get('q', '')
    page = request.args.get('page', 1, type=int)

    results = Post.query.filter(
        db.or_(
            Post.title.ilike(f'%{query}%'),
            Post.content.ilike(f'%{query}%'),
        )
    ).order_by(Post.created_at.desc()).paginate(page=page, per_page=10)

    return jsonify({
        'results': [p.to_dict() for p in results.items],
        'total': results.total,
    })
```

---

> 📌 **Tip**: Bookmark this file and refer back whenever you build something new in Flask. Every concept here is something you will encounter in real-world projects.

> 🔗 **Official Docs**: [https://flask.palletsprojects.com](https://flask.palletsprojects.com)
> 🔗 **Flask Extensions**: [https://flask.palletsprojects.com/en/latest/extensions](https://flask.palletsprojects.com/en/latest/extensions)
> 🔗 **SQLAlchemy Docs**: [https://docs.sqlalchemy.org](https://docs.sqlalchemy.org)