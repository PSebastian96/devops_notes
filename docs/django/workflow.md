## Workflow

## CLI Commands

### 1. Starting a Django Project

```bash
django-admin startproject project_name

```

> Usage: This command creates a new Django project with the specified project_name. It generates the project directory structure and necessary configuration files.

### 2. Starting a New Django App

```bash
python manage.py startapp app_name

```
> Usage: This creates a new app within your project. Apps are individual components that make up a Django project (e.g., a blog app, a user app).

### 3. Running the Development Server

```bash
python manage.py runserver

```
> Usage: Starts the built-in Django development server. By default, it runs on http://127.0.0.1:8000/.
>
>You can specify a different port by appending the port number (e.g., python manage.py runserver8080).

### 4. Creating Migrations

#### 4.1 Project wide migration
```bash
python manage.py makemigrations
```
> Usage: Generates migration files based on the changes made to the models. Migrations are Django’s way of propagating model changes to the database schema.

#### 4.2 Creating Migrations for a Specific App

```bash
python manage.py makemigrations <app_name>
```
> Generates migration files only for the specified app instead of all apps. Useful when you want to isolate changes or avoid generating migrations for unrelated apps.

### 5. Applying Migrations

#### 5.1 Show planned migrations

```bash
python manage.py migrate --plan
```
> Shows all migrations within a project.

#### 5.2 Apply migrations

```bash
python manage.py migrate

```
> Usage: Applies all the migrations to the database. It syncs the database schema with the current state of your models.

#### 5.3 Check migrations

```bash
python manage.py showmigrations
```
> All migrations.

#### 5.4 Show planned App Migrations

```bash
python manage.py migrate <app_name> --plan
```
> Shows migrations that need to be applied within an app.

#### 5.5 Applying Migrations for a Specific App

```bash
python manage.py migrate <app_name>
```
> Applies only the migrations of the specified app to the database.

#### 5.6 Checking Migrations for a Specific App

```bash
python manage.py showmigrations <app_name>
```
> Shows which migrations have been applied for that particular app.

### 6. Creating a Superuser

```bash
python manage.py createsuperuser

```
> Usage: Prompts for a username, email, and password to create an admin user that can access the Django admin interface.

### 7. Collecting Static Files

```bash
python manage.py collectstatic

```

> Usage: Collects all static files from your apps and dependencies and moves them to the static root directory defined in your settings. Useful for deploying projects in production.

### 8. Database Shell

```bash
python manage.py dbshell

```

> Usage: Opens the database shell, allowing you to run raw SQL commands directly on your database.

### 9. Django Shell

```bash
python manage.py shell

```

> Usage: Opens an interactive Python shell with the Django environment loaded, allowing you to interact with your models and the database.

### 10. Testing

```bash
python manage.py test

```

> Usage: Runs the test suite for your project, executing all tests defined within your apps.

### 11. Clearing Migrations

```bash
python manage.py flush

```
> Deletes all data from the database tables and returns the database to the initial state (empty). It does not delete the migration files.

### 12. Checking for Issues

```bash
python manage.py check

```

> Usage: Runs system checks for your project to identify potential problems like incorrect configurations or improper model definitions.

### 13. Showing URLS's

```bash
python manage.py show_urls

```

> Usage: Displays a list of all the available URL patterns in your project, including their names and views.

### 14. Running Custom Commands

```bash
python manage.py custom_command

```

> Usage: You can write custom management commands by creating Python scripts inside the management/commands directory of an app. These commands can be executed using the manage.py script.

### 15. Dumping Data

```bash
python manage.py dumpdata [app_label] [model_name]

```

> Usage: Outputs the content of the database as JSON, XML, or YAML. You can specify a particular app or model to dump its data.

### 16. Loading Data

```bash
python manage.py loaddata file_name.json

```

> Usage: Loads data into the database from a fixture file. The file should be in a supported format like JSON, XML, or YAML.

### 17. Starting a Custom Command

```bash
python manage.py your_custom_command

```

> Usage: After defining a custom command under an app’s management/commands/ directory, this runs the custom logic defined there.

### Example CLI Workflow:

- Create a new project: `django-admin startproject myproject`
- Create a new app: `python manage.py startapp myapp`
- Create migrations after changing models: `python manage.py makemigrations`
- Apply migrations: `python manage.py migrate`
- Run the server: `python manage.py runserver`
- Create superuser: `python manage.py createsuperuser`
- Run tests: `python manage.py test`