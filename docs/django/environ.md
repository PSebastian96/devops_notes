# Environment Variables

## Django Environment Variables & Configuration

This section describes a conventional, production-friendly way to manage Django configuration using:

- modular Django settings
- `uv` for dependency and command management
- `django-environ` for environment variables
- `.env` for local development
- environment variables / secret management for production

The approach follows the general configuration philosophy of *Two Scoops of Django*: keep settings explicit, separate environment-specific configuration, avoid duplication, and keep secrets out of source control.

---

## Configuration philosophy

A useful rule is:

> **Settings files define configuration structure. Environment variables provide values that vary between environments or must remain secret.**

Do not put every setting into an environment variable.

For example, this is ordinary application configuration and can remain in Python:

```python
LANGUAGE_CODE = "en-gb"

TIME_ZONE = "Europe/London"

USE_I18N = True

USE_TZ = True
```

Whereas these are good candidates for environment variables:

```text
DJANGO_SECRET_KEY
DJANGO_ALLOWED_HOSTS
DATABASE_URL
EMAIL_HOST_PASSWORD
API keys
OAuth secrets
```

The goal is to keep configuration:

- explicit
- easy to understand
- easy to test
- different where environments actually differ
- free of secrets in source control

---

### Recommended project structure

For a real-world Django project, use a settings package rather than one large `settings.py`:

```text
myproject/
├── .env
├── .env.example
├── .gitignore
├── manage.py
├── pyproject.toml
├── uv.lock
│
├── config/
│   ├── __init__.py
│   ├── asgi.py
│   ├── urls.py
│   ├── wsgi.py
│   │
│   └── settings/
│       ├── __init__.py
│       ├── base.py
│       ├── local.py
│       ├── production.py
│       └── test.py
│
├── accounts/
├── orders/
├── products/
│
├── templates/
├── static/
└── media/
```

### Why `local.py` instead of `development.py`?

`local.py` describes the purpose more precisely:

```text
local.py
    → developer's machine

production.py
    → production deployment

test.py
    → automated tests
```

The name `development.py` is also valid, but `local.py` is often clearer because development can include shared development/staging infrastructure, whereas `local.py` specifically describes the developer's local environment.

---

## `uv` and environment configuration

Use `uv` to manage Python dependencies and run project commands.

Install `django-environ`:

```bash
uv add django-environ
```

This updates:

```text
pyproject.toml
uv.lock
```

Both should be committed to git.

Run Django commands through `uv`:

```bash
uv run python manage.py runserver
```

```bash
uv run python manage.py migrate
```

```bash
uv run python manage.py createsuperuser
```

```bash
uv run python manage.py collectstatic --noinput
```

For tests:

```bash
uv run pytest
```

The important distinction is:

> **`uv` manages the Python environment and dependencies. `django-environ` manages application configuration values.**

They solve different problems.

---

## Environment files

### `.env`

Use `.env` for local development values:

```dotenv
DJANGO_SECRET_KEY=django-insecure-local-development-key

DJANGO_ALLOWED_HOSTS=localhost,127.0.0.1

DATABASE_URL=sqlite:///db.sqlite3

EMAIL_HOST=
EMAIL_PORT=587
EMAIL_USE_TLS=True
EMAIL_HOST_USER=
EMAIL_HOST_PASSWORD=
DEFAULT_FROM_EMAIL=webmaster@localhost
```

The real `.env` file must not be committed.

---

### `.env.example`

Commit a template containing the variables the application expects:

```dotenv
# =============================================================================
# Django
# =============================================================================

DJANGO_SECRET_KEY=
DJANGO_ALLOWED_HOSTS=
DJANGO_CSRF_TRUSTED_ORIGINS=


# =============================================================================
# Database
# =============================================================================

DATABASE_URL=


# =============================================================================
# Email
# =============================================================================

EMAIL_HOST=
EMAIL_PORT=587
EMAIL_USE_TLS=True
EMAIL_HOST_USER=
EMAIL_HOST_PASSWORD=
DEFAULT_FROM_EMAIL=
```

A new developer can create their local environment with:

```bash
cp .env.example .env
```

and then fill in the appropriate values.

`.env.example` is therefore both:

- configuration documentation
- a starting point for new developers

---

## `.gitignore`

The real environment file should be ignored:

