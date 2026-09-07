# Gitlab & YAML

## 1.About YAML

- YAML is a human-readable data serialization language commonly used for writing configuration files and data exchange between systems. It is designed to be easy to read and understand, using indentation to indicate structure instead of symbols like brackets or braces.

- YAML itself does not define meaning. It only defines structure.

    - YAML = the grammar (how data is written)
    
    - Docker Compose / GitLab CI = the language specification (what specific keys mean)

    - The “intelligence” comes from the application reading the YAML, not from YAML itself.

## 2.How it works

- All applications have a predefined (standardized) schema model.

- Each system defines its own:

    - required keys 
    
    - optional keys
    
    - nesting rules
    
    - allowed value types
    
    - validation rules 
    
    - This is basically the file’s schema.

 - Examples:

    - Docker Compose schema

    - GitLab CI schema

    - Kubernetes manifest schema

    - Ansible playbook schema

- Same YAML syntax → different meanings.
- There are standardized/conventional key-value pairs, but they are standardized by the application (Docker, GitLab, Kubernetes), not by YAML itself.

## 3.Syntax Example

```yaml
# YAML Example: Musical Instruments Store

store:
  name: Harmony House
  location: London
  open: true
  established: 1998
  rating: 4.8

owner:
  first_name: Sarah
  last_name: Bennett
  active_member: true

inventory:
  guitars:
    - brand: Fender
      model: Stratocaster
      type: electric
      price: 1200
      in_stock: true
      colors:
        - black
        - sunburst
        - white

    - brand: Yamaha
      model: F310
      type: acoustic
      price: 180
      in_stock: false
      colors:
        - natural

  pianos:
    - brand: Yamaha
      model: U1
      type: upright
      price: 8500
      in_stock: true

    - brand: Roland
      model: FP-30X
      type: digital
      price: 750
      in_stock: true

services:
  lessons_available: true
  repair_service: true
  rental_options:
    - daily
    - weekly
    - monthly

contact:
  phone: "+44-20-1234-5678"
  email: info@harmonyhouse.com
  website: www.harmonyhouse.com
```

## 4.YAML File

### 1.Example:

```yaml
# YAML Example with Explanation

# -----------------------------------
# 1. OBJECT (also called a mapping)
# -----------------------------------
# An object is a group of key-value pairs.
# Think of it like a dictionary or JSON object.

store:
  name: Harmony House
  location: London
  open: true
  established: 1998

# Here:
# "store" is an object
# Inside it:
# - name
# - location
# - open
# - established
# are keys

# -----------------------------------
# 2. KEY-VALUE PAIR
# -----------------------------------
# Basic YAML structure:
# key: value

owner_name: Sarah
country: UK

# Examples:
# owner_name = key
# Sarah = value

# -----------------------------------
# 3. STRING
# -----------------------------------
# Text values are strings

instrument: guitar
brand: Yamaha

# "guitar" and "Yamaha" are strings

# -----------------------------------
# 4. NUMBER
# -----------------------------------
# Integers or decimals

price: 799
rating: 4.8

# 799 = integer
# 4.8 = decimal

# -----------------------------------
# 5. BOOLEAN
# -----------------------------------
# true or false values

in_stock: true
used_item: false

# YAML booleans:
# true
# false

# -----------------------------------
# 6. LIST (also called an array)
# -----------------------------------
# Lists use hyphens (-)

colors:
  - black
  - white
  - red

# "colors" is a list of strings

# Equivalent idea:
# colors = [black, white, red]

# -----------------------------------
# 7. LIST OF OBJECTS
# -----------------------------------
# Very common in Docker, GitLab, Kubernetes

guitars:
  - brand: Fender
    model: Stratocaster
    price: 1200

  - brand: Gibson
    model: Les Paul
    price: 2500

# "guitars" is a list
# each item inside the list is an object

# -----------------------------------
# 8. NESTED OBJECTS
# -----------------------------------
# Objects inside objects

contact:
  phone:
    country_code: "+44"
    number: "123456789"

# "contact" is an object
# "phone" is another object inside it

# -----------------------------------
# 9. NULL VALUE
# -----------------------------------
# Empty / no value

discount_price: null

# Can also be:

backup_contact:

# both mean "no value"

# -----------------------------------
# 10. COMMENTS
# -----------------------------------
# Comments start with #

# This is ignored by YAML

# -----------------------------------
# 11. IMPORTANT: INDENTATION
# -----------------------------------
# Spaces define structure
# indentation matters a lot

service:
  name: Guitar Repair
  available: true

# Correct

# Wrong example (This would break YAML):
# service:
# name: Guitar Repair
# available: true

```

