# `filters.py` notes

## Overview

Django package `django-filter` extends writing querysets for the application.

`FilterSet` → defines what and how the data can be filtered.

`FilterView` → a generic Django CBV provided by django-filter that connects a FilterSet to a template-based list/filtering page.

`filterset_fields` → a shortcut for simple field-based filtering

| Requirement                          | Django approach                            |
| ------------------------------------ | ------------------------------------------ |
| Define filtering rules               | `FilterSet`                                |
| Filtering + HTML list page           | `FilterView`                               |
| Existing `ListView`                  | Apply `FilterSet` manually                 |
| Detail page + filter related objects | `DetailView` + `FilterSet`                 |

---

## Common Filter Fields

| Filter Field                | Data Type / Use Case                                                          |
| --------------------------- | ----------------------------------------------------------------------------- |
| `CharFilter`                | `CharField`, `TextField`, strings; supports exact, contains, icontains, regex |
| `BooleanFilter`             | `BooleanField`; True/False values                                             |
| `ChoiceFilter`              | `CharField` or `IntegerField` with choices                                    |
| `MultipleChoiceFilter`      | `CharField` or `IntegerField` with multiple choices                           |
| `DateFilter`                | `DateField`; supports exact, lt, gt, lte, gte                                 |
| `DateTimeFilter`            | `DateTimeField`; supports exact, lt, gt, lte, gte                             |
| `TimeFilter`                | `TimeField`; supports exact, lt, gt, lte, gte                                 |
| `NumberFilter`              | `IntegerField`, `FloatField`, `DecimalField`                                  |
| `RangeFilter`               | `IntegerField`, `FloatField`, `DecimalField`; min/max range                   |
| `DateFromToRangeFilter`     | `DateField`; filters between two dates (start/end)                            |
| `DateTimeFromToRangeFilter` | `DateTimeField`; start/end datetime                                           |
| `AllValuesFilter`           | Any field; filters by all unique values in the column                         |
| `ModelChoiceFilter`         | ForeignKey / OneToOne; selects a single related object                        |
| `ModelMultipleChoiceFilter` | ManyToMany / ForeignKey; selects multiple related objects                     |
| `OrderingFilter`            | Any field; allows ordering by one or multiple fields                          |

---

## Fields & Attributes

| Filter Field                  | Key Attributes / Keywords (with defaults)                                                                                                         |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| **CharFilter**                | `field_name=None`, `lookup_expr='exact'`, `label=None`, `method=None`, `distinct=False`, `widget=None`, `exclude=False`, `help_text=None`         |
| **BooleanFilter**             | `field_name=None`, `widget=None`, `label=None`, `method=None`, `distinct=False`, `exclude=False`                                                  |
| **ChoiceFilter**              | `field_name=None`, `choices=()`, `empty_label='---------‘`, `lookup_expr='exact'`, `label=None`, `method=None`, `distinct=False`, `exclude=False` |
| **MultipleChoiceFilter**      | `field_name=None`, `choices=()`, `conjoined=True`, `label=None`, `method=None`, `distinct=False`, `exclude=False`, `widget=None`                  |
| **NumberFilter**              | `field_name=None`, `lookup_expr='exact'`, `label=None`, `method=None`, `distinct=False`, `exclude=False`, `widget=None`                           |
| **RangeFilter**               | `field_name=None`, `label=None`, `method=None`, `distinct=False`, `exclude=False`, `widget=None`                                                  |
| **DateFilter**                | `field_name=None`, `lookup_expr='exact'`, `input_formats=None`, `label=None`, `method=None`, `distinct=False`, `exclude=False`, `widget=None`     |
| **DateTimeFilter**            | `field_name=None`, `lookup_expr='exact'`, `input_formats=None`, `label=None`, `method=None`, `distinct=False`, `exclude=False`, `widget=None`     |
| **TimeFilter**                | `field_name=None`, `lookup_expr='exact'`, `input_formats=None`, `label=None`, `method=None`, `distinct=False`, `exclude=False`, `widget=None`     |
| **DateFromToRangeFilter**     | `field_name=None`, `label=None`, `method=None`, `distinct=False`, `exclude=False`, `widget=None`                                                  |
| **DateTimeFromToRangeFilter** | `field_name=None`, `label=None`, `method=None`, `distinct=False`, `exclude=False`, `widget=None`                                                  |
| **AllValuesFilter**           | `field_name=None`, `label=None`, `method=None`, `distinct=False`, `exclude=False`                                                                 |
| **ModelChoiceFilter**         | `field_name=None`, `queryset=None`, `empty_label='---------‘`, `label=None`, `method=None`, `distinct=False`, `exclude=False`, `widget=None`      |
| **ModelMultipleChoiceFilter** | `field_name=None`, `queryset=None`, `conjoined=True`, `label=None`, `method=None`, `distinct=False`, `exclude=False`, `widget=None`               |
| **OrderingFilter**            | `fields=None`, `field_labels=None`, `label=None`, `method=None`                                                                                   |

