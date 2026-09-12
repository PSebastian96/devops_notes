# `forms.py` notes

## Form vs ModelForm (Quick Notes)

| Feature              | `forms.Form`                                        | `forms.ModelForm`                             |
| -------------------- | --------------------------------------------------- | --------------------------------------------- |
| **Model knowledge**  | No knowledge of models                              | Tied to one model                             |
| **Field generation** | You define fields manually                          | Fields generated automatically from the model |
| **Saving**           | No automatic saving                                 | Automatic database persistence via `.save()`  |
| **Flexibility**      | Maximum flexibility                                 | More opinionated                              |
| **Constraints**      | You control validation and constraints              | Enforces model constraints                    |
| **Best for**         | Custom forms, search forms, filters, non-model data | Creating/updating model instances             |


---

## When to Use Which — Summary Table

| Scenario                   | Use `forms.Form` | Use `forms.ModelForm` |
| -------------------------- | ---------------- | --------------------- |
| No database interaction    | ✅                | ❌                     |
| Simple user input          | ✅                | ❌                     |
| Contact / Search / Login   | ✅                | ❌                     |
| CRUD operations            | ❌                | ✅                     |
| Admin-like interfaces      | ❌                | ✅                     |
| Single model create/update | ❌                | ✅                     |
| Multiple models involved   | ✅                | ❌                     |
| Full model validation      | ❌                | ✅                     |
| Custom save logic          | ⚠️ Manual         | ✅ Built-in            |
| DRY code                   | ❌                | ✅                     |

## Form Fields

### `forms.Form`

- `models.py`:

```python
from django.db import models


class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    stock = models.PositiveIntegerField(default=0)

    def __str__(self):
        return self.name
```

- `forms.py`:

```python
from django import forms


class ProductForm(forms.Form):
    name = forms.CharField(max_length=100)
    price = forms.DecimalField(max_digits=10, decimal_places=2)
    stock = forms.IntegerField(min_value=0)
```

#### 1. Text & Numeric Fields

- CharField
```python
forms.CharField(
    max_length=100,
    widget=forms.TextInput(attrs={
        "class": "primary",
        "placeholder": "Enter text"
    })
)
```

- CharField (Textarea)
```python
forms.CharField(
    widget=forms.Textarea(attrs={
        "rows": 3,
        "class": "primary"
    })
)
```

- IntegerField
```python
forms.IntegerField(
    widget=forms.NumberInput(attrs={
        "min": 0,
        "class": "input-number"
    })
)
```

- FloatField
```python
forms.FloatField(
    widget=forms.NumberInput(attrs={
        "step": "0.01"
    })
)
```

- DecimalField
```python
forms.DecimalField(
    max_digits=10,
    decimal_places=2,
    widget=forms.NumberInput(attrs={
        "step": "0.01"
    })
)
```

#### 2. Boolean Fields

- BooleanField
```python
forms.BooleanField(
    required=False,
    widget=forms.CheckboxInput(attrs={
        "class": "checkbox"
    })
)
```

#### 3. URL & Slug Fields

- EmailField
```python
forms.EmailField(
    widget=forms.EmailInput(attrs={
        "placeholder": "user@example.com"
    })
)
```

- URLField
```python
forms.URLField(
    widget=forms.URLInput(attrs={
        "placeholder": "https://example.com"
    })
)
```

- SlugField
```python
forms.SlugField(
    widget=forms.TextInput(attrs={
        "pattern": "[-a-zA-Z0-9_]+"
    })
)
```

#### 4. Date & Time Fields

- DateField
```python
forms.DateField(
    widget=forms.DateInput(attrs={
        "type": "date"
    })
)
```

- TimeField
```python
forms.TimeField(
    widget=forms.TimeInput(attrs={
        "type": "time"
    })
)
```

- DateTimeField
```python
forms.DateTimeField(
    widget=forms.DateTimeInput(attrs={
        "type": "datetime-local"
    })
)
```

- DurationField
```python
forms.DurationField(
    widget=forms.TextInput(attrs={
        "placeholder": "HH:MM:SS"
    })
)
```

#### 5. Choice Fields

- ChoiceField
```python
forms.ChoiceField(
    choices=[("a", "Option A"), ("b", "Option B")],
    widget=forms.Select(attrs={
        "class": "select"
    })
)
```

- MultipleChoiceField
```python
forms.MultipleChoiceField(
    choices=[("a", "A"), ("b", "B")],
    widget=forms.SelectMultiple(attrs={
        "size": 5
    })
)
```

- TypedChoiceField
```python
forms.TypedChoiceField(
    choices=[(1, "One"), (2, "Two")],
    coerce=int
)
```
#### 6. Model Choice Fields

- ModelChoiceField
```python
forms.ModelChoiceField(
    queryset=Category.objects.all(),
    widget=forms.Select(attrs={
        "class": "select"
    })
)
```

- ModelMultipleChoiceField
```python
forms.ModelMultipleChoiceField(
    queryset=Tag.objects.all(),
    widget=forms.SelectMultiple(attrs={
        "class": "multi-select"
    })
)
```

#### 7. File Fields

- FileField
```python
forms.FileField(
    widget=forms.ClearableFileInput(attrs={
        "class": "file-input"
    })
)
```

- ImageField
```python
forms.ImageField(
    widget=forms.ClearableFileInput(attrs={
        "accept": "image/*"
    })
)
```