```gitignore
# Python
__pycache__/
*.py[cod]

# Virtual environments
.venv/

# Environment variables
.env
.env.*
!.env.example

# Django
db.sqlite3
media/
staticfiles/

# Testing
.pytest_cache/
.coverage
htmlcov/

# IDE
.vscode/
.idea/
```

The important rule is:

> **Never commit real secrets.**

Do not rely on `.gitignore as a security mechanism after a secret has already been committed. If a secret is accidentally committed, rotate it.

---

## `django-environ`

Install:

```bash
uv add django-environ
```

Then initialise it once in `base.py`:

```python
# config/settings/base.py

from pathlib import Path

import environ


BASE_DIR = Path(__file__).resolve().parent.parent.parent


env = environ.Env(
    DJANGO_DEBUG=(bool, False),
)

environ.Env.read_env(BASE_DIR / ".env")
```

This creates a single configuration object:

```python
env
```

which can then be used throughout the settings package.

---

### Reading environment variables

For a required value:

```python
SECRET_KEY = env("DJANGO_SECRET_KEY")
```

If the variable is missing, Django fails during startup.

This is desirable for required production configuration.

For optional values:

```python
DEBUG = env.bool(
    "DJANGO_DEBUG",
    default=False,
)
```

For integers:

```python
DB_PORT = env.int(
    "DB_PORT",
    default=5432,
)
```

For lists:

```python
ALLOWED_HOSTS = env.list(
    "DJANGO_ALLOWED_HOSTS",
    default=["localhost", "127.0.0.1"],
)
```

The general rule is:

> **Required configuration should fail fast. Optional configuration can have sensible defaults.**

---

## Configuration separation

The settings package should have one clear responsibility for each file:

```text
base.py
    ↓
shared configuration

local.py
    ↓
local-development overrides

production.py
    ↓
production overrides

test.py
    ↓
test-specific overrides
```

The resulting configuration can be thought of as:

```text
                         base.py
                            │
             ┌──────────────┼──────────────┐
             │              │              │
             ▼              ▼              ▼
          local.py    production.py     test.py
             │              │              │
             ▼              ▼              ▼
           Local        Production       Tests
```

The principle is:

> **Put a setting in `base.py` if it is genuinely shared. Put it in an environment-specific file if it differs between environments.**

---

### `base.py`

`base.py` is the shared foundation.

It should contain configuration that is common to all environments.

```python
# config/settings/base.py

from pathlib import Path

import environ


# =============================================================================
# Paths
# =============================================================================

BASE_DIR = Path(__file__).resolve().parent.parent.parent


# =============================================================================
# Environment
# =============================================================================

env = environ.Env(
    DJANGO_DEBUG=(bool, False),
)

# Loads .env when present.
#
# This is primarily useful for local development.
# Production should normally inject environment variables directly through
# the deployment environment / secret manager.
environ.Env.read_env(BASE_DIR / ".env")


# =============================================================================
# Security
# =============================================================================

SECRET_KEY = env("DJANGO_SECRET_KEY")


# =============================================================================
# Applications
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
    "accounts",
    "orders",
    "products",
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
    "django.middleware.clickjacking.XFrameOptionsMiddleware",
]


# =============================================================================
# URL / WSGI / ASGI
# =============================================================================

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
# Password validation
# =============================================================================

