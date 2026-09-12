# Configuration Conventions

## Common project structure

```text
config/
├── __init__.py
├── urls.py
├── asgi.py
├── wsgi.py
└── settings/
    ├── __init__.py
    ├── base.py
    ├── development.py
    ├── production.py
    └── test.py
```

## `settings.py` structure

```python
"""
Django settings for myproject project.

This is a monolithic (single-file) settings.py suitable for small-to-medium projects.
It uses environment variables for sensitive/production values.
Compatible with Django 5.1 – 6.0 (2026 era).

For the full list of settings and their values, see:
https://docs.djangoproject.com/en/stable/ref/settings/
"""

import os
from pathlib import Path

# ───────────────────────────────────────────────
#               PATHS & PROJECT ROOT
# ───────────────────────────────────────────────

BASE_DIR = Path(__file__).resolve().parent.parent
# BASE_DIR points to the folder containing manage.py
# Almost all filesystem paths are built relative to this.

# ───────────────────────────────────────────────
#             SECURITY & DEBUG (MOST IMPORTANT)
# ───────────────────────────────────────────────

SECRET_KEY = os.environ.get(
    "DJANGO_SECRET_KEY",
    "django-insecure-CHANGE-ME-very-long-random-secure-string-9876543210"
)
# MUST be a long, unique, unpredictable value in production.
# Never commit the real value — always use env var.

DEBUG = os.environ.get("DJANGO_DEBUG", "True").lower() in ("true", "1", "yes", "on")
# True → detailed error pages, auto-reload, debug toolbar (if installed)
# False → generic 500/404 pages + production hardening (see below)

ALLOWED_HOSTS = os.environ.get(
    "DJANGO_ALLOWED_HOSTS",
    "localhost,127.0.0.1,[::1]"
).split(",")
# Required when DEBUG=False — otherwise you'll get 400 Bad Request errors.
# Example production value: "example.com,www.example.com,backend.example.com"

# ───────────────────────────────────────────────
#                  APPLICATIONS
# ───────────────────────────────────────────────

INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    
    # ── Your own applications ──
    # 'blog.apps.BlogConfig',
    # 'accounts.apps.AccountsConfig'
]

# ───────────────────────────────────────────────
#                   MIDDLEWARE
# ───────────────────────────────────────────────

MIDDLEWARE = [
    "django.middleware.security.SecurityMiddleware",
    "django.contrib.sessions.middleware.SessionMiddleware",
    "django.middleware.common.CommonMiddleware",
    "django.middleware.csrf.CsrfViewMiddleware",
    "django.contrib.auth.middleware.AuthenticationMiddleware",
    "django.contrib.messages.middleware.MessageMiddleware",
    "django.middleware.clickjacking.XFrameOptionsMiddleware",
    # Add custom middleware here if needed (e.g. logging, rate limiting)
]

# ───────────────────────────────────────────────
#              URLS, TEMPLATES, WSGI
# ───────────────────────────────────────────────

ROOT_URLCONF = "myproject.urls"

TEMPLATES = [
    {
        "BACKEND": "django.template.backends.django.DjangoTemplates",
        "DIRS": [BASE_DIR / "templates"],           # project-wide templates
        "APP_DIRS": True,                           # app-level templates/<app_name>/
        "OPTIONS": {
            "context_processors": [
                "django.template.context_processors.debug",
                "django.template.context_processors.request",
                "django.contrib.auth.context_processors.auth",
                "django.contrib.messages.context_processors.messages",
            ],
        },
    },
]

WSGI_APPLICATION = "myproject.wsgi.application"

# ───────────────────────────────────────────────
#                   DATABASE
# ───────────────────────────────────────────────

DATABASES = {
    "default": {
        "ENGINE": os.environ.get("DB_ENGINE", "django.db.backends.sqlite3"),
        "NAME": os.environ.get("DB_NAME", BASE_DIR / "db.sqlite3"),
        "USER": os.environ.get("DB_USER", ""),
        "PASSWORD": os.environ.get("DB_PASSWORD", ""),
        "HOST": os.environ.get("DB_HOST", ""),
        "PORT": os.environ.get("DB_PORT", ""),
        # Optional extras (useful for postgres/mysql)
        # "OPTIONS": {"sslmode": "require"},  # for cloud databases
    }
}

# ───────────────────────────────────────────────
#              PASSWORD VALIDATORS
# ───────────────────────────────────────────────

AUTH_PASSWORD_VALIDATORS = [
    {"NAME": "django.contrib.auth.password_validation.UserAttributeSimilarityValidator"},
    {"NAME": "django.contrib.auth.password_validation.MinimumLengthValidator"},
    {"NAME": "django.contrib.auth.password_validation.CommonPasswordValidator"},
    {"NAME": "django.contrib.auth.password_validation.NumericPasswordValidator"},
]

# ───────────────────────────────────────────────
#         INTERNATIONALIZATION & TIMEZONE
# ───────────────────────────────────────────────

LANGUAGE_CODE = "en-us"

TIME_ZONE = "UTC"  # ← Recommended: change to your real region e.g. "Europe/Berlin"

USE_I18N = True

USE_TZ = True

# ───────────────────────────────────────────────
#               STATIC & MEDIA FILES
# ───────────────────────────────────────────────

STATIC_URL = "/static/"
STATICFILES_DIRS = [BASE_DIR / "static"]           # dev: extra static folders
STATIC_ROOT = BASE_DIR / "staticfiles"             # prod: collectstatic destination

MEDIA_URL = "/media/"
MEDIA_ROOT = BASE_DIR / "media"                    # user-uploaded files

# Modern default primary key (recommended since Django 3.2+)
DEFAULT_AUTO_FIELD = "django.db.models.BigAutoField"

# ───────────────────────────────────────────────
#             AUTHENTICATION REDIRECTS
# ───────────────────────────────────────────────

LOGIN_REDIRECT_URL = "/"
LOGOUT_REDIRECT_URL = "/"
LOGIN_URL = "/accounts/login/"

# ───────────────────────────────────────────────
#                   EMAIL SETTINGS
# ───────────────────────────────────────────────

EMAIL_BACKEND = os.environ.get(
    "EMAIL_BACKEND",
    "django.core.mail.backends.console.EmailBackend"   # prints to console in dev
)
# Production example (uncomment & use env vars):
# EMAIL_HOST = os.environ.get("EMAIL_HOST", "smtp.mailgun.org")
# EMAIL_PORT = int(os.environ.get("EMAIL_PORT", 587))
# EMAIL_USE_TLS = True
# EMAIL_HOST_USER = os.environ.get("EMAIL_HOST_USER")
# EMAIL_HOST_PASSWORD = os.environ.get("EMAIL_HOST_PASSWORD")
# DEFAULT_FROM_EMAIL = "no-reply@yourdomain.com"

# ───────────────────────────────────────────────
#           PRODUCTION HARDENING (when DEBUG=False)
# ───────────────────────────────────────────────

if not DEBUG:
    # HTTP → HTTPS redirection (requires front-end proxy / load balancer)
    SECURE_SSL_REDIRECT = True
    
    # Secure cookies
    SESSION_COOKIE_SECURE = True
    CSRF_COOKIE_SECURE = True
    
    # Browser protections
    SECURE_BROWSER_XSS_FILTER = True
    SECURE_CONTENT_TYPE_NOSNIFF = True
    X_FRAME_OPTIONS = "DENY"
    
    # HSTS (HTTP Strict Transport Security) – tells browsers to always use HTTPS
    SECURE_HSTS_SECONDS = 31536000          # 1 year
    SECURE_HSTS_INCLUDE_SUBDOMAINS = True
    SECURE_HSTS_PRELOAD = True
    
    # Logging (minimal console logging – can be expanded)
    LOGGING = {
        "version": 1,
        "disable_existing_loggers": False,
        "handlers": {"console": {"class": "logging.StreamHandler"}},
        "root": {"handlers": ["console"], "level": "INFO"},
    }

    # Optional: use Whitenoise for static files in production
    # STATICFILES_STORAGE = "whitenoise.storage.CompressedManifestStaticFilesStorage"

# ───────────────────────────────────────────────
#          DEVELOPMENT-ONLY TOOLS (debug toolbar)
# ───────────────────────────────────────────────

if DEBUG:
    try:
        import debug_toolbar  # noqa
        INSTALLED_APPS.append("debug_toolbar")
        MIDDLEWARE.insert(0, "debug_toolbar.middleware.DebugToolbarMiddleware")
        INTERNAL_IPS = ["127.0.0.1", "localhost"]
    except ImportError:
        pass  # debug-toolbar not installed → silently skip

# ───────────────────────────────────────────────
#                     THE END
# ───────────────────────────────────────────────
```