#### 8. Specialized Fields

- UUIDField
```python
forms.UUIDField(
    widget=forms.TextInput(attrs={
        "readonly": True
    })
)
```

- GenericIPAddressField
```python
forms.GenericIPAddressField(
    widget=forms.TextInput(attrs={
        "placeholder": "192.168.1.1"
    })
)
```

- JSONField
```python
forms.JSONField(
    widget=forms.Textarea(attrs={
        "rows": 6,
        "class": "json-editor"
    })
)
```

## forms.ModelForm

- Django ModelForms = model-bound forms that automatically map model fields → form fields → widgets.

- Model fields define data structure and constraints; ModelForm translates them into form validation and HTML widgets.

- forms.ModelForm requires a Meta class to declare:

    model → which database model it is bound to

    fields / exclude → which model fields are exposed

    ModelForms support automatic persistence:

    form.save() creates or updates a database record

- Widgets, labels, help texts, and validation can be customized via Meta or overridden explicitly

- Use ModelForm when form data should map directly to a database model.

- Model = source of truth while ModelForm = UI + validation layer on top of the model

## ModelForm & Meta class

!!! tip "ModelForm Rules"

    ModelForms auto-generate fields from models, while widgets control how users interact with them.

    Widgets are optional — Django auto-selects defaults

    Meta.widgets overrides defaults cleanly

    HTML5 inputs (date, email, number) must be explicit

    Model constraints still apply (unique, null, blank, etc.)

#### 1. CRUD Example

- Model

```python
# products/models.py
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    description = models.TextField(blank=True)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    in_stock = models.BooleanField(default=True)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.name
```

- ModelForm

```python
# products/forms.py
from django import forms
from .models import Product

class ProductForm(forms.ModelForm):

    class Meta:
        model = Product
        fields = ["name", "description", "price", "in_stock"]

        widgets = {
            "name": forms.TextInput(attrs={
                "class": "input",
                "placeholder": "Product name"
            }),
            "description": forms.Textarea(attrs={
                "rows": 4,
                "class": "textarea"
            }),
            "price": forms.NumberInput(attrs={
                "step": "0.01",
                "class": "input"
            }),
            "in_stock": forms.CheckboxInput(attrs={
                "class": "checkbox"
            }),
        }
```

- Widget Mapping

| Model Field    | Form Field     | Widget          |
| -------------- | -------------- | --------------- |
| `CharField`    | `CharField`    | `TextInput`     |
| `TextField`    | `CharField`    | `Textarea`      |
| `DecimalField` | `DecimalField` | `NumberInput`   |
| `BooleanField` | `BooleanField` | `CheckboxInput` |

#### 2. Relations and File

- Model

```python
# accounts/models.py
from django.db import models
from django.contrib.auth.models import User

class User(AbstractUser):
    username = models.CharField(max_length=150, unique=True)
    first_name = models.CharField(max_length=150, blank=True)
    last_name = models.CharField(max_length=150, blank=True)
    email = models.EmailField(blank=True)

    is_staff = models.BooleanField(default=False)
    is_active = models.BooleanField(default=True)
    is_superuser = models.BooleanField(default=False)

    date_joined = models.DateTimeField(auto_now_add=True)

class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    bio = models.TextField(blank=True)
    avatar = models.ImageField(upload_to="avatars/", blank=True)
    birth_date = models.DateField(null=True, blank=True)

    def __str__(self):
        return self.user.username
```

- ModelForm

```python
# accounts/forms.py
from django import forms
from .models import Profile

class ProfileForm(forms.ModelForm):

    class Meta:
        model = Profile
        fields = ["bio", "avatar", "birth_date"]

        widgets = {
            "bio": forms.Textarea(attrs={
                "rows": 5
            }),
            "avatar": forms.ClearableFileInput(attrs={
                "accept": "image/*"
            }),
            "birth_date": forms.DateInput(attrs={
                "type": "date"
            }),
        }
```

- Widget Mapping

| Model Field  | Widget                  |
| ------------ | ----------------------- |
| `TextField`  | `Textarea`              |
| `ImageField` | `ClearableFileInput`    |
| `DateField`  | `DateInput (type=date)` |


#### 3. Order, Choices and Relations

- Model

```python
# orders/models.py
from django.db import models

class Order(models.Model):

    class Status(models.TextChoices):
        STATUS_CHOICES = [
            ("pending", "Pending"),
            ("paid", "Paid"),
            ("shipped", "Shipped"),
        ]

    customer_email = models.EmailField()
    status = models.CharField(max_length=20, choices=Status.choices)
    quantity = models.PositiveIntegerField()
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f"{self.customer_email} ({self.status})"
```

- ModelForm

```python
# orders/forms.py
from django import forms
from .models import Order

class OrderForm(forms.ModelForm):

    class Meta:
        model = Order
        fields = ["customer_email", "status", "quantity"]

        widgets = {
            "customer_email": forms.EmailInput(attrs={
                "placeholder": "customer@example.com"
            }),
            "status": forms.Select(),
            "quantity": forms.NumberInput(attrs={
                "min": 1
            }),
        }
```

- Widget Mapping

| Model Field            | Widget        |
| ---------------------- | ------------- |
| `EmailField`           | `EmailInput`  |
| `CharField (choices)`  | `Select`      |
| `PositiveIntegerField` | `NumberInput` |