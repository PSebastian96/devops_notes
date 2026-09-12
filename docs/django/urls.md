# `urls.py` notes

## Overview

- URLs map intent to views — naming and structure matter more than syntax.

!!! tip "URL Best Practices"

    One urls.py per app.

    Always name URLs.

    Use namespaces.

    Use pk by default.

    Reverse URLs everywhere.

    Keep paths readable.

    Avoid regex unless necessary.

    Never expose internal IDs you don’t control.

    Validate object ownership in views.

    URLs ≠ authorization.

- URL Conventional Setup:

```bash
project/
├── config/
│   └── urls.py        # root router
└── products/
    └── urls.py        # app routes
```

!!! warning "Common Mistakes (Avoid These)"

    ❌ Using function-based views inside CBV URLs

    ❌ Forgetting .as_view()

    ❌ Not naming URLs

    ❌ Using id instead of pk

    ❌ Hardcoding success_url (use reverse_lazy)

## Conventional namespaces

> Using app_name in urls.py enables URL namespacing, which prevents name collisions and makes URL reversing deterministic and scalable.

- Bad Pattern:

```python
# blog/urls.py
urlpatterns = [
    path("", views.index, name="index"),
]

# shop/urls.py
urlpatterns = [
    path("", views.index, name="index"),
]
```

```html
{% url "index" %}
```

- Result:

    - Django cannot know which index to look for.
    - No Reverse Match url
    - Unexpected URL resolution
    - Accidental override

- Safe Pattern:

Blog App:

```python
# blog/urls.py

app_name = "blog"

urlpatterns = [
    path("", views.index, name="index"),
]
```

Shop App:

```python
# shop/urls.py

app_name = "shop"

urlpatterns = [
    path("", views.index, name="index"),
]
```

Templates:

```html
{% url "blog:index" %}
{% url "shop:index" %}
```

Result: Now resolution is explicit and unambiguous.

---

## Class Based Views and URL Mapping

- Model:

```python
# products/models.py
class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=10, decimal_places=2)
```

- View:

```python
# products/views.py
from django.views.generic import (
    ListView, DetailView, CreateView, UpdateView, DeleteView
)
from .models import Product

# ListView
class ProductListView(ListView):
    model = Product
    template_name = "products/product_list.html"

#DetailView
class ProductDetailView(DetailView):
    model = Product
    template_name = "products/product_detail.html"

# CreateView
class ProductDetailView(DetailView):
    model = Product
    template_name = "products/product_detail.html"

# UpdateView
class ProductUpdateView(UpdateView):
    model = Product
    fields = ["name", "price"]

# DeleteView
class ProductDeleteView(DeleteView):
    model = Product
    success_url = "/products/"
```

- URL:

```python
# products/urls.py
from django.urls import path
from .views import (
    ProductListView,
    ProductDetailView,
    ProductCreateView,
    ProductUpdateView,
    ProductDeleteView,
)

app_name = "products"

urlpatterns = [
    path("", ProductListView.as_view(), name="list"),
    path("<int:pk>/", ProductDetailView.as_view(), name="detail"),
    path("create/", ProductCreateView.as_view(), name="create"),
    path("<int:pk>/update/", ProductUpdateView.as_view(), name="update"),
    path("<int:pk>/delete/", ProductDeleteView.as_view(), name="delete"),
]
```

- Parameter Naming Rules:

| CBV          | Required URL Parameter |
| ------------ | ---------------------- |
| `DetailView` | `pk` or `slug`         |
| `UpdateView` | `pk` or `slug`         |
| `DeleteView` | `pk` or `slug`         |

- Bad Pattern:

```python
path("<int:id>/", ...)
```

- Safe Pattern:

```python
path("<int:pk>/", ...)
```

- Slug Based Mapping:

```python
# urls.py
path("<slug:slug>/", ProductDetailView.as_view())

# views.py
class ProductDetailView(DetailView):
    model = Product
    slug_field = "slug"
    slug_url_kwarg = "slug"
```
- Template Pattern:

```html
<a href="{% url 'products:detail' pk=product.id %}">
```