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

- Full example

```python
# urls.py
from django.urls import include, path
from rest_framework.routers import DefaultRouter

from .views import ProductViewSet


# Create router
router = DefaultRouter()

# Register ViewSet
router.register(
    "products",
    ProductViewSet,
    basename="product",
)


# App URL patterns
urlpatterns = [
    path("", include(router.urls)),
]
```

- viewset

```python
from rest_framework.viewsets import ModelViewSet

from .models import Product
from .serializers import ProductSerializer


class ProductViewSet(ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
```

- config urls.py

```python
from django.urls import include, path

urlpatterns = [
    path(
        "api/v1/",
        include("products.urls"),
    ),
]
```

---

## Authentication

---

### Auth

#### Overview

* Typical setup flow

1.Create/configure the custom User model
    - Usually subclass AbstractUser.
    - Set AUTH_USER_MODEL early, before creating migrations.

2.Install and configure allauth
    - Add allauth and allauth.account.
    - Configure authentication methods, signup fields, email verification, etc.
    - Add allauth.urls.

3.Add DRF
    - Add rest_framework.
    - Decide how API authentication will work.

4.Choose the authentication strategy
    - Same Django website + API → session authentication can be sufficient.
    - SPA/mobile/separate frontend → token/JWT authentication is more typical.
    - With current allauth, the headless integration can provide authentication flows and JWT authentication for APIs.

5.Configure allauth headless
    - Add allauth.headless.
    - Add the headless URL endpoint.
    - Use its JWT authentication class in DRF.

6.Configure DRF
Set default authentication classes.
Set default permission classes.
Individual views can override these when necessary.

7.Build API

```text
models.py
    ↓
serializers.py
    ↓
views.py / viewsets.py
    ↓
urls.py / routers
```

8.Protect API endpoints

    - IsAuthenticated for private endpoints.
    - AllowAny only where authentication isn't required.

9.Add API concerns separately

`filters.py` → filtering
`pagination.py` → pagination
`services.py` → business workflows
`permissions.py` → custom authorization rules

These settings belong to different layers:

```text
Django
├── AUTH_USER_MODEL
└── AUTHENTICATION_BACKENDS

Allauth
├── ACCOUNT_LOGIN_METHODS
├── ACCOUNT_SIGNUP_FIELDS
├── ACCOUNT_EMAIL_VERIFICATION
└── ACCOUNT_UNIQUE_EMAIL

DRF
├── DEFAULT_AUTHENTICATION_CLASSES
└── DEFAULT_PERMISSION_CLASSES

Allauth Headless
├── JWTTokenAuthentication
├── headless URLs
└── headless-specific settings
```

---

#### Config

- `settings.py`

```python
INSTALLED_APPS = [
    # Django
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.staticfiles",

    # Allauth
    "allauth",
    "allauth.account",
    "allauth.headless", # JWT

    # DRF
    "rest_framework",

    # Local apps
    "accounts",
    "products",
]

AUTH_USER_MODEL = "accounts.User"

AUTHENTICATION_BACKENDS = [
    "django.contrib.auth.backends.ModelBackend",
    "allauth.account.auth_backends.AuthenticationBackend",
]

ACCOUNT_LOGIN_METHODS = {"username"}

ACCOUNT_SIGNUP_FIELDS = [
    "username*",
    "email*",
    "password1*",
    "password2*",
]

ACCOUNT_EMAIL_VERIFICATION = "mandatory"
ACCOUNT_UNIQUE_EMAIL = True
ACCOUNT_PREVENT_ENUMERATION = True
ACCOUNT_SESSION_REMEMBER = None
ACCOUNT_LOGOUT_ON_GET = False

REST_FRAMEWORK = {
    "DEFAULT_AUTHENTICATION_CLASSES": [
        "allauth.headless.contrib.rest_framework.authentication.JWTTokenAuthentication",
    ],
    "DEFAULT_PERMISSION_CLASSES": [
        "rest_framework.permissions.IsAuthenticated",
    ],
}
```

- `urls.py`

```python
urlpatterns = [
    path("accounts/", include("allauth.urls")),
    path("_allauth/", include("allauth.headless.urls")),
    path("api/v1/products/", include("products.urls")),
]
```

---

#### Config Summary

