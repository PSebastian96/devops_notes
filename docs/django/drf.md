# Django REST Framework

## Overview

### Basics

Django REST Framework (DRF) is a toolkit built on Django for creating APIs.

The easiest way to understand DRF is to think of it as a layer that turns Django's models and Python objects into HTTP-accessible resources, usually represented as JSON.

An API, in this context, is an interface that allows another application to communicate with your Django application using HTTP.

Django REST Framework is usually used alongside with a Front End Framework (React, Vue, Svelte, Angular, Solid), which is used for the browser/mobile app to create the user interface, while backend api (django rest) stores and records data, front end displays that data.

- Traditional Django:

```text
Traditional Django

Browser
   ↓
URL
   ↓
Django View
   ↓
Model / Database
   ↓
HTML Template
   ↓
HTML response
```

- Django REST:

```text
Client
   ↓
HTTP request
   ↓
URL / Router
   ↓
DRF View
   ↓
Serializer
   ↓
Model / Database
   ↓
Serializer
   ↓
JSON response
```

---

### DRF Project Structure

```text
project/
│
├── config/
│   ├── settings
│   └── urls
│
├── products/
│   ├── models
│   │
│   ├── serializers
│   │
│   ├── views
│   │
│   ├── filters
│   │
│   ├── permissions
│   │
│   ├── urls
│   │
│   └── admin
│
├── accounts/
│   ├── models
│   ├── serializers
│   ├── views
│   ├── permissions
│   └── ...
│
└── ...
```

---

- DRF Concepts:

```text
Models
    What data exists?

Serializers
    What does that data look like through the API?
    Is incoming API data valid?

Views
    What should happen when an API request arrives?

URLs / Routers
    Which view handles this endpoint?

Filters
    Which records should be returned?

Pagination
    How many records should be returned at once?

Authentication
    Who is making the request?

Permissions
    What can they do?

Responses
    What does the client receive?
```

---

### DRF Workflow

```text
MODEL

Represents persistent data
        │
        ▼
SERIALIZER

Defines API representation
and validates input
        │
        ▼
VIEW

Handles HTTP requests
and coordinates the operation
        │
        ▼
URL

Makes the API accessible
```

---

### DRF Request Life Cycle

* For a request like: `GET /products/?category=electronics&max_price=100`

```text
Client
   │
   ▼
HTTP request
   │
   ▼
URL routing
   │
   ▼
DRF View / ViewSet
   │
   ▼
Authentication
   │
   ▼
Permissions
   │
   ▼
QuerySet
   │
   ▼
Filters
   │
   ├── category = electronics
   └── price <= 100
   │
   ▼
Pagination
   │
   ▼
Serializer
   │
   ▼
JSON representation
   │
   ▼
HTTP response
   │
   ▼
Client
```

---

## Reference

| Component             | Main responsibility                                               |
| --------------------- | ----------------------------------------------------------------- |
| Django Model          | Represents persistent application data                            |
| API                   | Interface through which clients communicate with your application |
| Endpoint              | A particular API URL/resource                                     |
| HTTP method           | Describes the requested operation                                 |
| Serializer            | Converts data to/from API representations                         |
| ModelSerializer       | Serializer specifically designed around Django models             |
| Serializer validation | Validates incoming API data                                       |
| View                  | Handles the HTTP/API operation                                    |
| ViewSet               | Groups related API operations for a resource                      |
| Router                | Generates URL routes for ViewSets                                 |
| QuerySet              | Represents database query operations                              |
| FilterSet             | Provides structured filtering of QuerySets                        |
| Search                | Finds records based on search criteria                            |
| Ordering              | Controls result ordering                                          |
| Pagination            | Limits results returned per request                               |
| Authentication        | Determines who is making the request                              |
| Permissions           | Determines what that requester can do                             |
| JSON                  | Common representation used for API requests/responses             |

## Views

## API views

DRF provides several approaches to building views.

From lower-level to higher-level abstraction:

```text
APIView
    ↓
Generic API views
    ↓
Mixins
    ↓
GenericViewSet
    ↓
ModelViewSet
```

The highest abstraction is not always needed.

The choice depends on how much control you need.

### APIView

Gives you relatively explicit control over HTTP methods.

Conceptually:

```text
APIView

GET
POST
PUT
PATCH
DELETE
```

You decide what each operation does.

---

### Generic views