- Notes on Key Attributes

`field_name` → model field this filter applies to; defaults to the filter name.

`lookup_expr` → SQL lookup expression; default is 'exact'. Can be 'icontains', 'gt', 'lt', 'gte', 'lte', etc.

`choices` → list of (value, label) tuples for ChoiceFilter / MultipleChoiceFilter.

`queryset` → for ModelChoiceFilter / ModelMultipleChoiceFilter; defines the objects to select from.

`conjoined` → only for MultipleChoiceFilter / ModelMultipleChoiceFilter; True = AND logic, False = OR logic.

`method` → custom method for filtering. Signature: def my_filter(self, queryset, name, value).

`distinct` → if True, adds .distinct() to the queryset to avoid duplicates.

`exclude` → if True, negates the filter (NOT).

`widget` → custom form widget to render the input.

`empty_label` → default label for blank option in select inputs.

---

### Example

```python
brand = django_filters.MultipleChoiceFilter(
    choices=Car.BrandType.choices,
    conjoined=False,  # OR logic
    label="Brand",
    method=None,      # Use default lookup
    distinct=True,    # Avoid duplicates
)
```

!!! tip "Example Usage of MultipleChoiceFilter"
    
    `conjoined=False` → returns cars where brand = Tesla OR Ford OR VW
    
    `distinct=True` → ensures no duplicates in result set

    conjoined=True → AND logic

    conjoined=False → OR logic

---

## Field lookups

```python
# general syntax
field__lookup=value

# example
Book.objects.filter(title__exact="Django")
```

| Lookup        | Meaning                      | Example                     |
| ------------- | ---------------------------- | --------------------------- |
| `exact`       | Exact match                  | `title__exact="Django"`     |
| `iexact`      | Case-insensitive exact match | `title__iexact="django"`    |
| `contains`    | Contains substring           | `title__contains="Django"`  |
| `icontains`   | Case-insensitive contains    | `title__icontains="django"` |
| `in`          | Matches values in iterable   | `id__in=[1, 2, 3]`          |
| `gt`          | Greater than                 | `price__gt=20`              |
| `gte`         | Greater than or equal        | `price__gte=20`             |
| `lt`          | Less than                    | `price__lt=20`              |
| `lte`         | Less than or equal           | `price__lte=20`             |
| `startswith`  | Starts with                  | `title__startswith="Djan"`  |
| `istartswith` | Case-insensitive starts with | `title__istartswith="djan"` |
| `endswith`    | Ends with                    | `title__endswith="Guide"`   |
| `iendswith`   | Case-insensitive ends with   | `title__iendswith="guide"`  |
| `range`       | Between two values           | `price__range=(10, 50)`     |
| `date`        | Matches date portion         | `created_at__date=date`     |
| `year`        | Matches year                 | `created_at__year=2026`     |
| `month`       | Matches month                | `created_at__month=9`       |
| `day`         | Matches day                  | `created_at__day=14`        |
| `week`        | Matches ISO week             | `created_at__week=38`       |
| `week_day`    | Matches day of week          | `created_at__week_day=2`    |
| `quarter`     | Matches quarter              | `created_at__quarter=3`     |
| `time`        | Matches time portion         | `created_at__time=time`     |
| `hour`        | Matches hour                 | `created_at__hour=15`       |
| `minute`      | Matches minute               | `created_at__minute=30`     |
| `second`      | Matches second               | `created_at__second=0`      |
| `isnull`      | Checks for `NULL`            | `author__isnull=True`       |
| `regex`       | Regular expression match     | `title__regex=r"^Django"`   |
| `iregex`      | Case-insensitive regex       | `title__iregex=r"^django"`  |

---

## Aggregation functions

| Function   | Purpose                           | Example             |
| ---------- | --------------------------------- | ------------------- |
| `Avg`      | Average value                     | `Avg("price")`      |
| `Count`    | Number of objects/related objects | `Count("books")`    |
| `Max`      | Maximum value                     | `Max("price")`      |
| `Min`      | Minimum value                     | `Min("price")`      |
| `StdDev`   | Standard deviation                | `StdDev("price")`   |
| `Sum`      | Sum of values                     | `Sum("price")`      |
| `Variance` | Variance of values                | `Variance("price")` |

```python
from django.db.models import Avg, Count, Max, Min, Sum

Book.objects.aggregate(
    average_price=Avg("price"),
    total_books=Count("id"),
    highest_price=Max("price"),
    lowest_price=Min("price"),
    total_value=Sum("price"),
)
```

---

## Mental model

