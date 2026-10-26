# 'tasks.py'

## Overview

### Celery

Celery is an asynchronous, distributed task queue used in Django to execute heavy or time-consuming operations in the background, outside the standard HTTP request-response cycle.

---

- How Celery Works (The Producer-Consumer Model)

Celery splits your infrastructure into three main components:

1.The Producer (Django): The web application handles the user's web request. Instead of performing the heavy task directly, it passes a message to the queue.

2.The Message Broker (Redis or RabbitMQ): A separate database/service that stores the list of tasks (the queue) until a worker is ready to handle them.

3.The Consumer (Celery Worker): A separate Python process running in the background that constantly watches the broker, picks up tasks, and executes them.

---

Common Use Cases

• Sending Emails: Handing off email generation to Celery ensures that users don't experience a 2–3 second loading delay when registering or resetting passwords.

• Data Processing & Reports: Generating large Excel/CSV exports, compressing images, or processing files.

• Third-Party API Calls: Interfacing with external services that might be slow or temporarily down.

• Scheduled Tasks (Celery Beat): Running regular maintenance jobs, like clearing out expired user sessions every midnight or generating daily analytics.

---

## Config Reference

| Setting                                     | Common value / default              | Required? | What it is used for                                                                                                                                   |
| ------------------------------------------- | ----------------------------------- | --------: | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| `CELERY_BROKER_URL`                         | —                                   |   **Yes** | URL of the message broker Celery uses to send/receive task messages. Commonly `redis://localhost:6379/0` or `amqp://...` for RabbitMQ.                |
| `CELERY_RESULT_BACKEND`                     | `None`                              |   Usually | Where Celery stores task results/statuses. Commonly `redis://localhost:6379/1`, a database backend, etc. Not required if you don't need task results. |
| `CELERY_ACCEPT_CONTENT`                     | `{'json'}`                          |        No | Content serialization formats Celery will accept.                                                                                                     |
| `CELERY_TASK_SERIALIZER`                    | `'json'`                            |        No | Serialization format used when sending task arguments. JSON is the normal choice.                                                                     |
| `CELERY_RESULT_SERIALIZER`                  | `'json'`                            |        No | Serialization format used when storing task results.                                                                                                  |
| `CELERY_TIMEZONE`                           | `'UTC'`                             |        No | Timezone Celery uses for task scheduling and ETA/countdown calculations.                                                                              |
| `CELERY_ENABLE_UTC`                         | `True`                              |        No | Makes Celery use UTC internally. Generally leave this enabled.                                                                                        |
| `CELERY_TASK_TRACK_STARTED`                 | `False`                             |        No | If `True`, tasks get a `STARTED` state in addition to states such as `PENDING`, `SUCCESS`, and `FAILURE`.                                             |
| `CELERY_TASK_TIME_LIMIT`                    | `None`                              |        No | Hard maximum execution time for a task, in seconds. Celery terminates a task exceeding this limit.                                                    |
| `CELERY_TASK_SOFT_TIME_LIMIT`               | `None`                              |        No | Soft execution limit. Raises `SoftTimeLimitExceeded`, allowing the task to clean up before termination.                                               |
| `CELERY_TASK_ACKS_LATE`                     | `False`                             |        No | If `True`, acknowledge a task after it finishes rather than immediately after receiving it. Useful for retry/reliability scenarios.                   |
| `CELERY_TASK_REJECT_ON_WORKER_LOST`         | `False`                             |        No | If enabled with late acknowledgements, tasks can be requeued when a worker unexpectedly dies.                                                         |
| `CELERY_WORKER_PREFETCH_MULTIPLIER`         | `4`                                 |        No | Number of tasks a worker can reserve ahead of time relative to its concurrency. Lower values can improve fairness for long-running tasks.             |
| `CELERY_TASK_DEFAULT_QUEUE`                 | `'celery'`                          |        No | Default queue to which tasks are sent.                                                                                                                |
| `CELERY_TASK_DEFAULT_EXCHANGE`              | `'celery'`                          |        No | Default exchange used for tasks.                                                                                                                      |
| `CELERY_TASK_DEFAULT_ROUTING_KEY`           | `'celery'`                          |        No | Default routing key used for tasks.                                                                                                                   |
| `CELERY_TASK_ALWAYS_EAGER`                  | `False`                             |        No | Executes tasks synchronously in the Django process instead of sending them to a worker. Mostly useful for testing.                                    |
| `CELERY_TASK_EAGER_PROPAGATES`              | `False`                             |        No | When eager mode is enabled, propagates task exceptions back to the caller.                                                                            |
| `CELERY_BROKER_CONNECTION_RETRY_ON_STARTUP` | `True`                              |        No | Controls whether Celery retries connecting to the broker during worker startup.                                                                       |
| `CELERY_BROKER_CONNECTION_MAX_RETRIES`      | `100`                               |        No | Maximum number of broker connection retries.                                                                                                          |
| `CELERY_BROKER_CONNECTION_TIMEOUT`          | `4`                                 |        No | Timeout, in seconds, when establishing a broker connection.                                                                                           |
| `CELERY_RESULT_EXPIRES`                     | `86400` (1 day)                     |        No | How long task results remain in the result backend before they can be expired.                                                                        |
| `CELERY_TASK_IGNORE_RESULT`                 | `False`                             |        No | If `True`, successful task results aren't stored. Useful for fire-and-forget tasks.                                                                   |
| `CELERY_RESULT_EXTENDED`                    | `False`                             |        No | Stores additional metadata about tasks in the result backend.                                                                                         |
| `CELERY_WORKER_MAX_TASKS_PER_CHILD`         | `None`                              |        No | Restarts a worker child process after it has executed this many tasks. Useful for mitigating memory leaks.                                            |
| `CELERY_WORKER_MAX_MEMORY_PER_CHILD`        | `None`                              |        No | Restarts a worker child process when it exceeds this memory limit.                                                                                    |
| `CELERY_TASK_ROUTES`                        | `{}`                                |        No | Routes particular tasks to specific queues, exchanges, or routing keys.                                                                               |
| `CELERY_TASK_QUEUES`                        | Auto-created/default                |        No | Defines named queues and their exchanges/routing configuration.                                                                                       |
| `CELERY_BEAT_SCHEDULE`                      | `{}`                                |        No | Defines periodic/recurring tasks when using Celery Beat.                                                                                              |
| `CELERY_BEAT_SCHEDULER`                     | `'celery.beat.PersistentScheduler'` |        No | Scheduler implementation used by Celery Beat.                                                                                                         |
| `CELERY_BROKER_HEARTBEAT`                   | `120`                               |        No | Heartbeat interval used for AMQP broker connections.                                                                                                  |
| `CELERY_EVENT_QUEUE_EXPIRES`                | `60`                                |        No | How long Celery event queues remain unused before expiring. Mainly relevant to monitoring.                                                            |
| `CELERY_SEND_TASK_EVENTS`                   | `False`                             |        No | Enables sending task-related events, useful for monitoring tools such as Flower.                                                                      |
| `CELERY_WORKER_SEND_TASK_EVENTS`            | `False`                             |        No | Makes workers send task events for monitoring.                                                                                                        |
| `CELERYD_CONCURRENCY`                       | Number of CPU cores                 |        No | Number of concurrent worker processes. **Usually configured as a command-line option rather than a Django setting.**                                  |

