# Templates

## 1. Template Configuration

```python
# settings.py file

TEMPLATES = [
    {
        "BACKEND": "django.template.backends.django.DjangoTemplates",
        "DIRS": [BASE_DIR / "templates"],  # Project-level templates
        "APP_DIRS": True,                  # App-level templates
        "OPTIONS": {
            "context_processors": [
                ...
            ],
        },
    },
]

```

## 2. Scalable Multi-App Project Template Structure

```bash
project_root/
│
├── manage.py
├── config/                     # settings, urls, asgi, wsgi
│
├── templates/                  # GLOBAL templates (project-level)
│   ├── base/
│   │   ├── base.html
│   │   ├── layout.html
│   │   └── admin_base.html
│   │
│   ├── includes/
│   │   ├── navbar.html
│   │   ├── footer.html
│   │   └── messages.html
│   │
│   ├── errors/
│   │   ├── 400.html
│   │   ├── 403.html
│   │   ├── 404.html
│   │   └── 500.html
│   │
│   └── registration/           # Django auth default templates
│       ├── login.html
│       ├── logout.html
│       ├── password_reset_form.html
│       └── ...
│
├── apps/
│   ├── users/
│   │   ├── models.py
│   │   ├── views.py
│   │   ├── urls.py
│   │   └── templates/
│   │       └── users/          # IMPORTANT: namespaced by app name
│   │           ├── profile.html
│   │           ├── dashboard.html
│   │           └── user_list.html
│   │
│   ├── blog/
│   │   ├── models.py
│   │   ├── views.py
│   │   ├── urls.py
│   │   └── templates/
│   │       └── blog/
│   │           ├── post_list.html
│   │           ├── post_detail.html
│   │           └── post_form.html
│   │
│   └── orders/
│       ├── models.py
│       ├── views.py
│       ├── urls.py
│       └── templates/
│           └── orders/
│               ├── order_list.html
│               └── order_detail.html
│
└── static/
```

## 3. Template Syntax
### 1. URL's

```html
<a href="{% url 'blog:detail' id=post.id %}">
    View Post
</a>
```

```html
<a href="{% url 'category' slug=category.slug %}">
    {{ category.name }}
</a>
```

```html
<form method="post" action="{% url 'blog:create' %}">
    {% csrf_token %}
</form>
```
### 2. Model Object

```html
<h1>{{ post.title }}</h1>
<p>By {{ post.author.username }} on {{ post.created_at|date:"M d, Y" }}</p>
<p>{{ post.content }}</p>
```

### 3. Forms

!!! danger "CSRF TOKEN"

    CSRF = Cross-Site Request Forgery.

    A CSRF attack occurs when a malicious site tricks a logged-in user into submitting a form to your site without their knowledge.

    Django’s built-in CSRF protection prevents this by requiring a secret token on all POST requests.

    Always include {% csrf_token %} in POST, PUT, DELETE, or PATCH forms.

    Never trust user input — use form validation.

    Escape user input in templates (auto-escaping is default).

    Use ModelForm when possible to reduce manual errors.

    Validate both server-side and optionally client-side.

    Use AJAX requests with CSRF token in headers.

```html
<form method="post">
    {% csrf_token %}
    {% for field in form %}
        <div class="form-group">
            <label for="{{ field.id_for_label }}">{{ field.label }}</label>
            {{ field }}
            {% if field.help_text %}
                <small class="form-text text-muted">{{ field.help_text }}</small>
            {% endif %}
            {% for error in field.errors %}
                <div class="error">{{ error }}</div>
            {% endfor %}
        </div>
    {% endfor %}

    <button type="submit">Submit</button>
</form>
```

!!! tip "Using as_p, as_ul, as_table"

    - `{{ form.as_p }}` → Wraps each field in `<p>`

    - `{{ form.as_ul }}` → Wraps fields in `<li>`

    - `{{ form.as_table }}` → Wraps fields in `<tr>`

- `{{ form.as_p }}`:

```html
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Send</button>
</form>
```

- `{{ form.as_ul }}`:

