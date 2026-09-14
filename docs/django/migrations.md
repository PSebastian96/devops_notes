# Migration Scenarios

### 1. Reverting a Migration

!!! warning "Use with care — avoid in production"

    1. Inspect migration history

        ```bash
        python manage.py showmigrations app_name
        ```

    2. Roll back to a previous migration

        ```bash
        python manage.py migrate app_name migration_name
        ```
        Example:
        ```bash
        python manage.py migrate app_name 0002_previous_migration
        ```

    3. Update model code to match the reverted schema

        This is mandatory, code must match the migration state.

        Restore model fields, Restore relationships, Restore constraints


    4. (Optional) Delete unapplied migration files

        Delete: 0003_*.py and 0004_*.py migration files.

    5. Generate new migrations if needed

        ```bash
        python manage.py makemigrations app_name
        ```

---

### 2. Migration Reset (Development Only)

!!! danger "Never use in production"

    Use this only during early development when:
    - No real data exists
    - Migration history can be rewritten safely

Steps (SQLite):

1. Remove the database file

    ```bash
    rm db.sqlite3
    ```

2. Delete migration files (keep `__init__.py`)

    ```bash
    find . -path "*/migrations/*.py" -not -name "__init__.py" -delete
    ```

3. Recreate migrations

    ```bash
    python manage.py makemigrations app_name
    python manage.py migrate
    ```


---

### 3. Changing Model Fields

- Initial model:

```python
    class Product(models.Model):
        name = models.CharField(max_length=100)
        price = models.IntegerField()  # stored in cents
```

- Initial commit: `0001_initial.py`

---

#### 1.Add a NEW field (non-breaking)

```python
    class Product(models.Model):
        name = models.CharField(max_length=100)
        price = models.IntegerField()
        price_decimal = models.DecimalField(
            max_digits=10,
            decimal_places=2,
            null=True,
            blank=True
        )
```

```bash
python manage.py makemigrations app_name
python manage.py migrate
```
- Commit number 2: `0002_add_price_decimal.py`

---

#### 2.Data Migration (CRITICAL)

```bash
python manage.py makemigrations --empty products
```

```python
# products/migrations/0003_migrate_price.py

from django.db import migrations
from decimal import Decimal

def migrate_price(apps, schema_editor):
    Product = apps.get_model("products", "Product")

    for product in Product.objects.all():
        product.price_decimal = Decimal(product.price) / 100
        product.save(update_fields=["price_decimal"])

class Migration(migrations.Migration):

    dependencies = [
        ("products", "0002_add_price_decimal"),
    ]

    operations = [
        migrations.RunPython(migrate_price),
    ]
```

```bash
python manage.py migrate
```
- Commit number 3: `0003_migrate_price.py`

---

#### 3.Switch code usage

```python
class Product(models.Model):
    name = models.CharField(max_length=100)
    price_decimal = models.DecimalField(max_digits=10, decimal_places=2)

```

!!! danger "Important updates before migration"

    - Views

    - Serializers

    - Templates

    - Admin

    - APIs

    - Deploy this change before deleting the old field.

---

#### 4.Remove old field (FINAL)

```python
# products/migrations/0004_remove_old_price.py

from django.db import migrations

class Migration(migrations.Migration):

    dependencies = [
        ("products", "0003_migrate_price"),
    ]

    operations = [
        migrations.RemoveField(
            model_name="product",
            name="price",
        ),
    ]

```

- Final Model field

- Commit number 4: `0004_remove_old_price.py`

```python
class Product(models.Model):
    name = models.CharField(max_length=100)
    price_decimal = models.DecimalField(max_digits=10, decimal_places=2)
```