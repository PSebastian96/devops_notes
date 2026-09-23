# `serializers.py` notes

## Serializers

### Model

```python
# models.py
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=200)
    description = models.TextField(blank=True)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.name
```

#### Serializer

```python
from rest_framework import serializers
from .models import Product


class ProductSerializer(serializers.ModelSerializer):

    class Meta:
        model = Product
        fields = [
            "id",
            "name",
            "description",
            "price",
            "created_at",
        ]
```

!!! tip "How are `models` and `serializers` connected?"

    They establish the connection through the Meta class in Serializers,
    under the attribute of `model = <ModelName>`, 
    where `fields = ['<field_name>']` defines which fields from the model should be available and ommit in JSON format.


### Validator

```python
# validators.py
from rest_framework import serializers


def validate_product_name(value):
    if len(value.strip()) < 3:
        raise serializers.ValidationError(
            "Product name must be at least 3 characters long."
        )

    return value


def validate_product_price(value):
    if value <= 0:
        raise serializers.ValidationError(
            "Product price must be greater than 0."
        )

    return value
```
#### Serializer

```python
from rest_framework import serializers

from .models import Product
from .validators import (
    validate_product_name,
    validate_product_price,
)


class ProductSerializer(serializers.ModelSerializer):

    name = serializers.CharField(
        validators=[validate_product_name]
    )

    price = serializers.DecimalField(
        max_digits=10,
        decimal_places=2,
        validators=[validate_product_price]
    )

    class Meta:
        model = Product
        fields = [
            "id",
            "name",
            "description",
            "price",
            "created_at",
        ]
```

You don't have to put every validation rule into validators.py. A useful convention is

```text
validators.py
    │
    ├── reusable field validation
    └── validation shared by multiple serializers

serializers.py
    │
    ├── serializer-specific validation
    └── cross-field validation

models.py
    │
    ├── model constraints
    └── database-level integrity
```

>For example, "price must be greater than zero" is a good field validator, while "discounted price must be lower than the normal price" is a good candidate for serializer-level validation because it involves two fields

---

### Service

```python
from decimal import Decimal


def calculate_discounted_price(price, discount_percentage):
    """
    Calculate the final price after applying a percentage discount.
    """

    discount = price * (discount_percentage / Decimal("100"))
    final_price = price - discount

    return final_price
```

#### Serializer

```python
from rest_framework import serializers

from .models import Product
from .services import calculate_discounted_price


class ProductSerializer(serializers.ModelSerializer):

    discounted_price = serializers.SerializerMethodField()

    class Meta:
        model = Product
        fields = [
            "id",
            "name",
            "price",
            "discounted_price",
            "created_at",
        ]

    def get_discounted_price(self, obj):
        return calculate_discounted_price(
            obj.price,
            Decimal("20"),
        )
```

---

### Filter

The filter doesn't normally call the serializer, and the serializer doesn't normally call the filter.

Filters and Serializers are conencted through Views.

```text
Client
  │
  │ GET /products/?min_price=50
  ▼
View
  │
  ├── Filter
  │     └── determines WHICH products
  │
  ├── Pagination
  │
  └── Serializer
        └── determines HOW products are represented
  │
  ▼
JSON response
```

```python
import django_filters

from .models import Product


class ProductFilter(django_filters.FilterSet):

    name = django_filters.CharFilter(
        field_name="name",
        lookup_expr="icontains",
    )

    min_price = django_filters.NumberFilter(
        field_name="price",
        lookup_expr="gte",
    )

    max_price = django_filters.NumberFilter(
        field_name="price",
        lookup_expr="lte",
    )

    created_after = django_filters.DateTimeFilter(
        field_name="created_at",
        lookup_expr="gte",
    )

    created_before = django_filters.DateTimeFilter(
        field_name="created_at",
        lookup_expr="lte",
    )

    class Meta:
        model = Product
        fields = [
            "name",
            "min_price",
            "max_price",
            "created_after",
            "created_before",
        ]
```

---

### Pagination

Keeping `filters.py` and `pagination.py` separate is the convention.

```python
# pagination.py

from rest_framework.pagination import PageNumberPagination


class ProductPagination(PageNumberPagination):
    page_size = 10
    page_size_query_param = "page_size"
    max_page_size = 100
```

### View

In this section, the orcastration happens, where filters, paginations, serializers are put together.