## Config separation

```text
base.py       → shared
development.py → overrides
production.py → overrides
test.py        → overrides
```

### Base

This should contain things that are the same everywhere (shared settings with dev and prod).

```python
# config/settings/base.py

from pathlib import Path


BASE_DIR = Path(__file__).resolve().parent.parent.parent


# =============================================================================
# Application definition
# =============================================================================

INSTALLED_APPS = [
    # Django
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",

    # Third-party
    "django_filters",
    "rest_framework",

    # Local
    "products.apps.ProductsConfig",
    "orders.apps.OrdersConfig",
    "accounts.apps.AccountsConfig",
]


# =============================================================================
# Middleware
# =============================================================================

MIDDLEWARE = [
    "django.middleware.security.SecurityMiddleware",
    "django.contrib.sessions.middleware.SessionMiddleware",
    "django.middleware.common.CommonMiddleware",
    "django.middleware.csrf.CsrfViewMiddleware",
    "django.contrib.auth.middleware.AuthenticationMiddleware",
    "django.contrib.messages.middleware.MessageMiddleware",
]


ROOT_URLCONF = "config.urls"

WSGI_APPLICATION = "config.wsgi.application"
ASGI_APPLICATION = "config.asgi.application"


# =============================================================================
# Templates
# =============================================================================

TEMPLATES = [
    {
        "BACKEND": "django.template.backends.django.DjangoTemplates",
        "DIRS": [BASE_DIR / "templates"],
        "APP_DIRS": True,
        "OPTIONS": {
            "context_processors": [
                "django.template.context_processors.request",
                "django.contrib.auth.context_processors.auth",
                "django.contrib.messages.context_processors.messages",
            ],
        },
    },
]


# =============================================================================
# Internationalization
# =============================================================================

LANGUAGE_CODE = "en-gb"
TIME_ZONE = "Europe/London"

USE_I18N = True
USE_TZ = True


# =============================================================================
# Static files
# =============================================================================

STATIC_URL = "static/"
STATIC_ROOT = BASE_DIR / "staticfiles"

MEDIA_URL = "media/"
MEDIA_ROOT = BASE_DIR / "media"


# =============================================================================
# Django defaults
# =============================================================================

DEFAULT_AUTO_FIELD = "django.db.models.BigAutoField"
```