| Variable                         | Typical value                                         | Purpose                                           |
| -------------------------------- | ----------------------------------------------------- | ------------------------------------------------- |
| `AUTH_USER_MODEL`                | `"accounts.User"`                                     | Tells Django which User model to use              |
| `AUTHENTICATION_BACKENDS`        | `ModelBackend` + `allauth...AuthenticationBackend`    | Determines how users authenticate                 |
| `ACCOUNT_LOGIN_METHODS`          | `{"username"}`                                        | Determines what users can log in with             |
| `ACCOUNT_SIGNUP_FIELDS`          | `["username*", "email*", "password1*", "password2*"]` | Defines signup form fields                        |
| `ACCOUNT_EMAIL_VERIFICATION`     | `"mandatory"`                                         | Requires email verification                       |
| `ACCOUNT_UNIQUE_EMAIL`           | `True`                                                | Prevents duplicate email addresses                |
| `ACCOUNT_PREVENT_ENUMERATION`    | `True`                                                | Reduces account-enumeration leakage               |
| `ACCOUNT_SESSION_REMEMBER`       | `None`                                                | Lets the login form determine session persistence |
| `ACCOUNT_LOGOUT_ON_GET`          | `False`                                               | Requires POST for logout                          |
| `REST_FRAMEWORK`                 | `{...}`                                               | Global DRF configuration                          |
| `DEFAULT_AUTHENTICATION_CLASSES` | `JWTTokenAuthentication`                              | How DRF identifies the API user                   |
| `DEFAULT_PERMISSION_CLASSES`     | `IsAuthenticated`                                     | Makes API endpoints private by default            |
| `HEADLESS_ONLY`                  | `True` or omitted                                     | Whether allauth is intended to be headless-only   |
| `HEADLESS_SERVE_SPECIFICATION`   | `True` or omitted                                     | Exposes the headless API specification            |

---

### Permissions

---

### JWT

#### Session vs JWT

|                             | Session authentication                            | JWT authentication                         |
| --------------------------- | ------------------------------------------------- | ------------------------------------------ |
| Authentication state        | Server-side session                               | Token held by client                       |
| Client sends                | Session cookie                                    | `Authorization: Bearer <token>`            |
| DRF class                   | `SessionAuthentication`                           | JWT authentication class                   |
| Django `request.user`       | Yes                                               | Yes                                        |
| CSRF protection             | Important for browser requests                    | Generally not based on Django session CSRF |
| Browser website             | Excellent fit                                     | Works, but often unnecessary               |
| SPA                         | Possible, but requires CSRF/cookie considerations | Common                                     |
| Mobile app                  | Less convenient                                   | Common                                     |
| Separate frontend/backend   | More configuration around cookies/CORS/CSRF       | Usually simpler                            |
| Token revocation            | Easy: invalidate session                          | More complicated, depending on JWT design  |
| Server-side session storage | Required                                          | Not required for access-token validation   |
| Stateless API               | No                                                | Usually yes                                |

---

#### Config JWT

- `settings.py`