AUTH_PASSWORD_VALIDATORS = [
    {
        "NAME": (
            "django.contrib.auth.password_validation."
            "UserAttributeSimilarityValidator"
        ),
    },
    {
        "NAME": (
            "django.contrib.auth.password_validation."
            "MinimumLengthValidator"
        ),
    },
    {
        "NAME": (
            "django.contrib.auth.password_validation."
            "CommonPasswordValidator"
        ),
    },
    {
        "NAME": (
            "django.contrib.auth.password_validation."
            "NumericPasswordValidator"
        ),
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
# Static / Media
# =============================================================================

STATIC_URL = "/static/"

STATIC_ROOT = BASE_DIR / "staticfiles"

MEDIA_URL = "/media/"

MEDIA_ROOT = BASE_DIR / "media"


# =============================================================================
# Authentication
# =============================================================================

LOGIN_URL = "/accounts/login/"

LOGIN_REDIRECT_URL = "/"

LOGOUT_REDIRECT_URL = "/"


# =============================================================================
# Email defaults
# =============================================================================

DEFAULT_FROM_EMAIL = env(
    "DEFAULT_FROM_EMAIL",
    default="webmaster@localhost",
)


# =============================================================================
# Django defaults
# =============================================================================

DEFAULT_AUTO_FIELD = "django.db.models.BigAutoField"
```

## What belongs in `base.py`?

Typically:

```text
Applications
Middleware
Templates
URL configuration
WSGI / ASGI
Password validators
Internationalization
Static/media paths
Authentication defaults
Django defaults
Shared third-party configuration
```

Avoid putting environment-specific database, email, or production security settings here unless they genuinely are shared.

---

### `local.py`

`local.py` should contain only local-development differences.

```python
# config/settings/local.py

from .base import *


# =============================================================================
# Development
# =============================================================================

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


# =============================================================================
# Development tools
# =============================================================================

# Enable these when installed.

# INSTALLED_APPS += [
#     "debug_toolbar",
# ]

# MIDDLEWARE += [
#     "debug_toolbar.middleware.DebugToolbarMiddleware",
# ]


INTERNAL_IPS = [
    "127.0.0.1",
]
```

Notice that there is no need to put this into `.env`:

```dotenv
DJANGO_DEBUG=True
```

if `local.py` already explicitly defines:

```python
DEBUG = True
```

Do not turn every setting into an environment variable.

---

### `production.py`

Production should be stricter.

```python
# config/settings/production.py

from .base import *


# =============================================================================
# Security
# =============================================================================

DEBUG = False

ALLOWED_HOSTS = env.list(
    "DJANGO_ALLOWED_HOSTS",
)

CSRF_TRUSTED_ORIGINS = env.list(
    "DJANGO_CSRF_TRUSTED_ORIGINS",
)


# =============================================================================
# Database
# =============================================================================

DATABASES = {
    "default": env.db(
        "DATABASE_URL",
    )
}


# =============================================================================
# Email
# =============================================================================

EMAIL_BACKEND = "django.core.mail.backends.smtp.EmailBackend"

EMAIL_HOST = env("EMAIL_HOST")

EMAIL_PORT = env.int(
    "EMAIL_PORT",
    default=587,
)

EMAIL_USE_TLS = env.bool(
    "EMAIL_USE_TLS",
    default=True,
)

EMAIL_HOST_USER = env("EMAIL_HOST_USER")

EMAIL_HOST_PASSWORD = env("EMAIL_HOST_PASSWORD")

DEFAULT_FROM_EMAIL = env("DEFAULT_FROM_EMAIL")


# =============================================================================
# HTTPS / Cookies
# =============================================================================

SECURE_SSL_REDIRECT = True

SESSION_COOKIE_SECURE = True

CSRF_COOKIE_SECURE = True

SECURE_BROWSER_XSS_FILTER = True

SECURE_CONTENT_TYPE_NOSNIFF = True

X_FRAME_OPTIONS = "DENY"


# =============================================================================
# HSTS
# =============================================================================

SECURE_HSTS_SECONDS = 31536000

SECURE_HSTS_INCLUDE_SUBDOMAINS = True

SECURE_HSTS_PRELOAD = True
```

The important pattern is that production requires its important values:

```python
ALLOWED_HOSTS = env.list("DJANGO_ALLOWED_HOSTS")

DATABASES = {
    "default": env.db("DATABASE_URL")
}
```

rather than silently falling back to local development values.

---

### `test.py`

Tests should have their own predictable configuration.

```python
# config/settings/test.py

from .base import *


# =============================================================================
# Test configuration
# =============================================================================

DEBUG = False

SECRET_KEY = "test-only-secret-key"


# =============================================================================
# Database
# =============================================================================

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.sqlite3",
        "NAME": ":memory:",
    }
}


# =============================================================================
# Password hashing
# =============================================================================

PASSWORD_HASHERS = [
    "django.contrib.auth.hashers.MD5PasswordHasher",
]


# =============================================================================
# Email
# =============================================================================

EMAIL_BACKEND = "django.core.mail.backends.locmem.EmailBackend"
```

Test settings are optimized for:

- speed
- isolation
- deterministic behavior

---

# Selecting the settings environment

Django uses `DJANGO_SETTINGS_MODULE` to determine which settings package is loaded.

For local development:

```bash
DJANGO_SETTINGS_MODULE=config.settings.local \
uv run python manage.py runserver
```

For production:

```bash
DJANGO_SETTINGS_MODULE=config.settings.production \
uv run gunicorn config.wsgi
```

For tests:

```bash
DJANGO_SETTINGS_MODULE=config.settings.test \
uv run pytest
```

You can also make local development the default in `manage.py`:

```python
# manage.py