### 2.Docker-Compose:

#### 2.1.Docker-compose

```yaml
version: "3.9"

services:
  web:
    image: nginx
    ports:
      - "80:80"
    environment:
      - NODE_ENV=production

  db:
    image: postgres
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```


#### 2.2.Docker & Django

- File: `docker-compose.yml`

```yaml
# Example of Docker-Compose with Django 

version: "3.9"

services:

  django:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: django_app

    command: >
      gunicorn config.wsgi:application
      --bind 0.0.0.0:8000
      --workers 4
      --timeout 120

    volumes:
      - static_volume:/app/staticfiles
      - media_volume:/app/media

    env_file:
      - .env

    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_started

    expose:
      - 8000

    restart: unless-stopped



  nginx:
    image: nginx:alpine
    container_name: nginx

    ports:
      - "80:80"
      - "443:443"

    volumes:
      - ./nginx:/etc/nginx/conf.d
      - static_volume:/app/staticfiles
      - media_volume:/app/media

    depends_on:
      - django

    restart: unless-stopped



  db:
    image: postgres:16-alpine
    container_name: postgres

    volumes:
      - postgres_data:/var/lib/postgresql/data

    env_file:
      - .env

    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

    restart: unless-stopped



  redis:
    image: redis:7-alpine
    container_name: redis

    restart: unless-stopped



  celery:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: celery_worker

    command: celery -A config worker -l info

    env_file:
      - .env

    depends_on:
      - db
      - redis

    restart: unless-stopped



  celery-beat:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: celery_beat

    command: celery -A config beat -l info

    env_file:
      - .env

    depends_on:
      - db
      - redis

    restart: unless-stopped



volumes:
  postgres_data:
  static_volume:
  media_volume:

```

### 3.Gitlab:

#### 3.0. Gitlab Notes

- GitLab is an all-in-one DevOps platform that manages code, builds Docker images, runs CI/CD (Continous Integration and Development) pipelines, stores artifacts, and automates deployment from a single repository.

- GitLab is the control plane that converts code changes into tested, built, versioned Docker images and deployed applications through automated pipelines.

- Staging is where code proves itself no need for a separate beta branch in git.

- GitLab can trigger deployments to:

    - VPS servers
    
    - Render
    
    - AWS
    
    - Kubernetes
    
    - Docker hosts

    It does this via:
    
    - CI pipelines
    
    - SSH scripts
    
    - deploy hooks

- Key conceptual model:

| Layer | Role | What GitLab does |
|------|------|------------------|
| Repository System | Stores and manages source code | Hosts Git repos, branches, merge requests, permissions |
| Pipeline System | Automates workflows | Runs CI/CD jobs via `.gitlab-ci.yml` (tests, builds, migrations, etc.) |
| Artifact System | Stores build outputs | Stores Docker images in the Container Registry (versioned builds) |
| Deployment System | Releases software to environments | Triggers deployments to staging/production via pipelines or hooks |

- Centralized System of Tools:

| Function           | Separate Tool            |
| ------------------ | ------------------------ |
| Git hosting        | GitHub                   |
| CI/CD              | GitHub Actions / Jenkins |
| Docker registry    | Docker Hub               |
| Deployment scripts | custom VPS scripts       |


#### 3.1. Gitlab Example
```yaml
stages:
  - build
  - test
  - deploy

build-job:
  stage: build
  script:
    - npm install
    - npm run build

test-job:
  stage: test
  script:
    - npm test
```

#### 3.2. Gitlab-CI & Django

- File: `.gitlab-ci.yml`