---

- Celery config example:

```python
# Broker
CELERY_BROKER_URL = env("CELERY_BROKER_URL")

# Results — omit entirely if you don't need task results
CELERY_RESULT_BACKEND = env("CELERY_RESULT_BACKEND")

# Serialization
CELERY_ACCEPT_CONTENT = ["json"]
CELERY_TASK_SERIALIZER = "json"
CELERY_RESULT_SERIALIZER = "json"

# Time
CELERY_TIMEZONE = TIME_ZONE
CELERY_ENABLE_UTC = True

# Reliability
CELERY_TASK_ACKS_LATE = True
CELERY_TASK_REJECT_ON_WORKER_LOST = True

# Prevent tasks from running indefinitely
CELERY_TASK_SOFT_TIME_LIMIT = 300
CELERY_TASK_TIME_LIMIT = 360

# Worker behaviour
CELERY_WORKER_PREFETCH_MULTIPLIER = 1

# Results
CELERY_RESULT_EXPIRES = 3600
```

---

## Config steps

### Summary
1.Install dependencies

```python
pip install celery redis
```

2.Run Redis

Redis → broker
Redis → optional result backend

3.Add Celery settings to settings.py

```python
CELERY_BROKER_URL = "redis://localhost:6379/0"
CELERY_RESULT_BACKEND = "redis://localhost:6379/1"
```