```text
Django ORM
│
├── QuerySet methods
│   │
│   ├── Build/refine queries
│   │   └── filter(), exclude(), order_by()...
│   │
│   └── Execute / return something else
│       ├── get()
│       ├── count()
│       ├── exists()
│       ├── first()
│       ├── aggregate()
│       ├── update()
│       └── delete()
│
├── Field lookups
│   └── Define conditions
│       ├── __exact
│       ├── __icontains
│       ├── __gte
│       ├── __in
│       └── __isnull
│
└── Aggregations
    ├── Avg
    ├── Count
    ├── Max
    ├── Min
    ├── Sum
    ├── StdDev
    └── Variance
```

---

## Template

### Summary Table

| Rendering Method | Syntax                           | Output          | Use Case                     |
| ---------------- | -------------------------------- | --------------- | ---------------------------- |
| Default / auto   | `{{ filter.form }}`              | Table / default | Quick, minimal control       |
| Paragraph        | `{{ filter.form.as_p }}`         | `<p>` tags      | Vertical layout              |
| Unordered List   | `{{ filter.form.as_ul }}`        | `<ul><li>`      | Sidebar / list style         |
| Table            | `{{ filter.form.as_table }}`     | `<tr>`          | Tabular layout               |
| Manual fields    | `{{ filter.form.field_name }}`   | Custom HTML     | Full styling control         |
| Loop fields      | `{% for field in filter.form %}` | Custom HTML     | Dynamic / reusable           |
| Crispy Forms     | `{% crispy filter.form %}`       | Styled form     | Framework-integrated styling |

---

### Manual Render

```html
<form method="get">
  {{ filter.form.brand.label_tag }}
  {{ filter.form.brand }}

  {{ filter.form.fuel_type.label_tag }}
  {{ filter.form.fuel_type }}

  {{ filter.form.year.label_tag }}
  {{ filter.form.year }}

  {{ filter.form.miles.label_tag }}
  {{ filter.form.miles }}

  <button type="submit">Filter</button>
</form>
```

---

### Paragraph Render

```html
<form method="get">
  {{ filter.form.as_p }}
  <button type="submit">Filter</button>
</form>
```

---

### Unordered List Render

```html
<form method="get">
  {{ filter.form.as_ul }}
  <button type="submit">Filter</button>
</form>
```

---

### Table Rendering

```html
<form method="get">
  {{ filter.form.as_table }}
  <button type="submit">Filter</button>
</form>
```

---

### Form Iteration

```html
<form method="get">
  {% for field in filter.form %}
    <div class="filter-field">
      {{ field.label_tag }}
      {{ field }}
    </div>
  {% endfor %}
  <button type="submit">Apply Filters</button>
</form>
```

---

## Example

### 1.Model

```python
# models.py
from django.db import models
from django.core.validators import MinValueValidator, MaxValueValidator
import datetime

class Car(models.Model):

    class FuelType(models.TextChoices):
        PETROL = "petrol", "Petrol"
        DIESEL = "diesel", "Diesel"
        ELECTRIC = "electric", "Electric"
        HYBRID = "hybrid", "Hybrid"

    class BrandType(models.TextChoices):
        TESLA = "tesla", "Tesla"
        VW = "vw", "Volkswagen"
        FORD = "ford", "Ford"
        HYUNDAI = "hyundai", "Hyundai"

    brand = models.CharField(max_length=50, choices=BrandType.choices, default=BrandType.TESLA)
    fuel_type = models.CharField(max_length=20, choices=FuelType.choices, default=FuelType.ELECTRIC)
    year = models.PositiveIntegerField(validators=[MinValueValidator(1900), MaxValueValidator(datetime.date.today().year)])
    miles = models.PositiveIntegerField()
    price = models.DecimalField(max_digits=10, decimal_places=2)

    class Meta:
        ordering = ["-year", "brand"]
        indexes = [
            models.Index(fields=["brand"]),
            models.Index(fields=["fuel_type"]),
            models.Index(fields=["year"]),
        ]

    def __str__(self):
        return f"{self.get_brand_display()} ({self.year})"

```

---

### 2.Filter

```python
# filters.py
import django_filters
from django.db.models import Q
from .models import Car


class CarFilter(django_filters.FilterSet):

    brand = django_filters.MultipleChoiceFilter(
        choices=Car.BrandType,
        widget=django_filters.CheckboxSelectMultiple
    )

    fuel_type = django_filters.MultipleChoiceFilter(
        choices=Car.FuelType,
        widget=django_filters.CheckboxSelectMultiple
    )

    year_min = django_filters.NumberFilter(
        field_name="year",
        lookup_expr="gte"
    )

    miles_max = django_filters.NumberFilter(
        field_name="miles",
        lookup_expr="lte"
    )

    # Custom OR logic filter
    electric_or_low_miles = django_filters.BooleanFilter(
        method="filter_electric_or_low_miles",
        label="Electric OR Under 30k Miles"
    )

    class Meta:
        model = Car
        fields = []

    def filter_electric_or_low_miles(self, queryset, name, value):
        if value:
            return queryset.filter(
                Q(fuel_type="electric") | Q(miles__lte=30000)
            )
        return queryset

```

