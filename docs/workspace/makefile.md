# Makefile

## 1.Definition of a Makefile

A Makefile is a special file used by the make build automation tool to control the build process of a project, specifying how to compile and link a program from source files. It contains a set of rules and commands that automate the compilation process, making it more efficient and reducing errors.

- Key Features of a Makefile
        
    - Structure:
            
        - Rules: Each rule specifies a target, its prerequisites, and the commands to execute.
            
        - Dependencies: Makefiles track which files need to be updated based on changes, ensuring only necessary files are recompiled.

        - Makefile requires TAB not spaces when creating commands.
        
    - Components:
            
        - Targets: The output files or actions to be performed.
            
        - Prerequisites: The files that must be present or updated before the target can be built.
            
        - Commands: The shell commands that are executed to create or update the target.

- Benefits of Makefile:

| Benefit            |                                      Explanation |
| ------------------ | -----------------------------------------------: |
| Shorter commands   |   `make migrate` instead of long Docker commands |
| Team consistency   |          Everyone runs the same project commands |
| Version-controlled | Lives inside repo and shared with all developers |
| Easier onboarding  |      New developer can run `make up` immediately |
| Less mistakes      |                   No retyping long Docker syntax |
| Environment-aware  |            Can switch between dev / stage / prod |

## 2.Installing `make` on Debian / Ubuntu

- Installation:

```bash
sudo apt update
sudo apt install make
```

- Check Version and Verify:

```bash
make --version
```

- Project Structure Example:

```bash
myproject/
├── Makefile
├── docker-compose.yml
├── .env.dev
├── .env.stage
├── .env.prod
├── manage.py
├── requirements.txt
└── apps/
```

## 3.Makefile Example

- Create a `Makefile` file in the project root directory.

```makefile
up:
	docker compose --env-file .env.dev up --build

down:
	docker compose down

makemigrations:
	docker compose exec web python manage.py makemigrations

migrate:
	docker compose exec web python manage.py migrate

superuser:
	docker compose exec web python manage.py createsuperuser

shell:
	docker compose exec web python manage.py shell

test:
	docker compose exec web python manage.py test

logs:
	docker compose logs -f
```

- Then run any of the commands in the terminal with `make <command_name>`.

- Example `make migrate`.

## 4.Makefile workflow

```makefile
# Default environment
ENV ?= .env.dev

# Docker compose command
DC = docker compose --env-file $(ENV)

# -----------------------------
# Containers
# -----------------------------

up:
	$(DC) up --build

down:
	$(DC) down

restart:
	$(DC) down && $(DC) up --build

logs:
	$(DC) logs -f

ps:
	$(DC) ps

# -----------------------------
# Django management
# -----------------------------

makemigrations:
	$(DC) exec web python manage.py makemigrations

migrate:
	$(DC) exec web python manage.py migrate

shell:
	$(DC) exec web python manage.py shell

superuser:
	$(DC) exec web python manage.py createsuperuser

collectstatic:
	$(DC) exec web python manage.py collectstatic --noinput

test:
	$(DC) exec web python manage.py test

# -----------------------------
# Utility
# -----------------------------

bash:
	$(DC) exec web bash

build:
	$(DC) build

rebuild:
	$(DC) up --build --force-recreate
```

## 5.Environment versioning

- Handling Multiple Environment Files:

```bash
.env.dev
.env.stage
.env.prod
```

- Default behavior:

```bash
make up
```
Uses development environment because, the `.env.dev` is defined in `Makefile` as `ENV ?= .env.dev` .

- Run staging:

```bash
make ENV=.env.stage up
```

```bash
make ENV=.env.stage migrate
```