```yaml

stages:
  - lint
  - test
  - build
  - deploy


variables:
  POSTGRES_DB: test_db
  POSTGRES_USER: postgres
  POSTGRES_PASSWORD: postgres
  POSTGRES_HOST: postgres
  POSTGRES_PORT: 5432

  REDIS_URL: redis://redis:6379/0

  DATABASE_URL: postgres://postgres:postgres@postgres:5432/test_db

  DJANGO_SETTINGS_MODULE: config.settings.ci

  DOCKER_DRIVER: overlay2
  DOCKER_TLS_CERTDIR: ""


default:
  image: python:3.12

  before_script:
    - python --version
    - pip install --upgrade pip
    - pip install -r requirements.txt



lint:
  stage: lint

  script:
    - flake8 .
    - black --check .
    - isort --check-only .



test:
  stage: test

  services:
    - postgres:16
    - redis:7

  script:
    - python manage.py migrate
    - python manage.py test

  artifacts:
    when: always
    expire_in: 7 days



build:
  stage: build

  image: docker:latest

  services:
    - docker:dind

  before_script:
    - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" "$CI_REGISTRY"

  script:
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA

  only:
    - main



deploy_production:
  stage: deploy

  image: alpine:latest

  before_script:
    - apk add --no-cache openssh-client
    - mkdir -p ~/.ssh
    - echo "$SSH_PRIVATE_KEY" > ~/.ssh/id_rsa
    - chmod 600 ~/.ssh/id_rsa

  script:
    - |
      ssh -o StrictHostKeyChecking=no deploy@your-server-ip "
        cd /srv/django-app &&
        docker compose pull &&
        docker compose up -d --remove-orphans &&
        docker image prune -f
      "

  only:
    - main

  environment:
    name: production
    url: https://example.com

```

#### 3.3. What each section does

| Section             |                     Purpose | What Happens                                                              |
| ------------------- | --------------------------: | ------------------------------------------------------------------------- |
| `stages`            |              Pipeline order | Defines execution order like `lint → test → build → deploy`               |
| `variables`         |       Environment variables | Shared values available to all jobs (`DATABASE_URL`, `POSTGRES_DB`, etc.) |
| `default`           |        Shared configuration | Default Docker image and shared setup reused by all jobs                  |
| `before_script`     |               Pre-job setup | Runs before each job (install dependencies, setup environment)            |
| `lint`              |          Code quality check | Runs tools like `flake8`, `black`, `isort`                                |
| `test`              |         Run automated tests | Runs migrations and Django test suite                                     |
| `services`          | Temporary helper containers | Starts PostgreSQL / Redis for tests                                       |
| `artifacts`         |        Save files from jobs | Stores reports, logs, coverage output                                     |
| `build`             |       Build container image | Creates Docker image for the application                                  |
| `docker:dind`       |            Docker-in-Docker | Lets GitLab Runner execute Docker commands                                |
| `deploy_production` |       Production deployment | Connects to server via SSH and updates running containers                 |

#### 3.4. Typical flow after pushing code

| Step | Stage          | What GitLab Does                                    |
| ---: | -------------- | --------------------------------------------------- |
|    1 | Developer Push | `git push origin main`                              |
|    2 | `lint`         | Checks formatting, imports, code style              |
|    3 | `test`         | Starts PostgreSQL + Redis and runs Django tests     |
|    4 | `build`        | Builds Docker image from `Dockerfile`               |
|    5 | Push Image     | Uploads image to GitLab Container Registry          |
|    6 | `deploy`       | SSH into production server                          |
|    7 | Server Update  | Runs `docker compose pull`                          |
|    8 | Restart App    | Runs `docker compose up -d`                         |
|    9 | Live Release   | Updated Django version is now running in production |


#### 3.5 Gitlab-CI Logic

```bash
main branch push
   ↓
deploy to staging automatically

manual approval
   ↓
deploy to production
```

#### 3.6. Flowchart

??? note "Mental Model"

    - Code → Quality Check → Tests → Build Image → Push → Deploy → Live App

```mermaid
flowchart TD

A[Developer Pushes Code<br/>git push origin main] --> B[GitLab CI Pipeline Starts]

B --> C[Lint Stage]
C --> C1[flake8 / black / isort<br/>Code quality checks]

C --> D[Test Stage]
D --> D1[Start PostgreSQL + Redis services]
D1 --> D2[Run Django migrations]
D2 --> D3[Run Django test suite]

D --> E{Tests Passed?}
E -- No --> X[Pipeline Failed ❌]
E -- Yes --> F[Build Stage]

F --> F1[Build Docker Image<br/>from Dockerfile]
F1 --> F2[Push Image to Container Registry]

F2 --> G[Deploy Stage]

G --> G1[SSH into Production Server]
G1 --> G2[docker compose pull]
G2 --> G3[docker compose up -d]
G3 --> G4[Prune old images]

G4 --> H[Production Updated ✅]

H --> I[Users Access Live Django App]
```