---

### 3.View

```python
# views.py
from django_filters.views import FilterView
from .models import Car

from .filters import CarFilter

class CarListView(FilterView):
    model = Car
    filterset_class = CarFilter
    template_name = "cars/car_list.html"
    paginate_by = 12

    def get_queryset(self):
        return (
            Car.objects
            .all()
            .order_by("-year")
        )
```

---

### 4.URL

```python
path("cars/", CarListView.as_view(), name="car_list")
```

---

### 5.Template

```html
<form method="get" class="filter-form">

    <div class="filter-group">
        <h4>Brand</h4>
        {% for checkbox in filter.form.brand %}
            <label class="checkbox">
                {{ checkbox.tag }}
                {{ checkbox.choice_label }}
            </label>
        {% endfor %}
    </div>

    <div class="filter-group">
        <h4>Fuel Type</h4>
        {% for checkbox in filter.form.fuel_type %}
            <label class="checkbox">
                {{ checkbox.tag }}
                {{ checkbox.choice_label }}
            </label>
        {% endfor %}
    </div>

    <div class="filter-group">
        <label>Year From</label>
        {{ filter.form.year_min }}
    </div>

    <div class="filter-group">
        <label>Max Miles</label>
        {{ filter.form.miles_max }}
    </div>

    <div class="filter-group special">
        <label>
            {{ filter.form.electric_or_low_miles }}
            Electric OR Under 30k Miles
        </label>
    </div>

    <button type="submit">Apply Filters</button>

</form>
```

---

## Q() object

### Overview

- Q() helps build a condition, that can be passed to `.filter()`, `.exclude()`, etc.

- Its job is to enable building complex database conditions.

- Simple example:

    * Normally:

    ```python
    Book.objects.filter(author=author)
    ```

    * With Q():

    ```python
    Book.objects.filter(
        Q(title__icontains="django") |
        Q(description__icontains="django")
    )
    ```

> This means: Find books where the title OR description contains "django".

---

### Examples

- Specific criteria

```python
from django.db.models import Q

Book.objects.filter(
    Q(author__name="George Orwell")
)
```

> Means: Give me books where the author's name is George Orwell.

---

- NOT

```python
Book.objects.filter(
    ~Q(status="archived")
)
```

> Means:Give me books that are NOT archived.

---

- AND

```python
Book.objects.filter(
    Q(status="published") &
    Q(copies_available__gt=0)
)
```

> Means:published AND has copies available.

---

- OR

```python
Book.objects.filter(
    Q(author__name="George Orwell") |
    Q(author__name="J.K. Rowling")
)
```

> Means: George Orwell OR J.K. Rowling.

---

## Best Practices

### When to Use Which Query Strategy

| Situation                 | Recommended Approach           |
| ------------------------- | ------------------------------ |
| Simple filter             | `.filter()`                    |
| Single object             | `.get()` (or `.first()` safer) |
| OR conditions             | `Q()`                          |
| Prevent race conditions   | `F()`                          |
| Related FK optimization   | `select_related()`             |
| Many-to-many optimization | `prefetch_related()`           |
| Lightweight API output    | `values()`                     |
| Aggregation               | `annotate()`                   |
| Existence check           | `exists()`                     |

---

### Query Optimization & Scalability

- Queries & Filters:

| Concept            | Description                  |
| ------------------ | ---------------------------- |
| QuerySet           | Lazy SQL abstraction         |
| filter()           | Apply conditions             |
| exclude()          | Negate condition             |
| select_related()   | Join FK                      |
| prefetch_related() | Separate M2M query           |
| Q()                | OR logic                     |
| F()                | Field arithmetic             |
| annotate()         | Aggregation                  |
| exists()           | Efficient existence check    |
| get_queryset()     | Proper CBV override location |


!!! note "Before deployment:"

    ✅ Use select_related where appropriate

    ✅ Use prefetch_related where needed

    ✅ Avoid unnecessary .count() calls

    ✅ Add database indexes on frequently filtered fields

    ✅ Use pagination

    ✅ Profile queries (Django Debug Toolbar)

    ✅ Avoid unnecessary annotations

- Scalability:

| Problem           | Solution                      |
| ----------------- | ----------------------------- |
| Large tables      | Add indexes                   |
| Slow filters      | Optimize queries              |
| Heavy joins       | Reduce fields                 |
| Large responses   | Pagination                    |
| Repeated logic    | Query service layer           |
| Complex reporting | Use raw SQL or database views |