4.Create `project/celery.py`

    * Creates the Celery application.
     
    * Loads Django settings.
     
    * Automatically discovers tasks.py files.
     
    * Import Celery from project/__init__.py
    
    * Ensures the Celery application is loaded when Django starts.

5.Create tasks in each Django app

`myapp/tasks.py`

6.Start a Celery worker

```bash
celery -A project worker --loglevel=INFO
```

!!! note "For production"

    Put broker/backend URLs in environment variables.

    Configure task acknowledgement/retry behaviour deliberately.

    Run workers and Beat as separate processes/containers.

---

- Separation is useful to remember:

| Concern                     | File                                  | Example                      |
| --------------------------- | ------------------------------------- | ---------------------------- |
| Django settings             | `myproject/settings.py`               | `CELERY_BROKER_URL`          |
| Celery initialization       | `myproject/celery.py`                 | `Celery(...)`                |
| Celery import               | `myproject/__init__.py`               | `from .celery import app`    |
| Application tasks           | `orders/tasks.py`                     | `@shared_task`               |
| Other application tasks     | `users/tasks.py`                      | `@shared_task`               |
| Periodic schedules          | `settings.py` or `django-celery-beat` | `CELERY_BEAT_SCHEDULE`       |
| Queue routing               | `settings.py`                         | `CELERY_TASK_ROUTES`         |
| Environment-specific values | `.env` / environment                  | Redis URL                    |
| Worker process              | process/container config              | `celery -A myproject worker` |
| Beat process                | process/container config              | `celery -A myproject beat`   |

>The key principle is:
>celery.py initializes Celery; settings.py configures Celery; tasks.py defines what Celery does.

!!! danger "Move connection details into environment variables"

    Don't hard-code production Redis credentials in `settings.py`.

    Create a `.env` file and add the celery credentials with the rest of thr environment variables.

    Instead of: `CELERY_BROKER_URL = "redis://localhost:6379/0"`

    use something like:

    ```python
    import os

    CELERY_BROKER_URL = os.environ["CELERY_BROKER_URL"]
    CELERY_RESULT_BACKEND = os.environ.get("CELERY_RESULT_BACKEND")
    
    # or use env package
    CELERY_BROKER_URL = env("CELERY_BROKER_URL")
    CELERY_RESULT_BACKEND = env("CELERY_RESULT_BACKEND")
    ```

    Then:

    ```python
    CELERY_BROKER_URL=redis://localhost:6379/0
    CELERY_RESULT_BACKEND=redis://localhost:6379/1
    ```

    For a production Redis instance, it might instead look like: `redis://:password@redis.example.com:6379/0`

    The exact URL depends on how Redis is deployed.

---

## 1.Project Structure

```text
myproject/
│
├── manage.py
│
├── myproject/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── asgi.py
│   ├── wsgi.py
│   └── celery.py
│
├── orders/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── views.py
│   ├── tasks.py
│   └── migrations/
│
├── users/
│   ├── __init__.py
│   ├── models.py
│   ├── tasks.py
│   └── ...
│
└── requirements.txt
```

- Important convention pattern:

```text
myproject/celery.py       ← Celery application/configuration
myproject/settings.py     ← Django/Celery settings
myproject/__init__.py     ← loads Celery application
orders/tasks.py           ← tasks belonging to orders
users/tasks.py            ← tasks belonging to users
```

---

## 2.Configure Django settings

```python
# Celery

CELERY_BROKER_URL = "redis://localhost:6379/0"
CELERY_RESULT_BACKEND = "redis://localhost:6379/1"

CELERY_ACCEPT_CONTENT = ["json"]
CELERY_TASK_SERIALIZER = "json"
CELERY_RESULT_SERIALIZER = "json"

CELERY_TIMEZONE = TIME_ZONE
CELERY_ENABLE_UTC = True
```

| Setting                    | Purpose                                |
| -------------------------- | -------------------------------------- |
| `CELERY_BROKER_URL`        | Where Celery gets/sends task messages  |
| `CELERY_RESULT_BACKEND`    | Where Celery stores task state/results |
| `CELERY_ACCEPT_CONTENT`    | Accepted message serialization formats |
| `CELERY_TASK_SERIALIZER`   | How task arguments are serialized      |
| `CELERY_RESULT_SERIALIZER` | How task results are serialized        |
| `CELERY_TIMEZONE`          | Timezone used by Celery                |
| `CELERY_ENABLE_UTC`        | Whether Celery operates using UTC      |