Provide common CRUD behavior.

Conceptually:

```text
List
Create
Retrieve
Update
Destroy
ViewSet
```

Groups related operations around a resource.

For example:
```text
ProductViewSet

list
create
retrieve
update
partial_update
destroy
```

This fits naturally with routers.

---

## ViewSet + Router architecture

A common DRF production pattern is:

```text
URL Router
     │
     ▼
ProductViewSet
     │
     ├── list
     ├── create
     ├── retrieve
     ├── update
     ├── partial_update
     └── destroy
            │
            ▼
      ProductSerializer
            │
            ▼
        Product Model
```

The router automatically creates URL patterns corresponding to these operations.

Conceptually:

```text
GET     /products/
POST    /products/

GET     /products/42/
PUT     /products/42/
PATCH   /products/42/
DELETE  /products/42/
```

This is one of the reasons DRF can dramatically reduce repetitive CRUD code.

---

## Serializers and relationships

Suppose you have:

```text
Product
    category → Category

Category
    name
```

A serializer can represent the relationship in different ways.

For example:

```text
Product API

id
name
category

where category might contain:

category
    id
    name

Or it might simply contain:

category_id

Or:

category URL
```

The serializer determines how that relationship is represented externally.

This is another reason serializers are more than simple "JSON converters."

---

## Nested serializers

You can also represent related objects inside one another.

Conceptually:

```javascript
Product

{
    id
    name
    category
        id
        name
}
```

The serializer hierarchy becomes:

```text
ProductSerializer
      │
      └── CategorySerializer
```

This is useful, but you should avoid blindly nesting everything.

Deeply nested API responses can become:

```text
Product
  └── Category
       └── Department
            └── Company
                 └── ...
```

which can make APIs unnecessarily complicated and expensive.

---

## 2 Scoops of Django

Two Scoops of Django philosophy for DRF — short version

>Use Django/DRF's built-in conventions and abstractions first, keep responsibilities separated, and introduce custom architecture only when real complexity requires it.

In practice:

- Follow Django conventions — don't fight the framework.

- Keep apps focused — each app should represent a meaningful domain.

- Separate responsibilities — views handle HTTP, serializers handle representation/validation, models handle data/domain behavior, services handle complex workflows.

- Prefer built-ins — use ModelViewSet, ModelSerializer, DRF permissions, pagination, filtering, etc. before creating custom equivalents.

- Avoid premature abstraction — don't create services.py, selectors.py, custom base classes, etc. just because they might be useful later.

- Keep business logic out of API plumbing — important workflows shouldn't depend entirely on a specific HTTP endpoint.

- Optimize for maintainability — clear structure, loose coupling, high cohesion, testing, security, and explicit conventions.

- DRF should remain a layer, not your entire architecture — your domain shouldn't become tightly coupled to HTTP/DRF.

>Don't add an abstraction because you can. Add it because it solves a real problem.

- Two Scoops of Django in summary:

| File             | Responsibility                       | Typical contents                                                         | When needed                                                |
| ---------------- | ------------------------------------ | ------------------------------------------------------------------------ | ---------------------------------------------------------- |
| `models.py`      | **Data & domain models**             | Django models, fields, relationships, constraints                        | Almost always                                              |
| `views.py`       | **HTTP/API orchestration**           | `APIView`, `GenericAPIView`, `ViewSet`, request/response handling        | Almost always for APIs                                     |
| `serializers.py` | **API representation & validation**  | `Serializer`, `ModelSerializer`, validation, input/output transformation | Almost always for DRF APIs                                 |
| `urls.py`        | **URL routing**                      | `path()`, `include()`, routers                                           | Almost always                                              |
| `admin.py`       | **Django Admin configuration**       | `ModelAdmin`, admin registration                                         | When using Django Admin                                    |
| `services.py`    | **Business workflows/actions**       | Complex operations such as checkout, payment, registration               | Only when business logic becomes complex/reusable          |
| `selectors.py`   | **Complex data retrieval**           | Reusable/complex ORM queries                                             | Only when query logic warrants it                          |
| `permissions.py` | **Authorization**                    | Custom `BasePermission` classes                                          | When built-in permissions aren't enough                    |
| `tasks.py`       | **Background/async work**            | Celery tasks                                                             | When using Celery/background processing                    |
| `pagination.py`  | **Pagination behavior**              | Custom `PageNumberPagination`, `CursorPagination`                        | Only when DRF defaults aren't sufficient                   |
| `filters.py`     | **Advanced filtering**               | `FilterSet`, custom filter logic                                         | When filtering becomes complex                             |
| `exceptions.py`  | **API error handling**               | Custom exception handler, API error formatting                           | When you need consistent/custom errors                     |
| `middleware.py`  | **Request/response processing**      | Custom Django middleware                                                 | Only for cross-cutting HTTP concerns                       |
| `signals.py`     | **Event-driven model hooks**         | `post_save`, `pre_delete`, etc.                                          | Sparingly; avoid hiding important business logic           |
| `managers.py`    | **Model-level query interfaces**     | Custom model managers/querysets                                          | When reusable model-specific queries belong with the model |
| `apps.py`        | **Django app configuration**         | `AppConfig`, app initialization                                          | Normally created automatically                             |
| `tests.py`       | **Tests**                            | Unit/integration tests                                                   | Always, though you may instead use a `tests/` package      |
| `conftest.py`    | **Pytest configuration/fixtures**    | Shared pytest fixtures                                                   | If using pytest                                            |
| `celery.py`      | **Celery application configuration** | Celery app initialization/configuration                                  | If using Celery                                            |
| `wsgi.py`        | **WSGI deployment entry point**      | WSGI application                                                         | Traditional WSGI deployment                                |
| `asgi.py`        | **ASGI deployment entry point**      | ASGI application                                                         | ASGI/async deployment                                      |
| `manage.py`      | **Django management entry point**    | Django command configuration                                             | Django project standard                                    |

- SImple request life cycle example:

```text
                    HTTP Request
                         │
                         ▼
                      urls.py
                         │
                         ▼
                      views.py
                         │
              ┌──────────┴──────────┐
              ▼                     ▼
        serializers.py        permissions.py
              │
              ▼
         services.py
              │
         ┌────┴─────┐
         ▼          ▼
   selectors.py   models.py
                      │
                      ▼
                   Database


              Background work
                    │
                    ▼
                tasks.py
                    │
                    ▼
                  Celery
```

---

## SOLID Principle

| SOLID Principle               | How it maps to Django/DRF                                                                                                | Example                                                                                          |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------ |
| **S — Single Responsibility** | Keep models, serializers, views, services, selectors, tasks, and permissions focused                                     | View handles HTTP; service handles business operation                                            |
| **O — Open/Closed**           | Extend Django/DRF through serializers, permissions, pagination, custom actions, etc. instead of modifying framework code | Custom `Pagination` extending `PageNumberPagination`                                             |
| **L — Liskov Substitution**   | Subclasses should preserve the contract of their parent classes                                                          | Custom DRF permission behaves consistently with `BasePermission`                                 |
| **I — Interface Segregation** | Don't force components to depend on functionality they don't need                                                        | Separate serializers for read/write operations                                                   |
| **D — Dependency Inversion**  | Keep business logic independent from infrastructure where practical                                                      | Service layer depends on abstractions/interfaces rather than directly coupling workflows to HTTP |

---

### Single Responsibility 

- Instead of:

```python
class BookViewSet(ModelViewSet):

    def create(self, request):
        # validate request
        # create book
        # charge customer
        # send email
        # generate PDF
        # notify admin
        # update analytics
        ...
```

- Separate responsibilities:

```text
View
  ↓
Serializer
  ↓
Service
  ↓
Model
  ↓
Celery Task
```

- Example:

```python
class BookViewSet(ModelViewSet):

    def perform_create(self, serializer):
        book = create_book(
            user=self.request.user,
            data=serializer.validated_data,
        )

        notify_book_created.delay(book.id)

""" 
View        → HTTP concerns
Serializer  → validation
Service     → business operation
Model       → persistence/domain invariants
Celery      → asynchronous execution
"""
```

---

### Open/Closed

DRF is designed around extension.

```python
from rest_framework.pagination import PageNumberPagination


class StandardPagination(PageNumberPagination):

    page_size = 20

    page_size_query_param = "page_size"

    max_page_size = 100
```

Then configure:

```python
REST_FRAMEWORK = {
    "DEFAULT_PAGINATION_CLASS":
        "apps.core.pagination.StandardPagination",
}
```

You have:

```text
DRF behavior
      ↓
extend
      ↓
your behavior
```

rather than:

```text
DRF source
      ↓
modify
      ↓
potential framework problems
```

That's a good example of Open/Closed Principle.

---

### Liskov Substitution

DRF heavily relies on inheritance.

For example:

```python
class BookListView(ListCreateAPIView):
    ...
```

`ListCreateAPIView` itself builds on DRF's generic view hierarchy.


If you create:

```python
class MyBookListView(ListCreateAPIView):
    ...
```

your implementation should still behave according to the expectations of ListCreateAPIView.

You shouldn't suddenly make:

```text
GET /books/
```

return something completely incompatible with the expected API contract merely because you've subclassed the view.

The same applies to:

```text
BasePermission
BaseAuthentication
BaseSerializer
PageNumberPagination
APIView
```

The extension should preserve the behavioral contract of the base class.

---

### Interface Segregation

Read vs Write Serializers in DRF.

Might start like this:

```python
class BookSerializer(serializers.ModelSerializer):
    ...
```

But eventually your read representation may become very different from your write representation.

Instead of forcing one serializer to do everything:

```text
BookSerializer
    ├── create
    ├── update
    ├── nested representation
    ├── permissions
    ├── calculated fields
    └── different frontend representations
```

you can separate:

```python
class BookReadSerializer(serializers.ModelSerializer):
    ...


class BookWriteSerializer(serializers.ModelSerializer):
    ...
```

Now consumers depend only on the representation they actually need.

That's a practical application of Interface Segregation.

---

### Dependency Inversion

Consider:

```python
class OrderViewSet(ModelViewSet):

    def create(self, request):
        # create order
        # charge Stripe
        # send email
        # update inventory
        ...
```

The HTTP layer now knows too much.

Instead:

```python
class OrderViewSet(ModelViewSet):

    def perform_create(self, serializer):

        create_order(
            user=self.request.user,
            data=serializer.validated_data,
        )
```

Then:

```python
# services.py

def create_order(*, user, data):
    ...
```

Now your business operation isn't fundamentally dependent on DRF.

You can potentially invoke it from:

```text
DRF View
Celery Task
Django Admin
Management Command
Scheduled Job
```

without duplicating the business logic.

That's where Dependency Inversion and the Two Scoops philosophy can reinforce each other.

---

### Notes 

Where services.py and selectors.py fit.

This gives you a useful architecture:

```text
                    HTTP
                     │
                     ▼
               DRF ViewSet
                     │
          ┌──────────┴──────────┐
          ▼                     ▼
     Serializer            Permission
          │
          ▼
       Service
          │
     ┌────┴────┐
     ▼         ▼
   Model    External API
     │
     ▼
 Database
```

For reads:

```text
ViewSet
   ↓
Serializer
   ↓
Selector
   ↓
QuerySet
   ↓
Database
```

For asynchronous work:

```text
Service
   ↓
Celery Task
   ↓
External system
```

This separation is not mandatory Django architecture. It is an architectural technique that becomes valuable as business complexity increases.

---

1.Two Scoops tells you how to structure your Django project

This is the important distinction.

Two Scoops isn't another framework sitting on top of Django.

It's more like:

"Given the tools Django gives you, how should you organize a maintainable Django project?"

For example, instead of putting everything into one enormous views.py:

```text
views.py
    ↓
5000 lines of code
    ↓
business logic
database queries
permissions
API responses
emails
external API calls
```

you might structure things as:

```text
apps/
└── books/
    ├── models.py
    ├── services.py
    ├── selectors.py
    └── api/
        ├── serializers.py
        ├── views.py
        └── urls.py
```

The exact services.py / selectors.py structure isn't mandated by Django or even universally required by Two Scoops. It's an architectural choice that can help when complexity warrants it.

---

2.And SOLID helps explain why these patterns make sense

For example:

```text
DRF ViewSet
      ↓
Serializer
      ↓
Service
      ↓
Model
      ↓
Database
```

---

That's essentially applying separation of responsibilities.

Then Two Scoops gives you Django-specific guidance about where those things should live and how the project should be organized.v

| Layer       | Responsibility              |
| ----------- | --------------------------- |
| ViewSet     | HTTP/API orchestration      |
| Serializer  | Representation + validation |
| Service     | Business workflow           |
| Selector    | Complex read/query logic    |
| Model       | Domain/data behavior        |
| Celery task | Asynchronous execution      |

---