### Development

Development imports the shared configuration and overrides what needs to differ.

```python
# config/settings/development.py

from .base import *


DEBUG = True

ALLOWED_HOSTS = [
    "localhost",
    "127.0.0.1",
]


# =============================================================================
# Database
# =============================================================================

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.sqlite3",
        "NAME": BASE_DIR / "db.sqlite3",
    }
}


# =============================================================================
# Email
# =============================================================================

EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"
```

### Production

Production does the opposite.

```python
# config/settings/production.py

import os

from .base import *


DEBUG = False

ALLOWED_HOSTS = [
    "example.com",
    "www.example.com",
]


# =============================================================================
# Security
# =============================================================================

SECRET_KEY = os.environ["DJANGO_SECRET_KEY"]


# =============================================================================
# Database
# =============================================================================

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "NAME": os.environ["DB_NAME"],
        "USER": os.environ["DB_USER"],
        "PASSWORD": os.environ["DB_PASSWORD"],
        "HOST": os.environ["DB_HOST"],
        "PORT": os.environ.get("DB_PORT", "5432"),
    }
}


# =============================================================================
# Security headers
# =============================================================================

SECURE_SSL_REDIRECT = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
```

### Test

```python
# config/settings/test.py

from .base import *


DEBUG = False

PASSWORD_HASHERS = [
    "django.contrib.auth.hashers.MD5PasswordHasher",
]

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.sqlite3",
        "NAME": ":memory:",
    }
}
```

---

## Tips

### Git & migrations

Always commit migration files to git — every single one.You should never:
    
    - .gitignore migrations

    - Regenerate migrations on the production server

    - Have different migrations in dev vs prod

Why committing migrations is safe and necessaryWhen you merge a `branch → main → deploy`: 

    - You pull the new code (including new/changed migration files)

    - You run python manage.py migrate

Django sees which migrations are already applied (via django_migrations table) and only runs the new ones.Even if you created 15 migrations locally while developing a big feature, after merging only the net new schema changes are applied in production — exactly once.

- Common workflow:

```bash
# On your development machine
# Create models, make changes...
python manage.py makemigrations
python manage.py migrate              # test locally

# then (still development)
git add apps/*/migrations/
git commit -m "Add user profile fields + migrations"

git push
# → create PR → review → merge to main

# On production / CI / deploy server (after git pull / deploy)
python manage.py migrate               # ← only new migrations run
python manage.py collectstatic --noinput
# restart gunicorn / uvicorn / etc.
```

---

### Hardcode configuration

- After creating the project, the folder directory is:

```bash
supermarket/               ← this is the Python package (the "project" folder)
├── manage.py
├── supermarket/           ← inner folder = the settings module
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
```

- Renaming and making modular `settings.py`:

    - Step 1: Rename the inner `supermarket/` folder to `config/`
    - Step 2: Open manage.py and config/wsgi.py (and config/asgi.py if you have it) and change:

                ```python
                # from
                os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'supermarket.settings')

                # to
                os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'config.settings')
                ```
    
    - Step 3: Move settings.py → config/settings/base.py and create dev.py, prod.py (see above modular setup).

---

!!! tip "Tips to avoid pain"

    - Review migration files before committing (especially RemoveField, AlterField that can be destructive)

    - Test migrations on a copy of production data (staging)

    - Use --fake carefully (only when you really know what you're doing)

    - Periodically python manage.py squashmigrations (after major releases)

    - Never run makemigrations on production