```html
<form method="post">
    {% csrf_token %}
    {{ form.as_ul }}
    <button type="submit">Send</button>
</form>
```

- `{{ form.as_table }}`:

```html
<form method="post">
    {% csrf_token %}
    {{ form.as_table }}
    <button type="submit">Send</button>
</form>
```

### 4. Conditionals

- IF/ELSE:

```html
{% if user.is_authenticated %}
    <p>Welcome, {{ user.username }}!</p>
{% else %}
    <p>Hello, guest! <a href="{% url 'login' %}">Login</a></p>
{% endif %}
```

```html
{% if post.published %}
    <span class="badge bg-success">Published</span>
{% else %}
    <span class="badge bg-warning">Draft</span>
{% endif %}
```

```html
{% if user.age >= 18 %}
    <p>You are an adult.</p>
{% else %}
    <p>Access restricted.</p>
{% endif %}
```

- IF/ELIF/ELSE:

```html
{% if user.is_staff and user.is_active %}
    <p>Staff panel access granted.</p>
{% elif user.is_active %}
    <p>Regular user.</p>
{% else %}
    <p>Inactive user.</p>
{% endif %}
```

- Nested Iteration:

```html
{% if posts %}
    <ul>
    {% for post in posts %}
        <li>{{ post.title }}</li>
    {% endfor %}
    </ul>
{% else %}
    <p>No posts available.</p>
{% endif %}
```

- Combining Conditionals With Filters:

```html
{% if post.content|length > 100 %}
    <p>{{ post.content|truncatewords:20 }}...</p>
{% else %}
    <p>{{ post.content }}</p>
{% endif %}
```
- Example: Blog Post Card With Conditionals

```html
<div class="post-card">
    <h2>{{ post.title }}</h2>
    <p>By {{ post.author.username }} on {{ post.created_at|date:"M d, Y" }}</p>
    
    {% if post.published %}
        <span class="badge bg-success">Published</span>
    {% else %}
        <span class="badge bg-warning">Draft</span>
    {% endif %}

    {% if post.tags.all %}
        <p>Tags:
        {% for tag in post.tags.all %}
            {{ tag.name }}{% if not forloop.last %}, {% endif %}
        {% endfor %}
        </p>
    {% endif %}
</div>
```

!!! tip "Working with Conditionals"

    1. Templates cannot evaluate complex expressions (no arithmetic like a + b > c directly). Precompute in views or model properties.

    2. Keep logic presentation-only — avoid querying the database inside conditionals.

    3. Use {% if field.errors %} to highlight validation errors for forms.

    4. {% elif %} works exactly like Python elif.

    5. Avoid overly nested conditionals; precompute flags in view if needed.

### 5. Iteration

- Basic Iteration

```html
<ul>
{% for post in posts %}
    <li>{{ post.title }}</li>
{% endfor %}
</ul>
```

- Empty Iterables

```html
<ul>
{% for post in posts %}
    <li>{{ post.title }}</li>
{% empty %}
    <li>No posts available.</li>
{% endfor %}
</ul>
```
- Nested Iteration

```html
{% for category in categories %}
    <h3>{{ category.name }}</h3>
    <ul>
    {% for post in category.posts.all %}
        <li>{{ post.title }}</li>
    {% empty %}
        <li>No posts in this category.</li>
    {% endfor %}
    </ul>
{% endfor %}
```
- Iterating Over Dictionary Items

```html
{% for key, value in mydict.items %}
    <p>{{ key }} → {{ value }}</p>
{% endfor %}
```

- Slicing QuerySets

```html
{% for post in posts|slice:":5" %}
    <p>{{ post.title }}</p>
{% endfor %}
```

- Reversing

```html
{% for post in posts reversed %}
    <p>{{ post.title }}</p>
{% endfor %}
```

- Complete Example: Blog List With Iteration

```html
<h1>Blog Posts</h1>

{% for post in posts %}
<div class="post-card">
    <h2>{{ post.title }}</h2>
    <p>By {{ post.author.username }} on {{ post.created_at|date:"M d, Y" }}</p>

    {% if post.tags.all %}
        <p>Tags:
        {% for tag in post.tags.all %}
            {{ tag.name }}{% if not forloop.last %}, {% endif %}
        {% endfor %}
        </p>
    {% endif %}

    {% if forloop.last %}
        <hr>
    {% endif %}
</div>
{% empty %}
<p>No blog posts available.</p>
{% endfor %}
```