```python
# views.py

from django_filters.rest_framework import DjangoFilterBackend
from rest_framework.generics import ListAPIView

from .filters import ProductFilter
from .models import Product
from .paginations import ProductPagination
from .serializers import ProductSerializer


class ProductListView(ListAPIView):
    # models.py
    queryset = Product.objects.all()
    # serializers.py
    serializer_class = ProductSerializer

    filter_backends = [
        DjangoFilterBackend,
    ]

    # filters.py
    filterset_class = ProductFilter
    # paginations.py
    pagination_class = ProductPagination
```

---

### URL

The core principle is: keep URLs resource-oriented and let HTTP methods and query parameters express the operation.

- Global `urls.py` (next to `settings.py`):

```python
from django.contrib import admin
from django.urls import include, path


urlpatterns = [
    path("admin/", admin.site.urls),

    path("api/products/", include("products.urls")),
    path("api/accounts/", include("accounts.urls")),
]
```

- App scoped urls:

```python
from django.urls import path

from .views import (
    ProductDetailView,
    ProductListView,
)


app_name = "products"


urlpatterns = [
    path("", ProductListView.as_view(), name="list"),
    path("<int:pk>/", ProductDetailView.as_view(), name="detail"),
]
```

---

#### URL Naming Conventions

1.Use plural nouns for resources

```text   
   /api/v1/products/
   /api/v1/orders/
   /api/v1/users/
```

2.Use HTTP methods for CRUD operations

```text   
   GET     /api/v1/products/
   POST    /api/v1/products/
   GET     /api/v1/products/42/
   PATCH   /api/v1/products/42/
   DELETE  /api/v1/products/42/
```

3.Do not put CRUD verbs in URLs
   Avoid:

```text   
   /api/v1/get-products/
   /api/v1/create-product/
   /api/v1/delete-product/
```

4.Use IDs for individual resources

```text   
   /api/v1/products/42/
   /api/v1/orders/123/
```

5.Use query parameters for filtering

```text   
   /api/v1/products/?min_price=50
   /api/v1/products/?max_price=200
```

6.Use query parameters for searching

```text
   /api/v1/products/?search=keyboard
```

7.Use query parameters for ordering

```text
   /api/v1/products/?ordering=-price
```

8.Use query parameters for pagination

```text   
   /api/v1/products/?page=2&page_size=20
```

9.Use explicit action endpoints only for domain-specific actions

```text
   /api/v1/products/42/archive/
   /api/v1/orders/123/cancel/
```

10.Use API versioning when appropriate

```text
    /api/v1/products/
    /api/v2/products/
```

11.Keep project-level URLs separate from app-level URLs

```text
    config/urls.py
        /api/v1/products/
            ↓
        products/urls.py
```

12.Use Django URL namespaces

```text
    app_name = "products"

    name="list"
    name="detail"

    Refer to them as:
    products:list
    products:detail
```

13.Pick a trailing-slash convention and use it consistently

```text
    /api/v1/products/
    /api/v1/products/42/
```

14.Keep URLs resource-oriented

```text
    URL             → Resource
    HTTP method     → Operation
    Query parameter → Filtering/search/pagination/ordering
```

---

#### Viewsets

URL convention changes slightly because, normally a router is used to generate the CRUD routes automatically.

1.Define a ViewSet

```python
   class ProductViewSet(ModelViewSet):
       queryset = Product.objects.all()
       serializer_class = ProductSerializer
```

2.Register the ViewSet with a router

```python
   router.register(
       "products",
       ProductViewSet,
       basename="product",
   )
```

3.Include the router URLs in the app's urls.py

```python
   urlpatterns = [
       path("", include(router.urls)),
   ]
```

4.The router generates the standard CRUD URLs

```python
   GET     /api/v1/products/
           → list

   POST    /api/v1/products/
           → create

   GET     /api/v1/products/42/
           → retrieve

   PUT     /api/v1/products/42/
           → update

   PATCH   /api/v1/products/42/
           → partial_update

   DELETE  /api/v1/products/42/
           → destroy
```

5.Use plural resource names when registering ViewSets

```text
   router.register("products", ProductViewSet)

   Good:
   /api/v1/products/

   Avoid:
   /api/v1/product/
   /api/v1/get-products/
```

6.Use query parameters for filtering/search/ordering

```text
   /api/v1/products/?min_price=50

   /api/v1/products/?search=keyboard

   /api/v1/products/?ordering=-price
```

---

## Authentication

---

### Auth

---

### Permissions

---