---

3. Create `celery.py`

```text
myproject/celery.py
```

```python
# celery.py

import os

from celery import Celery

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "myproject.settings")

app = Celery("myproject")

app.config_from_object(
    "django.conf:settings",
    namespace="CELERY",
)

app.autodiscover_tasks()
```

---

- This tells Celery where Django settings is and which Django settings module to load.

```python
os.environ.setdefault(
    "DJANGO_SETTINGS_MODULE",
    "myproject.settings",
)
```

---

Load Django's Celery configuration:

```python
app.config_from_object(
    "django.conf:settings",
    namespace="CELERY",
)
```
This is why your Django settings use: `CELERY_BROKER_URL` instead of: `broker_url`.

The `CELERY_` namespace keeps Celery configuration separate from other Django settings.

---

Discover tasks

```python
app.autodiscover_tasks()
```

This tells Celery to look through installed Django applications for: `tasks.py`.

Each app containing `tasks.py`:

```text
orders/tasks.py
users/tasks.py
payments/tasks.py
```

Celery can automatically discover them.

---

## 3. `__init__`

```text
myproject/
├── __init__.py       ← exposes Celery app
├── settings.py       ← Celery configuration
├── celery.py         ← Celery initialization
├── urls.py
├── asgi.py
└── wsgi.py
```

Register Celery in `__init__.py`

```python
from .celery import app as celery_app

__all__ = ("celery_app",)
```

---

## 4. Create a Task

Within an app, create `tasks.py` file.

```python
# orders/tasks.py

from celery import shared_task


@shared_task
def process_order(order_id):
    print(f"Processing order {order_id}")

    return f"Order {order_id} processed"
```

- `@shared_task` is the common convention for Django applications.

---

> Task-specific configuration belongs in `tasks.py`

```python
from celery import shared_task


@shared_task(
    bind=True,
    autoretry_for=(Exception,),
    retry_backoff=True,
    retry_kwargs={"max_retries": 5},
)
def fetch_external_data(self, record_id):
    ...
```

---

## 5. Call Tasks

```python
from orders.tasks import process_order

process_order.delay(order.id)
```

The important part is: `.delay()`.

Instead of executing: `process_order(order.id)` directly, Django sends a message to the broker:

```text
Django
   │
   │ process_order.delay(123)
   ▼
Redis
   │
   │ task message
   ▼
Celery Worker
   │
   │ process_order(123)
   ▼
Task executes
```

This is the fundamental purpose of Celery.

---

## Celery in Production
### `settings.py`

```python
# config/settings.py

import os

from pathlib import Path

from celery.schedules import crontab


BASE_DIR = Path(__file__).resolve().parent.parent


# ---------------------------------------------------------------------------
# Django
# ---------------------------------------------------------------------------

SECRET_KEY = os.environ["DJANGO_SECRET_KEY"]

DEBUG = os.environ.get("DJANGO_DEBUG", "False").lower() == "true"


# ---------------------------------------------------------------------------
# Celery - broker / result backend
# ---------------------------------------------------------------------------

CELERY_BROKER_URL = os.environ["CELERY_BROKER_URL"]

CELERY_RESULT_BACKEND = os.environ.get(
    "CELERY_RESULT_BACKEND"
)


# ---------------------------------------------------------------------------
# Celery - serialization
# ---------------------------------------------------------------------------

CELERY_ACCEPT_CONTENT = [
    "json",
]

CELERY_TASK_SERIALIZER = "json"

CELERY_RESULT_SERIALIZER = "json"


# ---------------------------------------------------------------------------
# Celery - timezone
# ---------------------------------------------------------------------------

CELERY_TIMEZONE = "UTC"

CELERY_ENABLE_UTC = True


# ---------------------------------------------------------------------------
# Celery - task execution
# ---------------------------------------------------------------------------

CELERY_TASK_TRACK_STARTED = True

CELERY_TASK_IGNORE_RESULT = False

CELERY_RESULT_EXPIRES = 3600


# ---------------------------------------------------------------------------
# Celery - reliability
# ---------------------------------------------------------------------------

CELERY_TASK_ACKS_LATE = True

CELERY_TASK_REJECT_ON_WORKER_LOST = True

CELERY_WORKER_PREFETCH_MULTIPLIER = 1


# ---------------------------------------------------------------------------
# Celery - task execution limits
# ---------------------------------------------------------------------------

CELERY_TASK_SOFT_TIME_LIMIT = 300

CELERY_TASK_TIME_LIMIT = 360


# ---------------------------------------------------------------------------
# Celery - broker connection
# ---------------------------------------------------------------------------

CELERY_BROKER_CONNECTION_RETRY_ON_STARTUP = True

CELERY_BROKER_CONNECTION_MAX_RETRIES = 100


# ---------------------------------------------------------------------------
# Celery - queues
# ---------------------------------------------------------------------------

CELERY_TASK_DEFAULT_QUEUE = "default"

CELERY_TASK_DEFAULT_EXCHANGE = "default"

CELERY_TASK_DEFAULT_ROUTING_KEY = "default"


# ---------------------------------------------------------------------------
# Celery - periodic tasks
# ---------------------------------------------------------------------------

CELERY_BEAT_SCHEDULE = {
    "cleanup-expired-sessions": {
        "task": "users.tasks.cleanup_expired_sessions",
        "schedule": crontab(
            minute="*/15",
        ),
    },
}
```