- ForeignKey Reverse Relation

```python
class Comment(models.Model):
    post = models.ForeignKey(Post, related_name="comments", on_delete=models.CASCADE)
    content = models.TextField()
```

```html
{% for comment in post.comments.all %}
    <p>{{ comment.content }}</p>
{% empty %}
    <p>No comments yet.</p>
{% endfor %}
```

- ManyToManyField

```html
<p>Tags:
{% for tag in post.tags.all %}
    {{ tag.name }}{% if not forloop.last %}, {% endif %}
{% empty %}
    None
{% endfor %}
</p>
```

!!! note "Best Practices"

    1. Always use {% empty %} to handle empty lists gracefully.

    2. Use forloop metadata for numbering, styling first/last items, or nested loops.

    3. Perform filtering/sorting in views, not in templates.

    4. Keep logic presentation-only — avoid complex calculations in loops.

    5. Use slicing or reversed for display purposes only.

### 6. Blocks & Partials

- The framework’s template inheritance system enables to create a base template that contains the standard structure and the layout for website or app.

- Child templates can be created, that inherit from the base template and override specific blocks of sections as needed. This encourages code reuse and consistency across different templates.

```html
<!-- base.html -->
<!DOCTYPE html>
<html>
  <head>
    <title>{% block title %}Default Title{% endblock %}</title>
  </head>
  <body>
    {% block content %}
    {% endblock %}
  </body>
</html>
```

```html
<!-- child_template.html -->
{% extends 'base.html' %}

{% block title %}My Page Title{% endblock %}

{% block content %}
  <h1>My Page Content</h1>
  <p>This is the content of my page.</p>
{% endblock %}
```

- Example: File Structure

```html
templates/
│
├── base.html
├── pages/
│   └── home.html
└── partials/
    ├── navbar.html
    └── footer.html

```

- base.html

```html
<!DOCTYPE html>
<html>
<head>
  <title>{% block title %}Default Title{% endblock %}</title>
</head>
<body>

  {% include "partials/navbar.html" %}

  <main>
    {% block content %}{% endblock %}
  </main>

  {% include "partials/footer.html" %}

</body>
</html>
```

- partials/navbar.html

```html
<nav>
  <ul>
    <li><a href="{% url 'home' %}">Home</a></li>
    <li><a href="{% url 'about' %}">About</a></li>
    <li><a href="{% url 'contact' %}">Contact</a></li>
  </ul>
</nav>
```

- partials/footer.html

```html
<footer>
  <p>&copy; {{ year }} MySite</p>
</footer>
```

- pages/home.html

```html
{% extends "base.html" %}

{% block title %}Home{% endblock %}

{% block content %}
  <h1>Welcome</h1>
  <p>This is the homepage.</p>
{% endblock %}
```
- Passing Context to Partials

```html
{% include "partials/footer.html" with year=2026 %}
```
- `only` prevents leaking the parent context.

- This improves predictability and reduces coupling.

```html
{% include "partials/footer.html" with year=2026 only %}
```
- partials/post_card.html

```html
<article class="post">
  <h2>{{ post.title }}</h2>
  <p>{{ post.summary }}</p>
</article>
```
- usage

```html
{% for post in posts %}
  {% include "partials/post_card.html" with post=post %}
{% endfor %}
```

### 7. Static Files

- `{% load %}` — Import Template Tag Libraries. Used to load built-in or custom template tag libraries.

- Loads the static template tag library.

- Required before using `{% static %}`.

```html
{% load static %}

<img src="{% static 'images/logo.png' %}" alt="Logo">

<script src="{% static 'js/main.js' %}"></script>
```
 
- Static Config in `settings.py`:

```python

STATIC_URL = '/static'

STATICFILES_DIRS = [os.path.join(BASe_DIR, 'static')]
```

### 8. Template Filters

- Django Template Filter Syntax:

```bash
{{ value|filter_name }}
{{ value|filter_name:argument }}
```
- Quick Summary Table

| Category | Common Filters                       |
| -------- | ------------------------------------ |
| Strings  | lower, upper, slugify, truncatewords |
| Numbers  | add, floatformat                     |
| Lists    | length, first, last, join            |
| Dates    | date, timesince                      |
| Logic    | default, yesno                       |
| HTML     | safe, escape, urlize                 |

#### 1. String Filters

- addslashes

- Escapes quotes with backslashes.
```html
{{ "I'm Django"|addslashes }}
```
- capfirst

- Escapes quotes with backslashes.
```html
{{ "django"|capfirst }}
```

- center

- Centers string in given width.
```html
{{ "hi"|center:10 }}
```

- cut

- Removes all occurences of substring.
```html
{{ "Hello World"|cut:" " }}
```

- escape

- Escapes HTML.
```html
{{ "<b>bold</b>"|escape }}
```

- escapejs

- Escapes for JavaScript strings.
```html
{{ value|escapejs }}
```

- linebreaks

- Converts line breaks into `<p>` and `<br>`.
```html
{{ text|linebreaks }}
```

- linebreaksbr

- Converts line breaks into `<br>` only.
```html
{{ text|linebreaksbr }}
```

- lower

- Lowercase letters.
```html
{{ "DJANGO"|lower }}
```

- upper

- Uppercase letters.
```html
{{ "django"|upper }}
```

- slugify

- Create a slug text.
```html
{{ "Hello World"|slugify }}
```

- title

- Turns text into Title.
```html
{{ "hello world"|title }}
```

- truncatechars
```html
{{ text|truncatechars:20 }}
```

- truncatewords
```html
{{ text|truncatewords:10 }}
```

- scriptags

- Removes HTML tags.
```html
{{ html_content|striptags }}
```

#### 2. Numeric Filters

- add
```html
{{ 5|add:3 }}
```

- floatformat
```html
{{ 3.14159|floatformat:2 }}
```

- filesizeformat
```html
{{ file_size|filesizeformat }}
```

#### 3. List Filters

- dictsort
```html
{% for item in mydict|dictsort %}
```

- dictsortreversed
```html
{% for item in mydict|dictsortreversed %}
```

- first
```html
{{ my_list|first }}
```

- last
```html
{{ my_list|last }}
```

- join
```html
{{ my_list|join:", " }}
```

- lenght
```html
{{ my_list|length }}
```

- random
```html
{{ my_list|random }}
```

- slice
```html
{{ my_list|slice:":3" }}
```

#### 4. Date & Time Filters

- date
```html
{{ my_date|date:"Y-m-d" }}
```

- time
```html
{{ my_time|time:"H:i" }}
```

- timesince
```html
{{ my_date|timesince }}
```

- timeuntil
```html
{{ future_date|timeuntil }}
```

#### 5. Boolean/Logic Filters

- default
```html
{{ value|default:"N/A" }}
```
- default_if_none
```html
{{ value|default_if_none:"N/A" }}
```

- yesno
```html
{{ value|yesno:"Yes,No,Maybe" }}
```

#### 6. Formatting & HTML Filters

- safe

- Use carefully (XSS risk).
```html
{{ html_content|safe }}
```

- urlencode
```html
{{ value|urlencode }}
```

- urlize

- Converts URLs to clickable links.
```html
{{ text|urlize }}
```

- wordcount
```html
{{ text|wordcount }}
```

- wordwrap
```html
{{ text|wordwrap:30 }}
```

#### 7. JSON & Debug

- json_script
```html
{{ data|json_script:"data-id" }}
```
- pprint
```html
{{ object|pprint }}
```

#### 8. Miscellaneous

- pluralize
```html
{{ count }} item{{ count|pluralize }}
```

- make_list
```html
{{ "abc"|make_list }}
```

- unordered_list
```html
{{ nested_list|unordered_list }}
```
## 4. Tags

- Comment 

```html
{% comment %}
    Hidden from output
{% endcomment %}
```

- With

