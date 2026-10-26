# Celery-Beat

## Overview

Celery Beat for scheduling recurring tasks.

Celery's default schedule is CELERY_BEAT_SCHEDULE; the database scheduler adds runtime management through Django Admin.

`celery_beat.py` is a custom module name, not a required Celery filename. The conventional Celery application initialization file is `config/celery.py`.

Important distinction: CELERY_BEAT_SCHEDULE is the Django setting name; BEAT_SCHEDULE is simply a Python variable name you can choose for your separate module. Celery recognizes the schedule after you explicitly assign it to app.conf.beat_schedule in config/celery.py.

## 1.Installation

```bash
uv add "celery[redis]" django-celery-beat
```

---

## 2.Setup

```python
INSTALLED_APPS = [
    # Your existing Django apps...
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",

    # Optional: enable database-managed periodic schedules.
    # "django_celery_beat",
]

# Timezone configuration
USE_TZ = True
TIME_ZONE = "Europe/London"

# Celery uses the CELERY_ prefix for its settings.
CELERY_BROKER_URL = os.environ.get(
    "CELERY_BROKER_URL",
    "redis://localhost:6379/0",
)

# Use UTC internally, while the configured timezone determines
# how periodic schedules such as crontab entries are interpreted.
CELERY_ENABLE_UTC = True
CELERY_TIMEZONE = TIME_ZONE

# Task discovery and serialization
CELERY_TASK_SERIALIZER = "json"
CELERY_ACCEPT_CONTENT = ["json"]
CELERY_RESULT_SERIALIZER = "json"

# Store task results only if your application needs to query them.
# CELERY_RESULT_BACKEND = "redis://localhost:6379/1"

# Periodic scheduling is defined in config/celery_beat.py.
# The default PersistentScheduler is used for this approach.
```

-`CELERY_BROKER_URL` tells Celery where to publish messages.

-`CELERY_TIMEZONE` controls the timezone used by Celery schedules.

-`CELERY_ENABLE_UTC` = True keeps Celery's internal time handling UTC-aware.

- `JSON` serialization is a sensible default when tasks exchange ordinary data such as strings, numbers, lists, and dictionaries.

---

## 3.Celery.py

```python

import os

from celery import Celery

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "config.settings")

app = Celery("config")

# Read Django settings prefixed with CELERY_.
app.config_from_object("django.conf:settings", namespace="CELERY")

# Discover tasks.py modules in installed Django apps.
app.autodiscover_tasks()

# Load the Python-defined periodic schedule.
from config.celery_beat import BEAT_SCHEDULE

app.conf.beat_schedule = BEAT_SCHEDULE

```

---

## 4.init.py

```python

from .celery import app as celery_app

__all__ = ("celery_app",)

```

---

## 5.celery_beat.py

```python

from celery.schedules import crontab

BEAT_SCHEDULE = {
    "check-pending-orders-every-5-minutes": {
        "task": "orders.tasks.check_pending_orders",
        "schedule": 300.0,
    },
    "daily-order-cleanup-at-0230": {
        "task": "orders.tasks.cleanup_old_orders",
        "schedule": crontab(
            minute=30,
            hour=2,
        ),
    },
}

```

The task value is the registered Celery task name. The schedule value can be a number of seconds, a timedelta, a crontab, or another supported schedule object.

For this example:
- 300.0 means every 300 seconds.
- crontab(minute=30, hour=2) means at 02:30 according to CELERY_TIMEZONE.
- Beat sends the task message; a running worker must execute it.

---

## 6.task.py

```python

import logging
from datetime import timedelta

from celery import shared_task
from django.utils import timezone

from orders.models import Order

logger = logging.getLogger(__name__)


@shared_task
def check_pending_orders() -> dict:
    """Report how many orders are currently pending."""
    pending_count = Order.objects.filter(
        status=Order.Status.PENDING,
    ).count()

    logger.info("Pending orders: %s", pending_count)

    return {"pending_count": pending_count}


@shared_task
def cleanup_old_orders() -> dict:
    """Example cleanup task for old completed orders."""
    cutoff = timezone.now() - timedelta(days=90)

    # This example reports eligible records rather than deleting them.
    old_count = Order.objects.filter(
        status=Order.Status.COMPLETED,
        completed_at__lt=cutoff,
    ).count()

    logger.info("Old completed orders eligible for cleanup: %s", old_count)

    return {"eligible_count": old_count}
```