```python
from pathlib import Path
import os


BASE_DIR = Path(__file__).resolve().parent.parent


# ============================================================
# SECURITY
# ============================================================

SECRET_KEY = os.environ["DJANGO_SECRET_KEY"]

DEBUG = os.environ.get("DJANGO_DEBUG", "False") == "True"

ALLOWED_HOSTS = [
    "localhost",
    "127.0.0.1",
]


# ============================================================
# APPLICATIONS
# ============================================================

INSTALLED_APPS = [
    # Django
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",

    # Allauth
    "allauth",
    "allauth.account",
    "allauth.headless",

    # Django REST Framework
    "rest_framework",

    # Local apps
    "accounts",
    "products",
]


# ============================================================
# MIDDLEWARE
# ============================================================

MIDDLEWARE = [
    "django.middleware.security.SecurityMiddleware",

    "django.contrib.sessions.middleware.SessionMiddleware",

    "django.middleware.common.CommonMiddleware",

    "django.middleware.csrf.CsrfViewMiddleware",

    "django.contrib.auth.middleware.AuthenticationMiddleware",

    "django.contrib.messages.middleware.MessageMiddleware",

    "django.middleware.clickjacking.XFrameOptionsMiddleware",
]


# ============================================================
# URLS
# ============================================================

ROOT_URLCONF = "config.urls"


# ============================================================
# TEMPLATES
# ============================================================

TEMPLATES = [
    {
        "BACKEND": "django.template.backends.django.DjangoTemplates",

        "DIRS": [
            BASE_DIR / "templates",
        ],

        "APP_DIRS": True,

        "OPTIONS": {
            "context_processors": [
                "django.template.context_processors.request",

                "django.contrib.auth.context_processors.auth",

                "django.contrib.messages.context_processors.messages",

                "django.template.context_processors.static",
            ],
        },
    },
]


# ============================================================
# WSGI
# ============================================================

WSGI_APPLICATION = "config.wsgi.application"


# ============================================================
# DATABASE
# ============================================================

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.sqlite3",
        "NAME": BASE_DIR / "db.sqlite3",
    }
}


# ============================================================
# CUSTOM USER
# ============================================================

AUTH_USER_MODEL = "accounts.User"


# ============================================================
# AUTHENTICATION BACKENDS
# ============================================================

AUTHENTICATION_BACKENDS = [
    "allauth.account.auth_backends.AuthenticationBackend",
]


# ============================================================
# DJANGO-ALLAUTH
# ============================================================

ACCOUNT_LOGIN_METHODS = {"username"}

ACCOUNT_SIGNUP_FIELDS = [
    "username*",
    "email*",
    "password1*",
    "password2*",
]

ACCOUNT_EMAIL_VERIFICATION = "mandatory"

ACCOUNT_UNIQUE_EMAIL = True

ACCOUNT_PREVENT_ENUMERATION = True

ACCOUNT_SESSION_REMEMBER = None

ACCOUNT_LOGOUT_ON_GET = False


# ============================================================
# ALLAUTH HEADLESS
# ============================================================

HEADLESS_ONLY = True


# ============================================================
# ALLAUTH JWT
# ============================================================

HEADLESS_JWT_ALGORITHM = "RS256"

HEADLESS_JWT_PRIVATE_KEY = os.environ["ALLAUTH_JWT_PRIVATE_KEY"]

HEADLESS_JWT_ACCESS_TOKEN_EXPIRES_IN = 300

HEADLESS_JWT_REFRESH_TOKEN_EXPIRES_IN = 86400

HEADLESS_JWT_AUTHORIZATION_HEADER_SCHEME = "Bearer"

HEADLESS_JWT_STATEFUL_VALIDATION_ENABLED = False

HEADLESS_JWT_ROTATE_REFRESH_TOKEN = True


# ============================================================
# DJANGO REST FRAMEWORK
# ============================================================

REST_FRAMEWORK = {
    "DEFAULT_AUTHENTICATION_CLASSES": [
        "allauth.headless.contrib.rest_framework.authentication.JWTTokenAuthentication",
    ],

    "DEFAULT_PERMISSION_CLASSES": [
        "rest_framework.permissions.IsAuthenticated",
    ],
}


# ============================================================
# PASSWORD VALIDATION
# ============================================================

AUTH_PASSWORD_VALIDATORS = [
    {
        "NAME": (
            "django.contrib.auth.password_validation."
            "UserAttributeSimilarityValidator"
        ),
    },
    {
        "NAME": (
            "django.contrib.auth.password_validation."
            "MinimumLengthValidator"
        ),
    },
    {
        "NAME": (
            "django.contrib.auth.password_validation."
            "CommonPasswordValidator"
        ),
    },
    {
        "NAME": (
            "django.contrib.auth.password_validation."
            "NumericPasswordValidator"
        ),
    },
]


# ============================================================
# INTERNATIONALIZATION
# ============================================================

LANGUAGE_CODE = "en-us"

TIME_ZONE = "UTC"

USE_I18N = True

USE_TZ = True


# ============================================================
# STATIC FILES
# ============================================================

STATIC_URL = "static/"

STATIC_ROOT = BASE_DIR / "staticfiles"


# ============================================================
# MEDIA FILES
# ============================================================

MEDIA_URL = "media/"

MEDIA_ROOT = BASE_DIR / "media"


# ============================================================
# EMAIL
# ============================================================

EMAIL_BACKEND = (
    "django.core.mail.backends.console.EmailBackend"
)


# ============================================================
# DEFAULT PRIMARY KEY
# ============================================================

DEFAULT_AUTO_FIELD = "django.db.models.BigAutoField"
```

---

- Common JWT settings:

| Setting                                    |              Value | Meaning                                           |
| ------------------------------------------ | -----------------: | ------------------------------------------------- |
| `HEADLESS_JWT_ALGORITHM`                   |          `"RS256"` | RSA asymmetric signing                            |
| `HEADLESS_JWT_PRIVATE_KEY`                 | Environment secret | Private signing key                               |
| `HEADLESS_JWT_ACCESS_TOKEN_EXPIRES_IN`     |              `300` | 5-minute access token                             |
| `HEADLESS_JWT_REFRESH_TOKEN_EXPIRES_IN`    |            `86400` | 24-hour refresh token                             |
| `HEADLESS_JWT_AUTHORIZATION_HEADER_SCHEME` |         `"Bearer"` | `Authorization: Bearer ...`                       |
| `HEADLESS_JWT_STATEFUL_VALIDATION_ENABLED` |            `False` | JWT isn't checked against the originating session |
| `HEADLESS_JWT_ROTATE_REFRESH_TOKEN`        |             `True` | Refresh tokens are rotated                        |

