#  Packages

### Dependency Installing:

1. Activate virtual environment
2. Install dependencies
3. Register the dependency in **`settings.py`**
4. Import library/dependency into the py files.

!!! warning  "Migrations & Dependencies"

    Migrations are needed if the dependency influences database models or tables.
    Example django-allauth, django-guarding, django-simple-history.

### Project Setup

- Cookiecutter (free/open source):

**Official repo**  
https://github.com/cookiecutter/cookiecutter-django

**Documentation**  
https://cookiecutter-django.readthedocs.io/

```bash
pip install cookiecutter-django
```

!!! abstract "Cookiecutter Notes"

    - Modern project layout (apps in flat structure or `{{cookiecutter.project_slug}}/` style)
    - Settings split: base / local / production / test
    - Custom user model (email as username option available)
    - django-allauth included (social + email login)
    - Docker + docker-compose ready (PostgreSQL, Redis, Celery optional)
    - pytest + coverage configured
    - pre-commit hooks (black, ruff, isort, flake8…)
    - GitHub Actions CI workflows
    - Whitenoise / S3 static files support
    - Security headers, logging, 12-factor friendly config
    - Choice of frontend stacks: Tailwind, HTMX, Alpine.js, React/Vue islands, Bootstrap…

!!! danger "Notes on `.venv` file"
    
    After generation – common first steps create `.env` and keep `.venv.example` .

    Fill `.venv` with secrets.

    Only share `.venv.example` with other devepores, in order to access secret values (never share the actual values!, just the variable).

- Saas Pegasus (paid/closed source):

!!! info "About Pegasus"

    Created by Cory Zue (indie hacker / former CTO).
    It uses Cookiecutter under the hood (as confirmed in their own docs and interviews) to generate the project.
    But it's heavily specialized for SaaS / subscription-based products:  Built-in Stripe subscriptions & billing. 
    
    User + team management (multi-tenancy basics)  
    
    Secure auth flows, invitations, roles  
    
    Modern JS options (HTMX, Alpine.js, React/Vue islands, Tailwind)  
    
    Background tasks (Celery), async support (Channels demo)  
    
    Online configurator (web-based wizard) instead of command-line prompts  
    
    Example apps / patterns for common SaaS features  
    
    Guides + support focused on launching paid products fast
    
    You buy a license (one-time or subscription tiers), configure via their site, download the generated code, and own it forever (no royalties).




### Authentication & Authorization:

- django-allauth:
```bash 
pip install django-allauth
```

- django-axes: 
```bash 
pip install django-axes
```

- django-guardian: 
```bash 
pip install django-guardian
```

- django-rest-framework-simplejwt: 
```bash 
pip install djangorestframework-simplejwt
```

### Forms:

- django-crispy-forms: 
```bash 
pip install django-crispy-forms
```

- django-widget-tweaks: 
```bash 
pip install django-widget-tweaks
```

- django-formtools: 
```bash 
pip install django-formtools
```

### Filter & Query:

- django-filter:
```bash 
pip install django-filter
```

- django-searchable-select:
```bash 
pip install django-searchable-select
```

- django-queryset-csv:
```bash 
pip install django-queryset-csv
```

### Text Search:

- django-haystack:
```bash 
pip install django-haystack
```

- django-watson:
```bash
pip install django-watson
```

- elasticsearch-dsl-django:
```bash 
pip install django-elasticsearch-dsl
```

### Admin:

- django-unfold:
```bash 
pip install django-unfold
```

- django-import-export:
```bash 
pip install django-import-export
```

- django-admin-rangefilter:
```bash 
pip install django-admin-rangefilter
```

### Payments:

- django-payments: 
```bash 
pip install django-payments
```
```bash 
pip install django-payments[stripe]
```

- django-stripe-payments: 
```bash 
pip install stripe
```

### Emails:

- django-anymail: 
```bash 
pip install "django-anymail[amazon-ses,mailtrap]"
```

- django-notifications: 
```bash 
pip install django-notifications-hq
```

- django-templated-email: 
```bash 
pip install django-templated-email
```

### File Management:

- django-storages: 
```bash 
pip install django-storages
```

- pillow (image files):
```bash
pip install pillow
```

- sorl-thumbnail: 
```bash 
pip install sorl-thumbnail
```

- django-cleanup: 
```bash 
pip install django-cleanup
```

### Performance & Caching:

- django-redis:
```bash
pip install django-redis
```

- django-celery:
```bash
pip install django-celery
```

- django-cachalot:
```bash
pip install django-cachalot
```

- django-debug-toolbar:
```bash
pip install django-debug-toolbar
```

### Productivity & Versioning:

- django-decouple:
```bash
pip install python-decouple
```

- django-environ:
```bash
pip install django-environ
```

- django-simple-history:
```bash
pip install django-simple-history
```

- django-polymorphic:
```bash
pip install django-polymorphic
```

- django-extensions:
```bash
pip install django-extensions
```

- django-model-utils:
```bash
pip install django-model-utils
```