---

## 7.Conventional commands to run

```bash
uv sync --locked
python manage.py migrate
```

```bash
docker run --rm --name redis -p 6379:6379 redis:7
```

```bash
# Terminal 1: Execute tasks
uv run celery -A config worker --loglevel=INFO

# Terminal 2: Publish scheduled tasks
uv run celery -A config beat --loglevel=INFO
```

---

## Summary

### settings.py

| Variable | Default value | Purpose |
|---|---|---|
| `CELERY_BEAT_SCHEDULE` | `{}` | Defines periodic task schedules |
| `CELERY_BEAT_SCHEDULER` | `celery.beat:PersistentScheduler` | Selects the scheduler implementation |
| `CELERY_BEAT_SCHEDULE_FILENAME` | `celerybeat-schedule` | File used to persist scheduler state |
| `CELERY_BEAT_MAX_LOOP_INTERVAL` | `0` (scheduler-dependent behavior) | Controls the maximum scheduler loop interval |
| `CELERY_BEAT_SYNC_EVERY` | `0` | Controls synchronization frequency for persistent scheduler state |
| `CELERY_TIMEZONE` | `None` | Timezone used to interpret schedules; UTC is used when applicable |
| `CELERY_ENABLE_UTC` | `True` | Enables UTC-based time handling |
| `CELERY_BROKER_URL` | Must be configured for your deployment | Broker URL, such as Redis |
| `CELERY_TASK_ALWAYS_EAGER` | `False` | Executes tasks synchronously instead of sending them through the broker |

---

### celery_beat.py

| Variable / key | Example value | Purpose |
|---|---|---|
| `BEAT_SCHEDULE` | `{...}` | Dictionary containing periodic task definitions |
| Schedule entry name | `check-orders-every-5-minutes` | Unique identifier for a schedule entry |
| `task` | `orders.tasks.check_pending_orders` | Registered Celery task name |
| `schedule` (interval) | `300.0` | Runs every 300 seconds |
| `schedule` (crontab) | `crontab(minute=30, hour=2)` | Runs daily at 02:30 in the configured timezone |
| `args` | `(123,)` | Positional arguments passed to the task |
| `kwargs` | `{"batch_size": 100}` | Keyword arguments passed to the task |
| `options` | `{"queue": "maintenance"}` | Task execution options, such as queue routing |
| `relative` | `True` or `False` | Controls interval rounding for `timedelta` schedules |

---

## Code Example

### orders/tasks.py

```python

import logging

from celery import shared_task
from orders.models import Order

logger = logging.getLogger(__name__)


@shared_task(
    name="orders.tasks.check_pending_orders",
)
def check_pending_orders() -> dict:
    """Periodically count pending orders."""
    pending_count = Order.objects.filter(
        status=Order.Status.PENDING,
    ).count()

    logger.info("Found %s pending orders", pending_count)

    return {
        "status": "success",
        "pending_count": pending_count,
    }

```

---

### config/celery_beat.py

```python

BEAT_SCHEDULE = {
    "check-pending-orders-every-5-minutes": {
        "task": "orders.tasks.check_pending_orders",
        "schedule": 300.0,
    },
}

```

---

```python
# cron-style schedule

from celery.schedules import crontab

BEAT_SCHEDULE = {
    "daily-pending-order-check": {
        "task": "orders.tasks.check_pending_orders",
        "schedule": crontab(minute=30, hour=2),
    },
}
```

---

### config/celery.py

```python

import os

from celery import Celery
from config.celery_beat import BEAT_SCHEDULE

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "config.settings")

app = Celery("config")
app.config_from_object("django.conf:settings", namespace="CELERY")
app.autodiscover_tasks()

app.conf.beat_schedule = BEAT_SCHEDULE
```

---

### config/__init__.py

```python
from .celery import app as celery_app

__all__ = ("celery_app",)
```

### celery beat command

- Celery worker:

```bash
uv run celery -A config worker --loglevel=INFO
```

- Celery Beat scheduler:

```bash
uv run celery -A config beat --loglevel=INFO
```

Beat publishes the task every five minutes; the worker executes it.

Important: Run only one Beat scheduler instance for this schedule to avoid duplicate periodic dispatches. Also, this example counts orders rather than modifying them, making it a safe starting point for testing.

---