```html
{% with total=cart.total %}
    {{ total }}
{% endwith %}
```

- Verbatim (Prevents template parsing)

    - It will render `{{ this_will_not_render }}` as is (including double brackets).

```html
{% verbatim %}
{{ this_will_not_render }}
{% endverbatim %}
```

- Language

```html
{% language "fr" %}
    {{ value }}
{% endlanguage %}
```

- Get current language

```html
{% get_current_language as LANGUAGE_CODE %}
```

- Get available languages

```html
{% get_available_languages as languages %}
```

- Block trans

```html
{% blocktrans %}
    Hello {{ username }}
{% endblocktrans %}

```

- Cycle

- Used for: 

    UI presentation patterns

    Alternating CSS classes

    Repeated decorative values

- Named Cycle (Reusable Inside Loop)

```html
{% for product in products %}
    {% cycle 'odd' 'even' as rowclass %}
    <tr class="{{ rowclass }}">
        <td>{{ product.name }}</td>
    </tr>
{% endfor %}
```

- Using cycle Multiple Times Per Row

```html
{% for item in items %}
    {% cycle 'red' 'blue' 'green' as color %}
    <div class="box {{ color }}">
        {{ item }}
        <span class="{{ color }}">Highlight</span>
    </div>
{% endfor %}
```

- Silent Cycle (Prevent Immediate Output)

```html
{% for item in items %}
    {% cycle 'odd' 'even' as rowclass silent %}
    <div class="{{ rowclass }}">
        {{ item }}
    </div>
{% endfor %}
```

- Resetting a Named Cycle

```html
{% for category in categories %}
    <h2>{{ category.name }}</h2>

    {% for product in category.products %}
        {% cycle 'odd' 'even' as rowclass %}
        <div class="{{ rowclass }}">
            {{ product.name }}
        </div>
    {% endfor %}

    {% resetcycle rowclass %}
{% endfor %}

```

## 5. Dynamic Partials

### Step 1 — Create the Model

```python
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    featured = models.BooleanField(default=False)

    def get_partial_template(self):
        """
        Return the template path for rendering this object.
        Keeps UI decision close to the model.
        """
        if self.featured:
            return "blog/partials/_featured_post.html"
        return "blog/partials/_standard_post.html"

    def __str__(self):
        return self.title
```

- Rendering strategy is tied to model state.

- Template logic remains minimal.

- View stays clean.

### Step 2 — Create the CBV

```python
from django.views.generic import ListView
from .models import Post

class PostListView(ListView):
    model = Post
    template_name = "blog/post_list.html"
    context_object_name = "posts"
```

- No branching logic inside the view.

- The model handles template resolution.

### Step 3 — Create Partial Templates

```bash
templates/
    blog/
        post_list.html
        partials/
            _standard_post.html
            _featured_post.html
```

```html
<!-- _standard_post.html -->

<article class="post">
    <h2>{{ post.title }}</h2>
    <p>{{ post.content|truncatewords:30 }}</p>
</article>

```

```html
<!-- _featured_post.html -->

<article class="post featured">
    <h2>⭐ {{ post.title }}</h2>
    <p>{{ post.content }}</p>
</article>
```
- Each partial has a single responsibility.

### Step 4 — Use Dynamic Include in Template

```html
<!-- post_list.html -->
{% extends "base.html" %}

{% block content %}

<h1>Posts</h1>

{% for post in posts %}
    {% include post.get_partial_template with post=post only %}
{% empty %}
    <p>No posts available.</p>
{% endfor %}

{% endblock %}
```
- `post.get_partial_template` determines which partial to use.

- `only` prevents context leakage.

- Template contains no branching logic.

### Step 5 — Add URL Configuration

```python
from django.urls import path
from .views import PostListView

urlpatterns = [
    path("posts/", PostListView.as_view(), name="post_list"),
]
```
```mermaid
flowchart TD

    A[HTTP Request] --> B[PostListView]
    B --> C[Queryset Retrieved]
    C --> D[Template Rendered: post_list.html]

    D --> E{Loop Through Posts}

    E --> F["Call post.get_partial_template()"]
    F --> G[Determine Partial Template Path]
    G --> H[Include Correct Partial Template]

    H --> E
```
### Alternative: Compute in get_context_data()