os.environ.setdefault(
    "DJANGO_SETTINGS_MODULE",
    "config.settings.local",
)
```

Production deployment should explicitly use:

```text
config.settings.production
```

rather than relying on a local-development default.

---

## Production environment variables

A production environment might provide:

```dotenv
DJANGO_SECRET_KEY=very-long-production-secret

DJANGO_ALLOWED_HOSTS=example.com,www.example.com

DJANGO_CSRF_TRUSTED_ORIGINS=https://example.com,https://www.example.com

DATABASE_URL=postgresql://app_user:password@postgres:5432/app

EMAIL_HOST=smtp.example.com
EMAIL_PORT=587
EMAIL_USE_TLS=True
EMAIL_HOST_USER=notifications@example.com
EMAIL_HOST_PASSWORD=production-email-password
DEFAULT_FROM_EMAIL=notifications@example.com
```

These values should normally be supplied by the deployment platform, container runtime, CI/CD system, or secret manager.

Production should not depend on a committed `.env` file.

---

## Database configuration

`django-environ` is particularly useful for database URLs.

Instead of maintaining:

```dotenv
DB_NAME=myproject
DB_USER=myproject
DB_PASSWORD=secret
DB_HOST=localhost
DB_PORT=5432
```

you can use:

```dotenv
DATABASE_URL=postgresql://myproject:secret@localhost:5432/myproject
```

and:

```python
DATABASES = {
    "default": env.db("DATABASE_URL")
}
```

This is especially convenient for production deployments.

Local development can still use SQLite:

```python
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.sqlite3",
        "NAME": BASE_DIR / "db.sqlite3",
    }
}
```

---

## Email configuration

Production:

```dotenv
EMAIL_HOST=smtp.example.com
EMAIL_PORT=587
EMAIL_USE_TLS=True
EMAIL_HOST_USER=notifications@example.com
EMAIL_HOST_PASSWORD=...
DEFAULT_FROM_EMAIL=notifications@example.com
```

Settings:

```python
EMAIL_BACKEND = "django.core.mail.backends.smtp.EmailBackend"

EMAIL_HOST = env("EMAIL_HOST")

EMAIL_PORT = env.int("EMAIL_PORT", default=587)

EMAIL_USE_TLS = env.bool("EMAIL_USE_TLS", default=True)

EMAIL_HOST_USER = env("EMAIL_HOST_USER")

EMAIL_HOST_PASSWORD = env("EMAIL_HOST_PASSWORD")

DEFAULT_FROM_EMAIL = env("DEFAULT_FROM_EMAIL")
```

Local:

```python
EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"
```

This means local development prints emails to the terminal instead of accidentally sending real messages.

---

## What belongs in environment variables?

| Configuration | Python settings | Environment variable |
|---|:---:|:---:|
| `INSTALLED_APPS` | ✅ | ❌ |
| Middleware | ✅ | ❌ |
| URL configuration | ✅ | ❌ |
| `TIME_ZONE` | ✅ | Usually ❌ |
| `USE_TZ` | ✅ | ❌ |
| `DEBUG` | Environment-specific | Sometimes |
| `SECRET_KEY` | ❌ | ✅ |
| `ALLOWED_HOSTS` | ❌ | ✅ |
| `DATABASE_URL` | ❌ | ✅ |
| Database password | ❌ | ✅ |
| Email host | ❌ | Usually ✅ |
| Email password | ❌ | ✅ |
| API keys | ❌ | ✅ |
| OAuth secrets | ❌ | ✅ |
| Static paths | ✅ | Usually ❌ |
| Development email backend | ✅ | ❌ |
| Production HTTPS settings | ✅ | ❌ |
| Feature flags | Either | Sometimes |

The principle is:

> **Do not use environment variables simply because they are available. Use them where they provide a real configuration or security benefit.**

---

## Required vs optional configuration

For required production configuration:

```python
SECRET_KEY = env("DJANGO_SECRET_KEY")
```

```python
DATABASES = {
    "default": env.db("DATABASE_URL")
}
```

```python
ALLOWED_HOSTS = env.list("DJANGO_ALLOWED_HOSTS")
```

If they are missing, the application should fail during startup.

For optional configuration:

```python
EMAIL_PORT = env.int(
    "EMAIL_PORT",
    default=587,
)
```

This distinction prevents production from silently running with unsafe defaults.

---

## What not to do

### Do not put everything in `.env`

Avoid turning ordinary configuration into environment variables:

```dotenv
LANGUAGE_CODE=en-gb
USE_TZ=True
DEFAULT_AUTO_FIELD=django.db.models.BigAutoField
STATIC_URL=/static/
```

There is little benefit.

Keep these in Python:

```python
LANGUAGE_CODE = "en-gb"

