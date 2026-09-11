# Django Conventions

## Project Setup Example

```text
products/
│
├── models.py
│
├── selectors.py
│   ├── get_product()
│   ├── get_products()
│   └── get_active_products()
│
├── services.py
│   ├── create_product()
│   ├── update_product()
│   └── delete_product()
│
├── forms.py
│   ├── ProductForm
│   └── ProductSearchForm
│
├── filters.py
│   └── ProductFilter
│
├── views.py
│   ├── ProductListView
│   ├── ProductDetailView
│   ├── ProductCreateView
│   └── ProductUpdateView
│
└── urls.py
```

---

## Models

- Model names are generally singular because they represent one object.

```python
class Product(models.Model):
    ...

class OrderItem(models.Model):
    ...

class UserProfile(models.Model):
    ...
```

- Field names use `snake_case` and descriptive nouns.

```python
class Product(models.Model):
    name = models.CharField(...)
    unit_price = models.DecimalField(...)
    created_at = models.DateTimeField(...)
    is_active = models.BooleanField(...)
```

- Foreignkeys use singular name of the related object.

```python
class Order(models.Model):
    customer = models.ForeignKey(Customer, ...)
    shipping_address = models.ForeignKey(Address, ...)
```

---

## Object Manager

The conventional default is:`.objects`.

```python
Product.objects.all()
Order.objects.filter(...)
```

Custom manager:

```python
class Product(models.Model):
    objects = models.Manager()
    active = ActiveProductManager()
```

!!! warning

    Custom managers are given names describing what they return.
    Don't unnecessarily create managers when a selector is more appropriate.

---

## Selectors

Selectors use name functions with `get_....`.

```python
def get_products():
    ...

def get_products_for_user(*, user):
    ...

def get_active_products():
    ...

def get_pending_orders_for_customer(*, customer):
    ...
```

```python
# expected to return one object
get_product(...)

# expected to return a QuerySet / collection
get_products(...)
```

---

## Filters

The class name ends with `Filter`.

```python
class ProductFilter(django_filters.FilterSet):
    ...

class OrderFilter(django_filters.FilterSet):
    ...
```

- `qs` is a very common Django abbreviation for QuerySet.

```python
# same as queryset = Product.objects.filter(...)
qs = Product.objects.filter(...)
```

---

## Filter vs Selector

Selector => application decides what to query.

Filter => user/client decides how to narrow the query (filter translates the user's parameters into ORM queries).

- Key distinction:

|                     | Selector         | FilterSet                   |
| ------------------- | ---------------- | --------------------------- |
| Purpose             | Retrieve data    | Apply user-selected filters |
| Controlled by       | Application      | User/client                 |
| Usually lives in    | `selectors.py`   | `filters.py`                |
| Handles HTTP params | Usually no       | Yes                         |
| Validation          | Usually no       | Yes                         |
| Produces QuerySet   | Yes              | Yes                         |
| Business rules      | Often            | Generally no                |
| Search/filter UI    | Not specifically | Yes                         |


---

## Services

Live in `services.py` layer for operations that change state, names usually describe the action.

```python
create_order(...)
cancel_order(...)
activate_subscription(...)
send_invoice(...)
update_product(...)
```

---

## Tasks

---

## Forms

Form classes by convention end on `Form`.

```python
class ProductSearchForm(forms.Form):
    ...

class ProductCreateForm(forms.ModelForm):
    ...

class CheckoutForm(forms.Form):
    ...
```

> **Use `forms.Form` when the form represents an action/query/input.**
>
> **Use `forms.ModelForm` when the form represents creating or editing a model instance.**


- Common decision:

| Question | `forms.Form` | `forms.ModelForm` |
|---|:---:|:---:|
| Is the form directly based on a Django model? | ❌ | ✅ |
| Does the form create a model instance? | ❌ | ✅ |
| Does the form update a model instance? | ❌ | ✅ |
| Does `form.save()` make sense? | ❌ | ✅ |
| Is it for searching/filtering? | ✅ | ❌ |
| Is it for login? | ✅ | ❌ |
| Is it for registration? | Usually ✅ | Sometimes |
| Is it for contact/feedback? | ✅ | ❌ |
| Is it for changing a password? | ✅ | ❌ |
| Is it for a multi-step/query operation? | ✅ | ❌ |
| Is it for creating a `Product`? | ❌ | ✅ |
| Is it for editing a `Product`? | ❌ | ✅ |
| Is it for creating an `Order`? | Can be ❌ | Usually ✅ |
| Does it need model field metadata automatically? | ❌ | ✅ |
| Does it automatically validate model fields? | ❌ | ✅ |
| Does it automatically handle model relationships? | ❌ | ✅ |
| Is persistence the purpose of the form? | ❌ | ✅ |

- CRUD based decision:

| Operation              | Typical choice                      |
| ---------------------- | ----------------------------------- |
| Create `Product`       | `ModelForm`                         |
| Update `Product`       | `ModelForm`                         |
| Delete `Product`       | Usually no form / confirmation form |
| View `Product`         | No form                             |
| Search `Product`       | `Form`                              |
| Filter `Product`       | `Form` or `django-filter`           |
| Sort `Product`         | `Form` or query parameter           |
| Login                  | `Form`                              |
| Register user          | `ModelForm` or specialized `Form`   |
| Change password        | `Form`                              |
| Contact us             | `Form`                              |
| Generate report        | `Form`                              |
| Upload/edit model data | `ModelForm`                         |

---

## Views

WIth Class Based Views, uses the model/domain followed by what the view does.

```python
class ProductListView():
    ...

class ProductDetailView():
    ...

class ProductCreateView():
    ...

class ProductUpdateView():
    ...

class ProductDeleteView():
    ...
```

---

## Urls

Use lowercase snake_case.

- Example for an app: `products/urls.py`

```python
from django.urls import path

from . import views

app_name = "products"

urlpatterns = [
    path(
        "",
        views.ProductListView.as_view(),
        name="list",
    ),
    path(
        "<int:pk>/",
        views.ProductDetailView.as_view(),
        name="detail",
    ),
    path(
        "create/",
        views.ProductCreateView.as_view(),
        name="create",
    ),
    path(
        "<int:pk>/edit/",
        views.ProductUpdateView.as_view(),
        name="update",
    ),
    path(
        "<int:pk>/delete/",
        views.ProductDeleteView.as_view(),
        name="delete",
    ),
]
```

- Example for a project level `urls.py`

```python
# config/urls.py

from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path("admin/", admin.site.urls),
    path("products/", include("products.urls")),
]
```

Namespace provides the domain context:

```text
products:list
products:detail
products:create
```

This is particularly nice when there are many domains:

```text
products:list
orders:list
customers:list
```

- Example in templates:

```html
<a href="{% url 'products:detail' product.pk %}">
    {{ product.name }}
</a>
```

## Templates

```text
products/
    |
    templates/
    └── products/
        ├── product_list.html
        ├── product_detail.html
        ├── product_form.html
        └── product_confirm_delete.html
```

---

## Apps

App names are generally lowercase and singular/plural depending on what the app represents.

```text
products/
orders/
accounts/
billing/
```

---