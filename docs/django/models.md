# `models.py` notes

## Models

!!! note "Convention" 
    
    Convention: One models.py per app (can be split into a package for large apps).

    ```bash
        models/
            ├── __init__.py
            ├── product.py
            ├── category.py
    ```
    ```bash
    # models/__init__.py
        from .product import Product
        from .category import Category

    ```

- Field Options (Common to Most Fields):

| Option         | Description                |
| -------------- | -------------------------- |
| `null`         | Store NULL in DB           |
| `blank`        | Allow empty value in forms |
| `default`      | Default value              |
| `unique`       | Enforce uniqueness         |
| `db_index`     | Add DB index               |
| `editable`     | Editable in admin          |
| `help_text`    | Admin form help            |
| `verbose_name` | Human-readable name        |
| `choices`      | Limited set of values      |

!!! warning "Important Notes"

    null → database-level

    blank → form-level

    default=NOT_PROVIDED means required

    db_index=True adds a DB index

    unique=True creates a uniqueness constraint

### Field types

Django model fields are defined using `models.FieldType()`.

| Field Type | Example | Purpose |
|---|---|---|
| `AutoField` | `models.AutoField()` | Auto-incrementing integer primary key |
| `BigAutoField` | `models.BigAutoField()` | Auto-incrementing large integer primary key |
| `SmallAutoField` | `models.SmallAutoField()` | Auto-incrementing small integer primary key |
| `BigIntegerField` | `models.BigIntegerField()` | Large signed integer |
| `BinaryField` | `models.BinaryField()` | Store raw binary data |
| `BooleanField` | `models.BooleanField()` | Store `True` / `False` |
| `CharField` | `models.CharField(max_length=255)` | Short text/string |
| `DateField` | `models.DateField()` | Date without time |
| `DateTimeField` | `models.DateTimeField()` | Date and time |
| `DecimalField` | `models.DecimalField(max_digits=10, decimal_places=2)` | Fixed-precision decimal numbers |
| `DurationField` | `models.DurationField()` | Store a duration/time interval |
| `EmailField` | `models.EmailField()` | Email address |
| `FileField` | `models.FileField(upload_to="files/")` | Uploaded files |
| `FilePathField` | `models.FilePathField(path="/files/")` | File paths from a filesystem directory |
| `FloatField` | `models.FloatField()` | Floating-point number |
| `GenericIPAddressField` | `models.GenericIPAddressField()` | IPv4 or IPv6 address |
| `ImageField` | `models.ImageField(upload_to="images/")` | Uploaded image files |
| `IntegerField` | `models.IntegerField()` | Standard signed integer |
| `JSONField` | `models.JSONField()` | Store JSON objects/arrays |
| `PositiveBigIntegerField` | `models.PositiveBigIntegerField()` | Large non-negative integer |
| `PositiveIntegerField` | `models.PositiveIntegerField()` | Non-negative integer |
| `PositiveSmallIntegerField` | `models.PositiveSmallIntegerField()` | Small non-negative integer |
| `SlugField` | `models.SlugField(max_length=255)` | URL-friendly text/slug |
| `SmallIntegerField` | `models.SmallIntegerField()` | Small signed integer |
| `TextField` | `models.TextField()` | Large/unlimited text |
| `TimeField` | `models.TimeField()` | Time without a date |
| `UUIDField` | `models.UUIDField()` | Universally unique identifier |

### Relationship Fields

| Field Type | Example | Purpose |
|---|---|---|
| `ForeignKey` | `models.ForeignKey(User, on_delete=models.CASCADE)` | Many-to-one relationship |
| `OneToOneField` | `models.OneToOneField(User, on_delete=models.CASCADE)` | One-to-one relationship |
| `ManyToManyField` | `models.ManyToManyField(Product)` | Many-to-many relationship |

### Meta class

### Constraints

## Migrations

## Object manager

- Used in `services.py`, `filters.py`, `selectors.py` .

- Example: `MyObject.objects.all()`

| Method | Parameters | Purpose |
|---|---|---|
| `all()` | None | Return a `QuerySet` containing all objects |
| `filter()` | `**kwargs` | Return objects matching conditions |
| `exclude()` | `**kwargs` | Return objects *not* matching conditions |
| `get()` | `*args, **kwargs` | Retrieve exactly one object |
| `create()` | `**kwargs` | Create and save an object |
| `get_or_create()` | `defaults=None, **kwargs` | Get an object or create it |
| `update_or_create()` | `defaults=None, create_defaults=None, **kwargs` | Update an object or create it |
| `bulk_create()` | `objs, batch_size=None, ignore_conflicts=False, update_conflicts=False, update_fields=None, unique_fields=None` | Insert multiple objects efficiently |
| `bulk_update()` | `objs, fields, batch_size=None` | Update multiple objects efficiently |
| `count()` | None | Count matching objects |
| `exists()` | None | Check whether any objects exist |
| `first()` | None | Get the first object |
| `last()` | None | Get the last object |
| `earliest()` | `*fields` | Get earliest object by field(s) |
| `latest()` | `*fields` | Get latest object by field(s) |
| `aggregate()` | `*args, **kwargs` | Calculate aggregate values |
| `annotate()` | `*args, **kwargs` | Add calculated fields to each result |
| `order_by()` | `*field_names` | Specify ordering |
| `distinct()` | `*field_names` | Remove duplicate results |
| `values()` | `*fields, **expressions` | Return dictionaries instead of model instances |
| `values_list()` | `*fields, flat=False, named=False` | Return tuples/values |
| `dates()` | `field_name, kind, order='ASC'` | Query by date periods |
| `datetimes()` | `field_name, kind, order='ASC', tzinfo=None` | Query by datetime periods |
| `select_related()` | `*fields` | Efficiently load `ForeignKey`/`OneToOne` relations |
| `prefetch_related()` | `*lookups` | Efficiently load related collections |
| `raw()` | `raw_query, params=(), translations=None` | Execute raw SQL and return model instances |
| `get_queryset()` | None | Return the manager's base `QuerySet` |

## F() functions

## Atomic transactions