!!! tip "Celery Config Notes"

    JSON serialization - This keeps task messages predictable and avoids accepting arbitrary serialization formats.
    For most Django applications, JSON is the sensible default.

    UTC - Keep Celery's internal time handling in UTC. Django application can still present dates/times to users in their local timezone.

---

### `celery.py`

```python
import os

from celery import Celery


os.environ.setdefault(
    "DJANGO_SETTINGS_MODULE",
    "config.settings",
)


app = Celery("config")


app.config_from_object(
    "django.conf:settings",
    namespace="CELERY",
)


app.autodiscover_tasks()
```

!!! tip ""

    File `celery.py` should remain relatively small.
    
    Its job is primarily to:

    1.Tell Celery which Django settings to use.

    2.Create the Celery application.
    
    3.Load settings.
    
    4.Discover tasks.
---

### `__init__.py`

* This ensures the Celery application is imported as part of the Django project package.

```python
from .celery import app as celery_app


__all__ = ("celery_app",)
```

---

### Celery & Database transactions

The recommended Django convention is to enqueue Celery tasks only after the database transaction commits. This prevents a worker from running before the data it needs is visible in the database.

You do not need Celery Beat for this. The following applies to ordinary Celery tasks.

## 1. Recommended convention: `transaction.on_commit()`

Suppose you create an order and then want Celery to process it.

File: `orders/views.py` or your service layer.

Python

Run

```
from django.db import transaction

from orders.models import Order
from orders.tasks import process_order


@transaction.atomic
def create_order(customer, amount):
    order = Order.objects.create(
        customer=customer,
        amount=amount,
    )

    transaction.on_commit(
        lambda: process_order.delay(order.pk)
    )

    return order
```

### Execution flow

* 1.Start database transaction

`transaction.atomic()`

* 2.Create and save the order

The data is not committed yet.

* 3.Commit the transaction

Django successfully commits the database changes.

* 4.Enqueue the Celery task

`process_order.delay(order.pk)`

* 5.Worker processes the order

The committed order is available to query.

If the transaction rolls back, the callback is discarded and the task is not enqueued.

## 2. Define the task in `tasks.py`

File: `orders/tasks.py`

Python

Run

```
from celery import shared_task

from orders.models import Order


@shared_task
def process_order(order_id):
    order = Order.objects.get(pk=order_id)

    # Process the committed order here.
    order.status = "processing"
    order.save(update_fields=["status"])
```

Pass the model's primary key rather than the model instance. Celery serializes task arguments, so passing a database ID is simpler and avoids stale model objects.

## 3. The shorter convention: `delay_on_commit()`

If you use Celery 5.4 or later, Celery provides a convenient Django integration.

Python

Run

```
from django.db import transaction

from orders.models import Order
from orders.tasks import process_order


@transaction.atomic
def create_order(customer, amount):
    order = Order.objects.create(
        customer=customer,
        amount=amount,
    )

    process_order.delay_on_commit(order.pk)

    return order
```

This is equivalent in purpose to registering the task with Django's `transaction.on_commit()`.

Important: `delay_on_commit()` does not return the usual `AsyncResult` immediately because the task has not been submitted yet. If you need to capture the result after commit, use `transaction.on_commit()` with a callback.

## 4. What if the caller already manages the transaction?

You do not need to add `@transaction.atomic` to every function. If an outer transaction already exists, Django waits for that transaction to commit.