---

```python
HEADLESS_JWT_ALGORITHM = "RS256"

HEADLESS_JWT_PRIVATE_KEY = """
-----BEGIN PRIVATE KEY-----
...
-----END PRIVATE KEY-----
"""

HEADLESS_JWT_ACCESS_TOKEN_EXPIRES_IN = 300

HEADLESS_JWT_REFRESH_TOKEN_EXPIRES_IN = 86400

HEADLESS_JWT_AUTHORIZATION_HEADER_SCHEME = "Bearer"

HEADLESS_JWT_STATEFUL_VALIDATION_ENABLED = False

HEADLESS_JWT_ROTATE_REFRESH_TOKEN = True

REST_FRAMEWORK = {
    "DEFAULT_AUTHENTICATION_CLASSES": [
         # enable JWT
        "allauth.headless.contrib.rest_framework.authentication.JWTTokenAuthentication",
    ],
    "DEFAULT_PERMISSION_CLASSES": [
        "rest_framework.permissions.IsAuthenticated",
    ],
}
```

The documented defaults are RS256, 300 seconds for access tokens, 86400 seconds for refresh tokens, Bearer, stateful validation disabled, and refresh-token rotation enabled.

For an asymmetric algorithm such as RS256, allauth signs JWTs with the configured private key. The documentation shows generating an RSA private key with OpenSSL.

Don't commit the private key to Git. In production, load it from an environment variable or secret-management system.

---

#### Config Summary

| Variable                                   | Typical value                                         | Layer          | Purpose                                                 |
| ------------------------------------------ | ----------------------------------------------------- | -------------- | ------------------------------------------------------- |
| `AUTH_USER_MODEL`                          | `"accounts.User"`                                     | Django         | Custom user model                                       |
| `AUTHENTICATION_BACKENDS`                  | `ModelBackend` + allauth backend                      | Django/allauth | Authentication backends                                 |
| `ACCOUNT_LOGIN_METHODS`                    | `{"username"}`                                        | allauth        | Login identifier                                        |
| `ACCOUNT_SIGNUP_FIELDS`                    | `["username*", "email*", "password1*", "password2*"]` | allauth        | Signup fields                                           |
| `ACCOUNT_EMAIL_VERIFICATION`               | `"mandatory"`                                         | allauth        | Require email verification                              |
| `ACCOUNT_UNIQUE_EMAIL`                     | `True`                                                | allauth        | Unique email addresses                                  |
| `ACCOUNT_PREVENT_ENUMERATION`              | `True`                                                | allauth        | Reduce account enumeration                              |
| `HEADLESS_JWT_ALGORITHM`                   | `"RS256"`                                             | allauth JWT    | JWT signing algorithm                                   |
| `HEADLESS_JWT_PRIVATE_KEY`                 | RSA private key                                       | allauth JWT    | Signs JWTs                                              |
| `HEADLESS_JWT_ACCESS_TOKEN_EXPIRES_IN`     | `300`                                                 | allauth JWT    | Access-token lifetime, seconds                          |
| `HEADLESS_JWT_REFRESH_TOKEN_EXPIRES_IN`    | `86400`                                               | allauth JWT    | Refresh-token lifetime, seconds                         |
| `HEADLESS_JWT_AUTHORIZATION_HEADER_SCHEME` | `"Bearer"`                                            | allauth JWT    | `Authorization` header scheme                           |
| `HEADLESS_JWT_STATEFUL_VALIDATION_ENABLED` | `False`                                               | allauth JWT    | Whether JWT validity depends on its originating session |
| `HEADLESS_JWT_ROTATE_REFRESH_TOKEN`        | `True`                                                | allauth JWT    | Rotate refresh token when refreshing                    |
| `REST_FRAMEWORK`                           | `{...}`                                               | DRF            | Global DRF configuration                                |
| `DEFAULT_AUTHENTICATION_CLASSES`           | `JWTTokenAuthentication`                              | DRF            | How API requests authenticate                           |
| `DEFAULT_PERMISSION_CLASSES`               | `IsAuthenticated`                                     | DRF            | Default API access policy                               |

---