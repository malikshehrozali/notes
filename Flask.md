# Flask Complete Guide - Basic to Advanced

## Table of Contents

1. [Introduction to Flask](#introduction)
2. [Installation and Setup](#installation)
3. [Basic Concepts](#basic-concepts)
4. [Routing and URL Building](#routing)
5. [Templates with Jinja2](#templates)
6. [Forms and User Input](#forms)
7. [Database Integration](#database)
8. [Authentication and Authorization](#authentication)
9. [RESTful APIs](#restful-apis)
10. [Advanced Topics](#advanced-topics)
11. [Testing](#testing)
12. [Deployment](#deployment)

---

## Introduction to Flask {#introduction}

Flask is a lightweight WSGI web application framework written in Python. It's designed to make getting started quick and easy, with the ability to scale up to complex applications.

### Key Features

- Lightweight and modular design
- Built-in development server and debugger
- RESTful request dispatching
- Jinja2 templating engine
- Secure cookies support
- WSGI compliant
- Extensive documentation

### Flask Philosophy

Flask is based on two main dependencies: Werkzeug (WSGI toolkit) and Jinja2 (template engine). It follows the principle of being a microframework, meaning it provides the core functionality while allowing developers to choose their own tools and libraries.

---

## Installation and Setup {#installation}

### Installing Flask

```bash
# Create a virtual environment
python -m venv venv

# Activate virtual environment
# On Windows:
venv\Scripts\activate
# On macOS/Linux:
source venv/bin/activate

# Install Flask
pip install Flask
```

### Basic Application Structure

```
myproject/
├── app.py
├── templates/
│   └── index.html
├── static/
│   ├── css/
│   ├── js/
│   └── images/
├── requirements.txt
└── venv/
```

### Minimal Application

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run(debug=True)
```

---

## Basic Concepts {#basic-concepts}

### The Application Object

The Flask application object is an instance of the Flask class. It's the central object that manages your application.

```python
from flask import Flask

app = Flask(__name__)

# Configuration
app.config['SECRET_KEY'] = 'your-secret-key-here'
app.config['DEBUG'] = True
```

### Request and Response Objects

```python
from flask import Flask, request, make_response

@app.route('/user/<username>')
def show_user(username):
    # Access request data
    user_agent = request.headers.get('User-Agent')

    # Create custom response
    response = make_response(f'Hello {username}')
    response.headers['X-Custom-Header'] = 'Value'
    return response
```

### Application Context

```python
from flask import Flask, current_app, g

app = Flask(__name__)

# Use g for request-scoped data
@app.before_request
def before_request():
    g.user = 'John Doe'

@app.route('/')
def index():
    return f'User: {g.user}'
```

---

## Routing and URL Building {#routing}

### Basic Routing

```python
@app.route('/')
def index():
    return 'Index Page'

@app.route('/hello')
def hello():
    return 'Hello, World'
```

### Variable Rules

```python
@app.route('/user/<username>')
def show_user_profile(username):
    return f'User: {username}'

@app.route('/post/<int:post_id>')
def show_post(post_id):
    return f'Post ID: {post_id}'

# Converters: string (default), int, float, path, uuid
@app.route('/path/<path:subpath>')
def show_subpath(subpath):
    return f'Subpath: {subpath}'
```

### HTTP Methods

```python
from flask import request

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        return do_login()
    else:
        return show_login_form()

# Or use separate routes
@app.get('/user/<id>')
def get_user(id):
    return f'Get user {id}'

@app.post('/user')
def create_user():
    return 'Create user'
```

### URL Building

```python
from flask import url_for

@app.route('/')
def index():
    return 'index'

@app.route('/login')
def login():
    return 'login'

@app.route('/user/<username>')
def profile(username):
    return f'{username}\'s profile'

with app.test_request_context():
    print(url_for('index'))  # /
    print(url_for('login'))  # /login
    print(url_for('profile', username='John'))  # /user/John
    print(url_for('static', filename='style.css'))  # /static/style.css
```

### URL Redirects

```python
from flask import redirect, url_for

@app.route('/')
def index():
    return redirect(url_for('login'))

@app.route('/old-page')
def old_page():
    return redirect('/new-page', code=301)  # Permanent redirect
```

---

## Templates with Jinja2 {#templates}

### Basic Template Rendering

```python
from flask import render_template

@app.route('/hello/')
@app.route('/hello/<name>')
def hello(name=None):
    return render_template('hello.html', name=name)
```

**templates/hello.html:**

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Hello</title>
  </head>
  <body>
    {% if name %}
    <h1>Hello {{ name }}!</h1>
    {% else %}
    <h1>Hello, World!</h1>
    {% endif %}
  </body>
</html>
```

### Template Inheritance

**templates/base.html:**

```html
<!DOCTYPE html>
<html>
  <head>
    <title>{% block title %}{% endblock %} - My Application</title>
    {% block head %}
    <link
      rel="stylesheet"
      href="{{ url_for('static', filename='style.css') }}"
    />
    {% endblock %}
  </head>
  <body>
    <div id="content">{% block content %}{% endblock %}</div>
    <div id="footer">
      {% block footer %} &copy; Copyright 2024 {% endblock %}
    </div>
  </body>
</html>
```

**templates/index.html:**

```html
{% extends "base.html" %} {% block title %}Index{% endblock %} {% block head %}
{{ super() }}
<style>
  .important {
    color: red;
  }
</style>
{% endblock %} {% block content %}
<h1>Index</h1>
<p class="important">Welcome to my website!</p>
{% endblock %}
```

### Jinja2 Control Structures

```html
<!-- Loops -->
{% for item in items %}
<li>{{ item }}</li>
{% else %}
<li>No items found</li>
{% endfor %}

<!-- Conditionals -->
{% if user.is_authenticated %}
<p>Welcome, {{ user.name }}!</p>
{% elif user.is_guest %}
<p>Welcome, Guest!</p>
{% else %}
<p>Please log in</p>
{% endif %}

<!-- Filters -->
<p>{{ name|upper }}</p>
<p>{{ text|truncate(50) }}</p>
<p>{{ price|round(2) }}</p>
<p>{{ html_content|safe }}</p>

<!-- Macros -->
{% macro render_input(name, type='text') %}
<input type="{{ type }}" name="{{ name }}" id="{{ name }}" />
{% endmacro %} {{ render_input('username') }} {{ render_input('password',
type='password') }}
```

### Custom Filters

```python
@app.template_filter('reverse')
def reverse_filter(s):
    return s[::-1]

# Or using decorator
def datetime_filter(dt, format='%Y-%m-%d'):
    return dt.strftime(format)

app.jinja_env.filters['datetime'] = datetime_filter
```

---

## Forms and User Input {#forms}

### Handling Form Data

```python
from flask import request

@app.route('/submit', methods=['GET', 'POST'])
def submit():
    if request.method == 'POST':
        username = request.form['username']
        email = request.form['email']
        return f'User: {username}, Email: {email}'
    return '''
        <form method="post">
            <input type="text" name="username">
            <input type="email" name="email">
            <input type="submit">
        </form>
    '''
```

### Flask-WTF Integration

```bash
pip install Flask-WTF
```

```python
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField, SubmitField
from wtforms.validators import DataRequired, Email, Length

class LoginForm(FlaskForm):
    username = StringField('Username',
                          validators=[DataRequired(), Length(min=4, max=25)])
    password = PasswordField('Password',
                            validators=[DataRequired()])
    submit = SubmitField('Login')

@app.route('/login', methods=['GET', 'POST'])
def login():
    form = LoginForm()
    if form.validate_on_submit():
        username = form.username.data
        password = form.password.data
        # Process login
        return redirect(url_for('index'))
    return render_template('login.html', form=form)
```

**templates/login.html:**

```html
<form method="POST">
  {{ form.hidden_tag() }}
  <div>
    {{ form.username.label }} {{ form.username() }} {% if form.username.errors
    %}
    <ul>
      {% for error in form.username.errors %}
      <li>{{ error }}</li>
      {% endfor %}
    </ul>
    {% endif %}
  </div>
  <div>{{ form.password.label }} {{ form.password() }}</div>
  {{ form.submit() }}
</form>
```

### File Uploads

```python
from werkzeug.utils import secure_filename
import os

app.config['UPLOAD_FOLDER'] = '/path/to/uploads'
app.config['MAX_CONTENT_LENGTH'] = 16 * 1024 * 1024  # 16MB max

ALLOWED_EXTENSIONS = {'txt', 'pdf', 'png', 'jpg', 'jpeg', 'gif'}

def allowed_file(filename):
    return '.' in filename and \
           filename.rsplit('.', 1)[1].lower() in ALLOWED_EXTENSIONS

@app.route('/upload', methods=['GET', 'POST'])
def upload_file():
    if request.method == 'POST':
        if 'file' not in request.files:
            return 'No file part'
        file = request.files['file']
        if file.filename == '':
            return 'No selected file'
        if file and allowed_file(file.filename):
            filename = secure_filename(file.filename)
            file.save(os.path.join(app.config['UPLOAD_FOLDER'], filename))
            return 'File uploaded successfully'
    return '''
        <form method="post" enctype="multipart/form-data">
            <input type="file" name="file">
            <input type="submit">
        </form>
    '''
```

---

## Database Integration {#database}

### Flask-SQLAlchemy

```bash
pip install Flask-SQLAlchemy
```

```python
from flask_sqlalchemy import SQLAlchemy

app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///site.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app)

# Models
class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False)
    posts = db.relationship('Post', backref='author', lazy=True)

    def __repr__(self):
        return f'<User {self.username}>'

class Post(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(100), nullable=False)
    content = db.Column(db.Text, nullable=False)
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'), nullable=False)

# Create tables
with app.app_context():
    db.create_all()
```

### CRUD Operations

```python
# Create
@app.route('/user/create', methods=['POST'])
def create_user():
    user = User(username='john', email='john@example.com')
    db.session.add(user)
    db.session.commit()
    return 'User created'

# Read
@app.route('/user/<int:user_id>')
def get_user(user_id):
    user = User.query.get_or_404(user_id)
    return f'User: {user.username}'

# Query examples
users = User.query.all()
user = User.query.filter_by(username='john').first()
users = User.query.filter(User.email.endswith('@example.com')).all()

# Update
@app.route('/user/<int:user_id>/update', methods=['POST'])
def update_user(user_id):
    user = User.query.get_or_404(user_id)
    user.email = 'newemail@example.com'
    db.session.commit()
    return 'User updated'

# Delete
@app.route('/user/<int:user_id>/delete', methods=['POST'])
def delete_user(user_id):
    user = User.query.get_or_404(user_id)
    db.session.delete(user)
    db.session.commit()
    return 'User deleted'
```

### Database Migrations with Flask-Migrate

```bash
pip install Flask-Migrate
```

```python
from flask_migrate import Migrate

migrate = Migrate(app, db)

# Commands to run:
# flask db init
# flask db migrate -m "Initial migration"
# flask db upgrade
```

---

## Authentication and Authorization {#authentication}

### Flask-Login

```bash
pip install Flask-Login
```

```python
from flask_login import LoginManager, UserMixin, login_user, logout_user, login_required, current_user

login_manager = LoginManager()
login_manager.init_app(app)
login_manager.login_view = 'login'

class User(UserMixin, db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True, nullable=False)
    password_hash = db.Column(db.String(128))

    def set_password(self, password):
        from werkzeug.security import generate_password_hash
        self.password_hash = generate_password_hash(password)

    def check_password(self, password):
        from werkzeug.security import check_password_hash
        return check_password_hash(self.password_hash, password)

@login_manager.user_loader
def load_user(user_id):
    return User.query.get(int(user_id))

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']
        user = User.query.filter_by(username=username).first()
        if user and user.check_password(password):
            login_user(user)
            return redirect(url_for('dashboard'))
    return render_template('login.html')

@app.route('/logout')
@login_required
def logout():
    logout_user()
    return redirect(url_for('index'))

@app.route('/dashboard')
@login_required
def dashboard():
    return f'Hello {current_user.username}!'
```

### Session Management

```python
from flask import session

app.config['SECRET_KEY'] = 'your-secret-key'

@app.route('/set-session')
def set_session():
    session['username'] = 'john'
    session['email'] = 'john@example.com'
    return 'Session set'

@app.route('/get-session')
def get_session():
    username = session.get('username', 'Not set')
    return f'Username: {username}'

@app.route('/clear-session')
def clear_session():
    session.pop('username', None)
    # Or clear all
    session.clear()
    return 'Session cleared'
```

---

## RESTful APIs {#restful-apis}

### Basic REST API

```python
from flask import jsonify, request

# Sample data
books = [
    {'id': 1, 'title': 'Book 1', 'author': 'Author 1'},
    {'id': 2, 'title': 'Book 2', 'author': 'Author 2'}
]

# GET all books
@app.route('/api/books', methods=['GET'])
def get_books():
    return jsonify({'books': books})

# GET single book
@app.route('/api/books/<int:book_id>', methods=['GET'])
def get_book(book_id):
    book = next((b for b in books if b['id'] == book_id), None)
    if book is None:
        return jsonify({'error': 'Book not found'}), 404
    return jsonify({'book': book})

# POST new book
@app.route('/api/books', methods=['POST'])
def create_book():
    if not request.json or 'title' not in request.json:
        return jsonify({'error': 'Bad request'}), 400

    book = {
        'id': books[-1]['id'] + 1 if books else 1,
        'title': request.json['title'],
        'author': request.json.get('author', '')
    }
    books.append(book)
    return jsonify({'book': book}), 201

# PUT update book
@app.route('/api/books/<int:book_id>', methods=['PUT'])
def update_book(book_id):
    book = next((b for b in books if b['id'] == book_id), None)
    if book is None:
        return jsonify({'error': 'Book not found'}), 404

    book['title'] = request.json.get('title', book['title'])
    book['author'] = request.json.get('author', book['author'])
    return jsonify({'book': book})

# DELETE book
@app.route('/api/books/<int:book_id>', methods=['DELETE'])
def delete_book(book_id):
    book = next((b for b in books if b['id'] == book_id), None)
    if book is None:
        return jsonify({'error': 'Book not found'}), 404

    books.remove(book)
    return jsonify({'result': True})
```

### Flask-RESTful

```bash
pip install Flask-RESTful
```

```python
from flask_restful import Api, Resource, reqparse, fields, marshal_with

api = Api(app)

resource_fields = {
    'id': fields.Integer,
    'title': fields.String,
    'author': fields.String
}

parser = reqparse.RequestParser()
parser.add_argument('title', type=str, required=True, help='Title is required')
parser.add_argument('author', type=str)

class Book(Resource):
    @marshal_with(resource_fields)
    def get(self, book_id):
        book = next((b for b in books if b['id'] == book_id), None)
        if not book:
            return {'error': 'Book not found'}, 404
        return book

    @marshal_with(resource_fields)
    def put(self, book_id):
        args = parser.parse_args()
        book = next((b for b in books if b['id'] == book_id), None)
        if not book:
            return {'error': 'Book not found'}, 404
        book.update(args)
        return book

    def delete(self, book_id):
        global books
        books = [b for b in books if b['id'] != book_id]
        return '', 204

class BookList(Resource):
    @marshal_with(resource_fields)
    def get(self):
        return books

    @marshal_with(resource_fields)
    def post(self):
        args = parser.parse_args()
        book = {
            'id': books[-1]['id'] + 1 if books else 1,
            'title': args['title'],
            'author': args.get('author', '')
        }
        books.append(book)
        return book, 201

api.add_resource(BookList, '/api/books')
api.add_resource(Book, '/api/books/<int:book_id>')
```

### CORS Support

```bash
pip install Flask-CORS
```

```python
from flask_cors import CORS

# Enable CORS for all routes
CORS(app)

# Or for specific routes
@app.route('/api/data')
@cross_origin()
def get_data():
    return jsonify({'data': 'value'})
```

---

## Advanced Topics {#advanced-topics}

### Blueprints

```python
# auth.py
from flask import Blueprint

auth = Blueprint('auth', __name__, url_prefix='/auth')

@auth.route('/login')
def login():
    return 'Login page'

@auth.route('/logout')
def logout():
    return 'Logout'

# blog.py
blog = Blueprint('blog', __name__, url_prefix='/blog')

@blog.route('/')
def index():
    return 'Blog index'

@blog.route('/<int:post_id>')
def show_post(post_id):
    return f'Post {post_id}'

# app.py
from auth import auth
from blog import blog

app.register_blueprint(auth)
app.register_blueprint(blog)
```

### Application Factory Pattern

```python
# app/__init__.py
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()

def create_app(config_name='development'):
    app = Flask(__name__)

    # Load configuration
    if config_name == 'development':
        app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///dev.db'
    elif config_name == 'production':
        app.config['SQLALCHEMY_DATABASE_URI'] = 'postgresql://...'

    db.init_app(app)

    # Register blueprints
    from .auth import auth
    from .blog import blog
    app.register_blueprint(auth)
    app.register_blueprint(blog)

    return app

# run.py
from app import create_app

app = create_app('development')

if __name__ == '__main__':
    app.run()
```

### Custom Error Handlers

```python
@app.errorhandler(404)
def not_found(error):
    return render_template('404.html'), 404

@app.errorhandler(500)
def internal_error(error):
    db.session.rollback()
    return render_template('500.html'), 500

# JSON error responses
@app.errorhandler(404)
def not_found_api(error):
    if request.path.startswith('/api/'):
        return jsonify({'error': 'Not found'}), 404
    return render_template('404.html'), 404
```

### Middleware and Before/After Request

```python
@app.before_request
def before_request():
    g.user = current_user if current_user.is_authenticated else None

@app.after_request
def after_request(response):
    response.headers['X-Custom-Header'] = 'Value'
    return response

@app.teardown_request
def teardown_request(exception):
    if exception:
        db.session.rollback()
    db.session.remove()

@app.before_first_request
def before_first_request():
    # Initialize something on first request
    pass
```

### Background Tasks with Celery

```bash
pip install celery redis
```

```python
from celery import Celery

app.config['CELERY_BROKER_URL'] = 'redis://localhost:6379/0'
app.config['CELERY_RESULT_BACKEND'] = 'redis://localhost:6379/0'

celery = Celery(app.name, broker=app.config['CELERY_BROKER_URL'])
celery.conf.update(app.config)

@celery.task
def send_email(email, subject, body):
    # Send email logic
    time.sleep(10)  # Simulate long task
    return f'Email sent to {email}'

@app.route('/send-email')
def trigger_email():
    send_email.delay('user@example.com', 'Hello', 'Test email')
    return 'Email task queued'
```

### Caching

```bash
pip install Flask-Caching
```

```python
from flask_caching import Cache

cache = Cache(app, config={
    'CACHE_TYPE': 'SimpleCache',
    'CACHE_DEFAULT_TIMEOUT': 300
})

@app.route('/expensive-operation')
@cache.cached(timeout=60)
def expensive_operation():
    # Expensive computation
    result = compute_something()
    return jsonify(result)

@app.route('/user/<int:user_id>')
@cache.cached(timeout=120, key_prefix='user_view')
def user_view(user_id):
    user = User.query.get_or_404(user_id)
    return render_template('user.html', user=user)

# Manual caching
def get_user(user_id):
    cache_key = f'user_{user_id}'
    user = cache.get(cache_key)
    if user is None:
        user = User.query.get(user_id)
        cache.set(cache_key, user, timeout=300)
    return user
```

### WebSockets with Flask-SocketIO

```bash
pip install Flask-SocketIO python-socketio
```

```python
from flask_socketio import SocketIO, emit, join_room, leave_room

socketio = SocketIO(app)

@socketio.on('connect')
def handle_connect():
    print('Client connected')
    emit('response', {'data': 'Connected'})

@socketio.on('disconnect')
def handle_disconnect():
    print('Client disconnected')

@socketio.on('message')
def handle_message(data):
    print(f'Received message: {data}')
    emit('response', {'data': data}, broadcast=True)

@socketio.on('join')
def on_join(data):
    room = data['room']
    join_room(room)
    emit('status', {'msg': f'User joined room {room}'}, room=room)

if __name__ == '__main__':
    socketio.run(app)
```

### Rate Limiting

```bash
pip install Flask-Limiter
```

```python
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

limiter = Limiter(
    app=app,
    key_func=get_remote_address,
    default_limits=["200 per day", "50 per hour"]
)

@app.route('/api/resource')
@limiter.limit("10 per minute")
def limited_resource():
    return jsonify({'data': 'value'})

@app.route('/api/special')
@limiter.exempt
def unlimited_resource():
    return jsonify({'data': 'unlimited'})
```

---

## Testing {#testing}

### Unit Testing

```python
# test_app.py
import unittest
from app import app, db
from app.models import User

class TestApp(unittest.TestCase):
    def setUp(self):
        app.config['TESTING'] = True
        app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///:memory:'
        self.app = app.test_client()
        with app.app_context():
            db.create_all()

    def tearDown(self):
        with app.app_context():
            db.session.remove()
            db.drop_all()

    def test_home_page(self):
        response = self.app.get('/')
        self.assertEqual(response.status_code, 200)

    def test_create_user(self):
        response = self.app.post('/user/create', data={
            'username': 'testuser',
            'email': 'test@example.com'
        })
        self.assertEqual(response.status_code, 201)

    def test_login(self):
        # Create user first
        with app.app_context():
            user = User(username='test', email='test@example.com')
            user.set_password('password')
            db.session.add(user)
            db.session.commit()

        response = self.app.post('/login', data={
            'username': 'test',
            'password': 'password'
        })
        self.assertEqual(response.status_code, 302)  # Redirect

if __name__ == '__main__':
    unittest.main()
```

### Testing with pytest

```bash
pip install pytest pytest-flask
```

```python
# conftest.py
import pytest
from app import create_app, db

@pytest.fixture
def app():
    app = create_app('testing')
    with app.app_context():
        db.create_all()
        yield app
        db.drop_all()

@pytest.fixture
def client(app):
    return app.test_client()

@pytest.fixture
def runner(app):
    return app.test_cli_runner()

# test_routes.py
def test_home_page(client):
    response = client.get('/')
    assert response.status_code == 200
    assert b'Welcome' in response.data

def test_api_get_books(client):
    response = client.get('/api/books')
    assert response.status_code == 200
    assert response.is_json

def test_create_book(client):
    response = client.post('/api/books', json={
        'title': 'Test Book',
        'author': 'Test Author'
    })
    assert response.status_code == 201
    data = response.get_json()
    assert data['book']['title'] == 'Test Book'
```

---

## Deployment {#deployment}

### Production Configuration

```python
# config.py
import os

class Config:
    SECRET_KEY = os.environ.get('SECRET_KEY') or 'dev-secret-key'
    SQLALCHEMY_TRACK_MODIFICATIONS = False

class DevelopmentConfig(Config):
    DEBUG = True
    SQLALCHEMY_DATABASE_URI = 'sqlite:///dev.db'

class ProductionConfig(Config):
    DEBUG = False
    SQLALCHEMY_DATABASE_URI = os.environ.get('DATABASE_URL')

config = {
    'development': DevelopmentConfig,
    'production': ProductionConfig,
    'default': DevelopmentConfig
}
```

### WSGI with Gunicorn

```bash
pip install gunicorn
```

```bash
# Run with gunicorn
gunicorn -w 4 -b 0.0.0.0:8000 app:app

# Or with config file
# gunicorn_config.py
bind = "0.0.0.0:8000"
workers = 4
worker_class = "sync"
worker_connections = 1000
timeout = 30
keepalive = 2
errorlog = "-"
accesslog = "-"
loglevel = "info"

# Run: gunicorn -c gunicorn_config.py app:app
```

### Using Nginx as Reverse Proxy

```nginx
# /etc/nginx/sites-available/myapp
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /static {
        alias /path/to/your/app/static;
        expires 30d;
    }
}
```

### Environment Variables

```python
# .env file
SECRET_KEY=your-secret-key-here
DATABASE_URL=postgresql://user:password@localhost/dbname
MAIL_SERVER=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=your-email@gmail.com
MAIL_PASSWORD=your-password

# Load in Flask
import os
from dotenv import load_dotenv

load_dotenv()

app.config['SECRET_KEY'] = os.getenv('SECRET_KEY')
app.config['SQLALCHEMY_DATABASE_URI'] = os.getenv('DATABASE_URL')
```

### Docker Deployment

```dockerfile
# Dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["gunicorn", "-w", "4", "-b", "0.0.0.0:8000", "app:app"]
```

```yaml
# docker-compose.yml
version: "3.8"

services:
  web:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:password@db:5432/myapp
      - SECRET_KEY=your-secret-key
    depends_on:
      - db
    volumes:
      - ./:/app

  db:
    image: postgres:15
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=myapp
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

### Logging

```python
import logging
from logging.handlers import RotatingFileHandler
import os

if not app.debug:
    if not os.path.exists('logs'):
        os.mkdir('logs')

    file_handler = RotatingFileHandler('logs/myapp.log',
                                      maxBytes=10240,
                                      backupCount=10)
    file_handler.setFormatter(logging.Formatter(
        '%(asctime)s %(levelname)s: %(message)s [in %(pathname)s:%(lineno)d]'
    ))
    file_handler.setLevel(logging.INFO)
    app.logger.addHandler(file_handler)

    app.logger.setLevel(logging.INFO)
    app.logger.info('MyApp startup')

# Usage
@app.route('/some-route')
def some_route():
    app.logger.info('Processing request')
    try:
        # Do something
        app.logger.debug('Debug information')
    except Exception as e:
        app.logger.error(f'Error occurred: {str(e)}')
        raise
```

### Security Best Practices

```python
# 1. HTTPS Only
from flask_talisman import Talisman

Talisman(app, force_https=True)

# 2. CSRF Protection (with Flask-WTF)
from flask_wtf.csrf import CSRFProtect

csrf = CSRFProtect(app)

# 3. Secure Headers
@app.after_request
def set_secure_headers(response):
    response.headers['X-Content-Type-Options'] = 'nosniff'
    response.headers['X-Frame-Options'] = 'SAMEORIGIN'
    response.headers['X-XSS-Protection'] = '1; mode=block'
    response.headers['Strict-Transport-Security'] = 'max-age=31536000; includeSubDomains'
    return response

# 4. SQL Injection Prevention (use parameterized queries)
# Good:
user = User.query.filter_by(username=username).first()
# Bad:
# db.execute(f"SELECT * FROM users WHERE username = '{username}'")

# 5. Password Hashing
from werkzeug.security import generate_password_hash, check_password_hash

hashed = generate_password_hash('password', method='pbkdf2:sha256')
is_valid = check_password_hash(hashed, 'password')

# 6. Rate Limiting (already covered)

# 7. Input Validation
from wtforms.validators import DataRequired, Email, Length, Regexp

class RegistrationForm(FlaskForm):
    username = StringField('Username', validators=[
        DataRequired(),
        Length(min=3, max=20),
        Regexp('^[A-Za-z0-9_]+, message='Username must contain only letters, numbers, and underscores')
    ])
    email = StringField('Email', validators=[DataRequired(), Email()])
```

---

## Additional Flask Extensions

### Flask-Mail

```bash
pip install Flask-Mail
```

```python
from flask_mail import Mail, Message

app.config['MAIL_SERVER'] = 'smtp.gmail.com'
app.config['MAIL_PORT'] = 587
app.config['MAIL_USE_TLS'] = True
app.config['MAIL_USERNAME'] = os.getenv('MAIL_USERNAME')
app.config['MAIL_PASSWORD'] = os.getenv('MAIL_PASSWORD')

mail = Mail(app)

@app.route('/send-email')
def send_email():
    msg = Message('Hello',
                  sender='from@example.com',
                  recipients=['to@example.com'])
    msg.body = 'This is the email body'
    msg.html = '<b>HTML</b> body'
    mail.send(msg)
    return 'Email sent!'

# With attachments
def send_with_attachment():
    msg = Message('Subject', recipients=['user@example.com'])
    with app.open_resource('document.pdf') as fp:
        msg.attach('document.pdf', 'application/pdf', fp.read())
    mail.send(msg)
```

### Flask-Babel (Internationalization)

```bash
pip install Flask-Babel
```

```python
from flask_babel import Babel, gettext

babel = Babel(app)

app.config['BABEL_DEFAULT_LOCALE'] = 'en'
app.config['BABEL_SUPPORTED_LOCALES'] = ['en', 'es', 'fr']

@babel.localeselector
def get_locale():
    return request.accept_languages.best_match(['en', 'es', 'fr'])

# In your code
from flask_babel import gettext as _

@app.route('/')
def index():
    return _('Hello, World!')

# In templates
{{ _('Welcome') }}
```

### Flask-Admin

```bash
pip install Flask-Admin
```

```python
from flask_admin import Admin
from flask_admin.contrib.sqla import ModelView

admin = Admin(app, name='MyApp Admin', template_mode='bootstrap4')

# Add model views
admin.add_view(ModelView(User, db.session))
admin.add_view(ModelView(Post, db.session))

# Custom admin view
class UserAdmin(ModelView):
    column_list = ('id', 'username', 'email')
    column_searchable_list = ('username', 'email')
    column_filters = ('username',)
    form_excluded_columns = ('password_hash',)

    def is_accessible(self):
        return current_user.is_authenticated and current_user.is_admin

admin.add_view(UserAdmin(User, db.session))
```

### Flask-Uploads

```bash
pip install Flask-Uploads
```

```python
from flask_uploads import UploadSet, configure_uploads, IMAGES

photos = UploadSet('photos', IMAGES)

app.config['UPLOADED_PHOTOS_DEST'] = 'static/uploads'
configure_uploads(app, photos)

@app.route('/upload', methods=['POST'])
def upload():
    if 'photo' in request.files:
        filename = photos.save(request.files['photo'])
        return f'Photo saved: {filename}'
    return 'No photo uploaded'
```

### Flask-Moment (Date/Time Display)

```bash
pip install Flask-Moment
```

```python
from flask_moment import Moment
from datetime import datetime

moment = Moment(app)

@app.route('/')
def index():
    return render_template('index.html',
                         current_time=datetime.utcnow())

# In template
{{ moment(current_time).format('LLLL') }}
{{ moment(current_time).fromNow() }}
```

---

## Performance Optimization

### Database Query Optimization

```python
# 1. Use lazy loading appropriately
class User(db.Model):
    posts = db.relationship('Post', backref='author', lazy='dynamic')

# 2. Eager loading to avoid N+1 queries
from sqlalchemy.orm import joinedload

users = User.query.options(joinedload(User.posts)).all()

# 3. Use pagination
@app.route('/posts')
def posts():
    page = request.args.get('page', 1, type=int)
    posts = Post.query.paginate(page=page, per_page=20)
    return render_template('posts.html', posts=posts)

# In template
{% for post in posts.items %}
    {{ post.title }}
{% endfor %}

{% if posts.has_prev %}
    <a href="{{ url_for('posts', page=posts.prev_num) }}">Previous</a>
{% endif %}
{% if posts.has_next %}
    <a href="{{ url_for('posts', page=posts.next_num) }}">Next</a>
{% endif %}

# 4. Index database columns
class User(db.Model):
    email = db.Column(db.String(120), unique=True, index=True)
    username = db.Column(db.String(80), unique=True, index=True)
```

### Compression

```python
from flask_compress import Compress

Compress(app)

# Or with configuration
app.config['COMPRESS_MIMETYPES'] = [
    'text/html',
    'text/css',
    'text/xml',
    'application/json',
    'application/javascript'
]
app.config['COMPRESS_LEVEL'] = 6
app.config['COMPRESS_MIN_SIZE'] = 500
```

### Static File Optimization

```python
# Use CDN for static files
app.config['CDN_DOMAIN'] = 'cdn.example.com'

@app.context_processor
def override_url_for():
    return dict(url_for=cdn_url_for)

def cdn_url_for(endpoint, **values):
    if endpoint == 'static':
        filename = values.get('filename')
        if filename:
            return f"https://{app.config['CDN_DOMAIN']}/static/{filename}"
    return url_for(endpoint, **values)
```

### Async Support (Flask 2.0+)

```python
import asyncio
from flask import Flask

app = Flask(__name__)

@app.route('/async')
async def async_route():
    await asyncio.sleep(1)
    return 'Async response'

# Async before request
@app.before_request
async def before_request():
    # Async operations
    pass
```

---

## Common Patterns and Recipes

### Pagination Helper

```python
def paginate_query(query, page, per_page=20):
    """Generic pagination helper"""
    pagination = query.paginate(
        page=page,
        per_page=per_page,
        error_out=False
    )
    return {
        'items': pagination.items,
        'total': pagination.total,
        'page': page,
        'pages': pagination.pages,
        'has_prev': pagination.has_prev,
        'has_next': pagination.has_next,
        'prev_num': pagination.prev_num,
        'next_num': pagination.next_num
    }

@app.route('/api/users')
def get_users():
    page = request.args.get('page', 1, type=int)
    users_query = User.query.order_by(User.created_at.desc())
    result = paginate_query(users_query, page)
    return jsonify(result)
```

### Request Validation Decorator

```python
from functools import wraps
from flask import jsonify, request

def validate_json(*expected_args):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            if not request.is_json:
                return jsonify({'error': 'Content-Type must be application/json'}), 400

            json_data = request.get_json()
            for arg in expected_args:
                if arg not in json_data:
                    return jsonify({'error': f'Missing required field: {arg}'}), 400

            return func(*args, **kwargs)
        return wrapper
    return decorator

@app.route('/api/create', methods=['POST'])
@validate_json('name', 'email')
def create_item():
    data = request.get_json()
    # Process data
    return jsonify({'success': True})
```

### Custom JSON Encoder

```python
from flask.json import JSONEncoder
from datetime import datetime

class CustomJSONEncoder(JSONEncoder):
    def default(self, obj):
        if isinstance(obj, datetime):
            return obj.isoformat()
        if isinstance(obj, User):
            return {
                'id': obj.id,
                'username': obj.username,
                'email': obj.email
            }
        return super().default(obj)

app.json_encoder = CustomJSONEncoder
```

### API Versioning

```python
# Version 1
v1 = Blueprint('api_v1', __name__, url_prefix='/api/v1')

@v1.route('/users')
def get_users_v1():
    return jsonify({'version': 1, 'users': []})

# Version 2
v2 = Blueprint('api_v2', __name__, url_prefix='/api/v2')

@v2.route('/users')
def get_users_v2():
    return jsonify({'version': 2, 'users': []})

app.register_blueprint(v1)
app.register_blueprint(v2)
```

### Database Seeding

```python
# manage.py or seed.py
from app import app, db
from app.models import User, Post

def seed_database():
    with app.app_context():
        # Clear existing data
        db.drop_all()
        db.create_all()

        # Create users
        user1 = User(username='john', email='john@example.com')
        user1.set_password('password')

        user2 = User(username='jane', email='jane@example.com')
        user2.set_password('password')

        db.session.add_all([user1, user2])
        db.session.commit()

        # Create posts
        post1 = Post(title='First Post', content='Content', author=user1)
        post2 = Post(title='Second Post', content='More content', author=user2)

        db.session.add_all([post1, post2])
        db.session.commit()

        print('Database seeded successfully!')

if __name__ == '__main__':
    seed_database()
```

### Custom CLI Commands

```python
import click

@app.cli.command()
def init_db():
    """Initialize the database."""
    db.create_all()
    click.echo('Initialized the database.')

@app.cli.command()
@click.argument('username')
def create_admin(username):
    """Create an admin user."""
    user = User(username=username, email=f'{username}@example.com', is_admin=True)
    user.set_password('admin123')
    db.session.add(user)
    db.session.commit()
    click.echo(f'Created admin user: {username}')

# Run with: flask init-db
# Run with: flask create-admin john
```

---

## Debugging Tips

### Debug Toolbar

```bash
pip install Flask-DebugToolbar
```

```python
from flask_debugtoolbar import DebugToolbarExtension

app.config['DEBUG_TB_ENABLED'] = True
app.config['DEBUG_TB_INTERCEPT_REDIRECTS'] = False
toolbar = DebugToolbarExtension(app)
```

### Profiling

```python
from werkzeug.middleware.profiler import ProfilerMiddleware

app.wsgi_app = ProfilerMiddleware(app.wsgi_app, restrictions=[30])
```

### SQL Query Logging

```python
import logging

logging.basicConfig()
logging.getLogger('sqlalchemy.engine').setLevel(logging.INFO)

# Or in development config
app.config['SQLALCHEMY_ECHO'] = True
```

### Custom Exception Handling

```python
class APIException(Exception):
    status_code = 400

    def __init__(self, message, status_code=None, payload=None):
        super().__init__()
        self.message = message
        if status_code is not None:
            self.status_code = status_code
        self.payload = payload

    def to_dict(self):
        rv = dict(self.payload or ())
        rv['message'] = self.message
        return rv

@app.errorhandler(APIException)
def handle_api_exception(error):
    response = jsonify(error.to_dict())
    response.status_code = error.status_code
    return response

# Usage
@app.route('/api/resource/<int:id>')
def get_resource(id):
    resource = Resource.query.get(id)
    if not resource:
        raise APIException('Resource not found', status_code=404)
    return jsonify(resource.to_dict())
```

---

## Best Practices Summary

### Project Structure

```
myproject/
├── app/
│   ├── __init__.py          # Application factory
│   ├── models.py            # Database models
│   ├── routes/
│   │   ├── __init__.py
│   │   ├── auth.py          # Authentication routes
│   │   ├── main.py          # Main routes
│   │   └── api.py           # API routes
│   ├── templates/
│   │   ├── base.html
│   │   ├── auth/
│   │   └── main/
│   ├── static/
│   │   ├── css/
│   │   ├── js/
│   │   └── images/
│   ├── forms.py             # WTForms
│   ├── utils.py             # Helper functions
│   └── extensions.py        # Flask extensions
├── migrations/              # Database migrations
├── tests/
│   ├── __init__.py
│   ├── test_auth.py
│   └── test_api.py
├── config.py                # Configuration
├── requirements.txt
├── .env                     # Environment variables
├── .gitignore
└── run.py                   # Application entry point
```

### Configuration Management

```python
# config.py
import os
from datetime import timedelta

class Config:
    SECRET_KEY = os.getenv('SECRET_KEY', 'dev-secret-key')
    SQLALCHEMY_TRACK_MODIFICATIONS = False

    # Session
    PERMANENT_SESSION_LIFETIME = timedelta(days=7)
    SESSION_COOKIE_SECURE = True
    SESSION_COOKIE_HTTPONLY = True
    SESSION_COOKIE_SAMESITE = 'Lax'

    # Upload
    MAX_CONTENT_LENGTH = 16 * 1024 * 1024  # 16MB
    UPLOAD_FOLDER = os.path.join(os.getcwd(), 'uploads')

    # Pagination
    POSTS_PER_PAGE = 20

    # Email
    MAIL_SERVER = os.getenv('MAIL_SERVER')
    MAIL_PORT = int(os.getenv('MAIL_PORT', 587))
    MAIL_USE_TLS = os.getenv('MAIL_USE_TLS', 'true').lower() in ['true', 'on', '1']

class DevelopmentConfig(Config):
    DEBUG = True
    SQLALCHEMY_DATABASE_URI = 'sqlite:///dev.db'

class ProductionConfig(Config):
    DEBUG = False
    SQLALCHEMY_DATABASE_URI = os.getenv('DATABASE_URL')

class TestingConfig(Config):
    TESTING = True
    SQLALCHEMY_DATABASE_URI = 'sqlite:///:memory:'
    WTF_CSRF_ENABLED = False

config = {
    'development': DevelopmentConfig,
    'production': ProductionConfig,
    'testing': TestingConfig,
    'default': DevelopmentConfig
}
```

### Code Quality Tips

1. **Use type hints**

```python
from typing import Optional, List, Dict

def get_user_by_id(user_id: int) -> Optional[User]:
    return User.query.get(user_id)

def get_all_users() -> List[User]:
    return User.query.all()
```

2. **Document your code**

```python
def process_payment(amount: float, currency: str) -> Dict[str, any]:
    """
    Process a payment transaction.

    Args:
        amount: The payment amount
        currency: Currency code (e.g., 'USD', 'EUR')

    Returns:
        Dictionary containing transaction details

    Raises:
        ValueError: If amount is negative
        PaymentError: If payment processing fails
    """
    if amount < 0:
        raise ValueError("Amount cannot be negative")
    # Process payment logic
    return {'status': 'success', 'transaction_id': '123'}
```

3. **Use environment-specific settings**
4. **Keep routes thin, move logic to services**
5. **Use database migrations**
6. **Write tests**
7. **Use blueprints for large applications**
8. **Handle errors gracefully**
9. **Log appropriately**
10. **Keep dependencies updated**

---

## Useful Resources

### Official Documentation

- Flask Documentation: https://flask.palletsprojects.com/
- Jinja2 Documentation: https://jinja.palletsprojects.com/
- SQLAlchemy Documentation: https://docs.sqlalchemy.org/
- Werkzeug Documentation: https://werkzeug.palletsprojects.com/

### Flask Extensions

- Flask-SQLAlchemy: Database ORM
- Flask-Login: User session management
- Flask-WTF: Form handling and validation
- Flask-Mail: Email support
- Flask-Migrate: Database migrations
- Flask-RESTful: REST API development
- Flask-CORS: Cross-Origin Resource Sharing
- Flask-Caching: Caching support
- Flask-Admin: Admin interface
- Flask-SocketIO: WebSocket support

### Learning Resources

- Flask Mega-Tutorial by Miguel Grinberg
- Real Python Flask Tutorials
- Full Stack Python - Flask Section
- Flask Discord Community
- Stack Overflow Flask Tag

### Tools

- Postman: API testing
- SQLite Browser: Database inspection
- Redis: Caching and message broker
- Celery: Background tasks
- Docker: Containerization
- Nginx: Web server and reverse proxy

---

## Conclusion

Flask is a powerful and flexible framework that can handle everything from simple web applications to complex, large-scale systems. The key to mastering Flask is:

1. Understanding the core concepts (routing, templates, request/response)
2. Choosing the right extensions for your needs
3. Following best practices for code organization
4. Writing tests for your application
5. Optimizing for performance and security
6. Continuously learning and staying updated

Remember: Flask gives you the freedom to structure your application as you see fit. Use this flexibility wisely to create maintainable, scalable applications.

Happy Flask development! 🚀