- Model:

```python
class PostListView(ListView):
    model = Post
    template_name = "blog/post_list.html"

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        posts = context["object_list"]

        for post in posts:
            post.partial_template = (
                "blog/partials/_featured_post.html"
                if post.featured
                else "blog/partials/_standard_post.html"
            )

        return context
```

- Template:
```html
{% for post in object_list %}
    {% include post.partial_template with post=post only %}
{% endfor %}
```
### When to Choose Which Approach?

| Approach             | When to Use                               |
| -------------------- | ----------------------------------------- |
| Model method         | Rendering strategy depends on model state |
| `get_context_data()` | Rendering depends on request/user context |
| Service layer        | Rendering logic is complex or cross-model |

### Best Practices Summary Table

| Category             | Best Practice                                            | Why                             |
| -------------------- | -------------------------------------------------------- | ------------------------------- |
| Template Logic       | Keep branching out of templates                          | Improves readability            |
| Dynamic Includes     | Compute template name in Python                          | Clean separation of concerns    |
| Context Isolation    | Use `only` in `{% include %}`                            | Prevent context leakage         |
| File Structure       | Store partials in `app/partials/`                        | Namespacing + clarity           |
| Partial Naming       | Prefix with `_`                                          | Signals non-standalone template |
| Model Responsibility | Use model method when rendering depends on model state   | Encapsulation                   |
| View Responsibility  | Use `get_context_data()` for request-dependent decisions | Proper layering                 |
| Avoid                | Heavy logic in templates                                 | Violates MVC separation         |
| Reusability          | Keep partials small and single-purpose                   | Scalable UI composition         |

!!! tip "Architectural Pattern"

    Well-structured dynamic rendering follows:

    Model → defines rendering strategy

    View → retrieves data

    Template → delegates to partial

    Partial → renders UI

## 6. Django-Cotton Package

### 1. Django-Cotton Setup

```mermaid
flowchart TD

    A["Data Layer<br/>(Models)"] --> B["Application Layer<br/>(Class-Based Views)"]
    B --> C["Template Layer<br/>(Iteration & Layout)"]
    C --> D["Component Layer<br/>(UI Modules / Cotton Components)"]

    D --> E[Rendered HTML Response]
```

#### 1. Install Cotton

```bash
pip install django-cotton
```
#### 2. Register in INSTALLED_APPS

```python
INSTALLED_APPS = [
    ...
    "cotton",
]
```
#### 3. Project Structure Example

```bash
templates/
    base.html
    blog/
        post_list.html
    components/
        post-card.html
        featured-post-card.html
```
#### 4. Add `{% load cotton %}` to HTML

```html
{% load cotton %}
```

### 2. Cotton Example

#### 1. Model

```python
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    featured = models.BooleanField(default=False)

    def get_component_name(self):
        """
        Decide which component should render this object.
        """
        return "featured-post-card" if self.featured else "post-card"

    def __str__(self):
        return self.title
```

#### 2. CBV (Class Based View)

```python
from django.views.generic import ListView
from .models import Post

class PostListView(ListView):
    model = Post
    template_name = "blog/post_list.html"
    context_object_name = "posts"
```

#### 3. Create Component

- templates/components/post-card.html

```html
<article class="post-card">
    <h2>{{ post.title }}</h2>
    <p>{{ post.content|truncatewords:20 }}</p>
</article>
```

- templates/components/featured-post-card.html

```html
<article class="post-card featured">
    <h2>⭐ {{ post.title }}</h2>
    <p>{{ post.content }}</p>
</article>
```

#### 4. Use Cotton in Template

```html
{% extends "base.html" %}
{% load cotton %}

{% block content %}

<h1>Posts</h1>

{% for post in posts %}
    {% cotton post.get_component_name post=post %}
{% empty %}
    <p>No posts available.</p>
{% endfor %}

{% endblock %}
```

- No `{% include %}`

- No branching

- Fully component-driven rendering

## 7. Example & Best Practices

- App-Level Templates (Namespaced):