Python

Run

```
from django.db import transaction

from orders.tasks import process_order


with transaction.atomic():
    order = create_order_record()

    process_order.delay_on_commit(order.pk)

    # Other database changes...
```

The task is enqueued only after the outermost transaction commits successfully.

If there is no active transaction, Django executes an `on_commit()` callback immediately. Therefore, the convention also works outside an explicit `atomic()` block.

## 5. The production caveat: `on_commit()` is not a delivery guarantee

There is an important failure window:

1. The database commits successfully.

2. Django runs the callback.

3. Redis or RabbitMQ is unavailable, so task submission fails.

Your database record exists, but the task may not be queued. `on_commit()` solves the transaction ordering problem, not guaranteed message delivery.

For critical workflows, such as payments, fulfilment, or financial operations, consider the transactional outbox pattern:

* Save the business record and an outbox event in the same database transaction.

* Have a separate publisher process send pending events to Celery.

* Mark events as published and retry failures.

* Make task processing idempotent to handle duplicate delivery.

## Recommended convention



| Situation                                          | Convention                                   |
| -------------------------------------------------- | -------------------------------------------- |
| Ordinary task after a database write               | `task.delay_on_commit(...)`                  |
| Need explicit callback logic or an `AsyncResult`   | `transaction.on_commit(...)`                 |
| Multiple database writes must commit together      | Register the callback inside the transaction |
| Critical task delivery must survive broker outages | Transactional outbox                         |
| Task can be delivered or executed more than once   | Implement idempotency                        |

> Use `delay_on_commit()` for normal tasks, pass primary keys, and use a transactional outbox when losing a task after a successful database commit would be unacceptable.

```python
# payments/tasks.py

from celery import shared_task

from payments.models import Payment


@shared_task
def process_payment(payment_id):
    payment = Payment.objects.get(pk=payment_id)

    if payment.completed:
        return

    # Illustrative API call:
    # The provider must support idempotency keys.
    result = payment_provider.charge(
        payment_id=payment.pk,
        idempotency_key=payment.reference,
    )

    if result.success:
        payment.completed = True
        payment.save(update_fields=["completed"])
```

---

### Celery commands

#### 1.Local Celery development commands

| Purpose                           | Command                                                   |
| --------------------------------- | --------------------------------------------------------- |
| Start a Celery worker             | `celery -A config worker --loglevel=INFO`                 |
| Start a worker with one process   | `celery -A config worker --loglevel=INFO --concurrency=1` |
| Start a worker with debug logging | `celery -A config worker --loglevel=DEBUG`                |
| List registered tasks             | `celery -A config inspect registered`                     |
| Check worker connectivity         | `celery -A config inspect ping`                           |
| List active tasks                 | `celery -A config inspect active`                         |
| List reserved tasks               | `celery -A config inspect reserved`                       |
| List scheduled tasks              | `celery -A config inspect scheduled`                      |
| Show worker statistics            | `celery -A config inspect stats`                          |

> Replace `config` with Django project name.

---

#### 2.Production deployment commands

| Step                                       | Command                                                                         |
| ------------------------------------------ | ------------------------------------------------------------------------------- |
| Activate virtual environment (Linux/macOS) | `source .venv/bin/activate`                                                     |
| Install dependencies                       | `pip install -r requirements.txt`                                               |
| Validate production settings               | `python manage.py check --deploy`                                               |
| Apply database migrations                  | `python manage.py migrate --noinput`                                            |
| Collect static files                       | `python manage.py collectstatic --noinput`                                      |
| Start production worker                    | `celery -A config worker --loglevel=INFO --concurrency=4 --hostname=worker1@%h` |
| Verify workers respond                     | `celery -A config inspect ping`                                                 |
| Verify task registration                   | `celery -A config inspect registered`                                           |

Production note: Run Celery workers under a process supervisor or container orchestrator, such as systemd, Supervisor, or Kubernetes, so they restart automatically and their logs can be managed.

---

### Conventional Workflow

```
# 1. Run Django tests
python manage.py test

# 2. Start your broker (e.g. Redis) separately

# 3. Start the Celery worker in another terminal
celery -A config worker --loglevel=INFO --concurrency=1

# 4. Verify worker connectivity
celery -A config inspect ping

# 5. Before deployment
python manage.py check --deploy
python manage.py migrate --noinput
python manage.py collectstatic --noinput
```

---