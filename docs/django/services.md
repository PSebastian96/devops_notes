# `services.py`

## Overview

A **service** is a function or class that encapsulates a **business operation**.

Django does not have a built-in `services.py` convention. It is an application-level pattern used when business logic needs to be separated from views, models, forms, or serializers.

---

## Conventions

### 1. Services represent business actions

Prefer names that describe an operation:

```python
create_order()
cancel_order()
process_payment()
register_customer()
approve_application()
```

Avoid vague names:

```python
do_order()
handle_data()
process_stuff()
```

---

### 2. Keep services independent of HTTP

A service should generally not know about:

```python
request
HttpResponse
redirect()
request.POST
request.GET
```

Prefer:

```python
def create_order(customer, items):
    ...
```

instead of:

```python
def create_order(request):
    ...
```

This makes the service reusable from views, APIs, tasks, commands, etc.

---

### 3. Services should have one clear responsibility

Good:

```python
def create_order(customer, items):
    ...
```

Less desirable:

```python
def create_order_and_send_email_and_update_inventory_and_charge_card(...):
    ...
```

A service can coordinate several operations, but it should still represent **one coherent business operation**.

---

### 4. Don't create services for trivial operations

You don't need:

```python
def get_user(user_id):
    return User.objects.get(pk=user_id)
```

if that's all the function does.

Likewise, don't wrap every ORM query in a service simply for the sake of having a service layer.

---

## Conventional Pattern

A common Django application flow is:

```text
Request
   │
   ▼
 View
   │
   ├── Form / Serializer
   │
   ▼
Service
   │
   ├── Models
   ├── Database
   └── External APIs
   │
   ▼
Result
   │
   ▼
 View
   │
   ▼
Response
```

Example:

```python
# views.py

def checkout(request):
    order = place_order(
        customer=request.user,
        items=get_cart_items(request),
    )

    return redirect("order-detail", order.id)
```

```python
# services.py

from django.db import transaction


@transaction.atomic
def place_order(customer, items):
    order = Order.objects.create(
        customer=customer,
    )

    for item in items:
        order.items.create(
            product=item["product"],
            quantity=item["quantity"],
        )

    return order
```

The view handles **HTTP**.

The service handles the **business operation**.

---

## Services vs Utils

A common question is whether a function belongs in `services.py` or `utils.py`.

| Question                                      | Service |  Utility  |
| --------------------------------------------- | :-----: | :-------: |
| Represents a business action?                 |    ✅    |     ❌     |
| Coordinates multiple operations?              |    ✅    | Sometimes |
| Contains application-specific business rules? |    ✅    | Usually ❌ |
| Generic/reusable operation?                   |    ❌    |     ✅     |
| Independent of business/domain logic?         |    ❌    |     ✅     |
| Example: `create_order()`                     |    ✅    |     ❌     |
| Example: `calculate_tax()`                    |  Maybe  |   Maybe   |
| Example: `format_currency()`                  |    ❌    |     ✅     |
| Example: `slugify_text()`                     |    ❌    |     ✅     |
| Example: `send_invoice()`                     |    ✅    |     ❌     |

### Simple distinction

```text
Service
→ "Perform this business operation."

Utility
→ "Help me perform this generic operation."
```

---

## Examples

### Service

```python
def cancel_order(order):
    if order.status == "shipped":
        raise ValueError("Shipped orders cannot be cancelled.")

    order.status = "cancelled"
    order.save(update_fields=["status"])
```

This represents a **business operation**.

---

### Utility

```python
def format_currency(amount):
    return f"£{amount:.2f}"
```

This is a **generic helper**.

---

## Where Services Usually Live

For a small application:

```text
orders/
├── models.py
├── views.py
├── forms.py
├── services.py
└── urls.py
```

As the application grows:

```text
orders/
├── models.py
├── views.py
├── forms.py
├── services/
│   ├── __init__.py
│   ├── orders.py
│   └── payments.py
└── urls.py
```

Don't create a service package prematurely.

Start with `services.py` and split it when it becomes large.

---

## Services and Transactions

Services are a natural place for transactions when a business operation contains multiple database changes that must succeed or fail together.

```python
from django.db import transaction


@transaction.atomic
def transfer_money(source, destination, amount):
    source.balance -= amount
    source.save()

    destination.balance += amount
    destination.save()
```

Conceptually:

```text
BEGIN
  │
  ├── Update account A
  ├── Update account B
  │
  ├── Success → COMMIT
  │
  └── Failure → ROLLBACK
```

Don't automatically use `transaction.atomic` for every service. Use it when the operation requires **atomicity**.

---

## Services don't have to be classes

Functions are often enough:

```python
def create_order(...):
    ...
```

You don't need:

```python
class OrderService:
    ...
```

unless a class provides a meaningful benefit, such as maintaining dependencies/state or grouping closely related operations.

---

## Services don't replace models

Models can still contain model-specific behavior:

```python
order.cancel()
```

A service is more appropriate when coordinating a larger operation:

```python
cancel_order(order)
```

Think:

```text
Model
→ What can this object do?

Service
→ What business operation needs to happen?
```

---

## Services don't mean "all business logic"

Don't turn this:

```text
services.py
```

into:

```text
services.py
└── 2,000 lines of everything
```

Use clear modules when necessary:

```text
services/
├── orders.py
├── payments.py
└── customers.py
```

---

## Decision Matrix

Use this as a quick rule:

| If the code...                    | Put it in                                 |
| --------------------------------- | ----------------------------------------- |
| Handles HTTP request/response     | `views.py`                                |
| Validates user input              | `forms.py` / serializers                  |
| Describes model-specific behavior | `models.py`                               |
| Performs a business operation     | `services.py`                             |
| Coordinates multiple models       | `services.py`                             |
| Requires a transaction            | Usually `services.py`                     |
| Is generic and reusable           | `utils.py`                                |
| Is a simple ORM query             | Usually directly in the appropriate layer |
| Is trivial                        | Don't create an abstraction               |