USE_TZ = True

DEFAULT_AUTO_FIELD = "django.db.models.BigAutoField"

STATIC_URL = "/static/"
```

---

### Do not put production secrets in `base.py`

Avoid:

```python
SECRET_KEY = "actual-production-secret"
```

Use:

```python
SECRET_KEY = env("DJANGO_SECRET_KEY")
```

---

### Do not duplicate settings

Avoid:

```python
# local.py
TIME_ZONE = "Europe/London"
USE_TZ = True
```

and:

```python
# production.py
TIME_ZONE = "Europe/London"
USE_TZ = True
```

Put shared configuration in `base.py`:

```python
TIME_ZONE = "Europe/London"

USE_TZ = True
```

---

### Do not use complicated environment branching

Avoid building a single settings file full of conditions:

```python
if os.environ.get("ENVIRONMENT") == "production":
    ...
elif os.environ.get("ENVIRONMENT") == "local":
    ...
```

when you already have:

```text
base.py
local.py
production.py
test.py
```

The settings module itself provides the separation.

---

## Recommended architecture

The overall configuration flow is:

```text
                       Application
                            │
                            ▼
                     Django settings
                            │
             ┌──────────────┴──────────────┐
             │                             │
             ▼                             ▼
          base.py                  Environment-specific
                                   settings module
                                      │
                    ┌─────────────────┼─────────────────┐
                    ▼                 ▼                 ▼
                 local.py        production.py       test.py
                    │                 │                 │
                    ▼                 ▼                 ▼
                  Local           Production          Tests
```

Environment variables are a separate concern:

```text
       .env / deployment environment
                    │
                    ▼
             django-environ
                    │
                    ▼
             env("SETTING")
                    │
                    ▼
             Django settings
```

Together:

```text
                         Django
                           │
                    settings package
                           │
                 ┌─────────┴─────────┐
                 │                   │
              base.py           Environment
                 │                 settings
                 │                   │
                 │       ┌───────────┼───────────┐
                 │       ▼           ▼           ▼
                 │     local     production     test
                 │
                 └───────────┬───────────────┐
                             │               │
                             ▼               ▼
                       shared config    django-environ
                                             │
                                             ▼
                                      environment values
```

---

## Quick reference

### Install

```bash
uv add django-environ
```

### Local environment

```bash
cp .env.example .env
```

### Run locally

```bash
DJANGO_SETTINGS_MODULE=config.settings.local \
uv run python manage.py runserver
```

### Run migrations

```bash
uv run python manage.py migrate
```

### Production

```text
DJANGO_SETTINGS_MODULE=config.settings.production
```

with production environment variables supplied by the deployment environment.

### Test

```text
DJANGO_SETTINGS_MODULE=config.settings.test
```

---

## Rules of thumb

1. **Use `base.py` for genuinely shared settings.**
2. **Use `local.py` for local-development differences.**
3. **Use `production.py` for production differences and hardening.**
4. **Use `test.py` for test-specific behavior.**
5. **Use `django-environ` to read and cast environment variables.**
6. **Use `.env` for local development only.**
7. **Commit `.env.example`, never `.env`.**
8. **Production secrets should come from the deployment environment or secret manager.**
9. **Required production configuration should fail fast.**
10. **Don't turn every Django setting into an environment variable.**
11. **Don't duplicate shared settings between environment files.**
12. **Use `uv add` to manage the `django-environ` dependency.**
13. **Use `uv run` to execute Django commands.**
14. **Keep the settings package boring, explicit, and easy to understand.**

> **The goal is not to eliminate configuration from Python. The goal is to separate application configuration from environment-specific values while keeping the settings system simple.**