```bash
project/
│
├── project/
│   ├── settings.py
│   └── urls.py
│
├── apps/
│   ├── blog/
│   │   ├── templates/
│   │   │   └── blog/
│   │   │       ├── base_blog.html
│   │   │       ├── post_list.html
│   │   │       ├── post_detail.html
│   │   │       └── partials/
│   │   │           └── post_card.html
│   │   └── views.py
│   │
│   └── accounts/
│       ├── templates/
│       │   └── accounts/
│       │       ├── login.html
│       │       └── register.html
│
└── templates/
    ├── base.html
    ├── partials/
    │   ├── navbar.html
    │   ├── footer.html
    │   └── messages.html
    └── includes/

```

- Typical `base.html`:

```html 
{% load static %}

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>{% block title %}MySite{% endblock %}</title>

    <link rel="stylesheet" href="{% static 'css/main.css' %}">
    {% block extra_css %}{% endblock %}
</head>
<body>

    {% include "partials/_navbar.html" %}

    {% include "partials/_messages.html" %}

    <main class="container">
        {% block content %}{% endblock %}
    </main>

    {% include "partials/_footer.html" %}

    <script src="{% static 'js/main.js' %}"></script>
    {% block extra_js %}{% endblock %}

</body>
</html>
```

- Django Templates Best Practices:

| Area               | Best Practice                       | Description                                                                                 |
| ------------------ | ----------------------------------- | ------------------------------------------------------------------------------------------- |
| **File Structure** | Use app namespacing                 | Place templates inside `app_name/templates/app_name/` to avoid naming conflicts             |
|                    | Separate global templates           | Keep shared templates (e.g., `base.html`, global partials) in a root `templates/` directory |
| **Naming**         | Use explicit, role-based names      | Example: `post_list.html`, `post_detail.html`, `post_form.html`                             |
|                    | Use `_prefix` for partials          | Example: `_navbar.html`, `_form_field.html` to signal non-standalone templates              |
| **Base Template**  | Keep it structural only             | Define layout, static files, and layout structure — avoid business logic                    |
|                    | Provide extension blocks            | Define `{% block content %}`, `{% block title %}`, `{% block extra_js %}`, etc.             |
| **Partials**       | Keep them small and reusable        | Each partial should serve one UI responsibility                                             |
|                    | Store inside `partials/`            | Organize reusable fragments in a dedicated folder                                           |
|                    | Use `with` and `only` appropriately | Pass controlled context using `{% include "..." with var=value only %}` when needed         |


- Django Templates — Responsibilities & Boundaries:

| Category               | Allowed / Expected                                            | Not Allowed                  | Where It Should Go Instead   |                 |                                                         |                        |
| ---------------------- | ------------------------------------------------------------- | ---------------------------- | ---------------------------- | --------------- | ------------------------------------------------------- | ---------------------- |
| **Data Handling**      | Render context data passed from the view                      | Fetch data from the database | View or Model                |                 |                                                         |                        |
| **UI Rendering**       | Display HTML structure and layout                             | Generate business rules      | View or Model                |                 |                                                         |                        |
| **Logic Level**        | Handle light presentation logic (`if`, `for`, simple filters) | Heavy conditional logic      | View                         |                 |                                                         |                        |
| **Computation**        | Minor formatting (`                                           | date`, `                     | length`, `                   | truncatewords`) | Business calculations (pricing, discounts, permissions) | Model or Service Layer |
| **Database Access**    | None                                                          | ORM queries inside templates | View                         |                 |                                                         |                        |
| **Complex Formatting** | Basic formatting using filters                                | Complex transformations      | Custom template tag / filter |                 |                                                         |                        |
| **Reusability**        | Use `{% include %}` and `{% extends %}`                       | Duplicate layout code        | Base template / partials     |                 |                                                         |                        |

- Rule of thumb:

| If the logic becomes…     | Move it to…                 |
| ------------------------- | --------------------------- |
| Data retrieval logic      | View                        |
| Business rules            | Model                       |
| Reusable formatting logic | Custom template tag         |
| Complex UI transformation | Custom template tag or View |