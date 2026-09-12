# 'admin.py' notes

## Overview

!!! danger "Core Principles"

    1.Performance first — Admin can degrade quickly with scale.

    2.Security always — It is effectively a database control panel.

    3.Separation of concerns — Admin config ≠ domain logic.

    4.Optimize for staff efficiency — It is a productivity tool.

    5.Avoid over-customization — Keep upgrade paths smooth.

!!! abstract "Internal Concepts"

    | Component    | Responsibility                  |
    | ------------ | ------------------------------- |
    | `Model`      | Defines schema + business logic |
    | `ModelAdmin` | Defines admin behavior          |
    | `AdminSite`  | Registry + routing              |
    | Forms        | Validation + rendering          |
    | ORM          | Database abstraction            |
    | Auth System  | Access control                  |

!!! abstract "Admin Forms"

    - Create `admin_forms.py` to create custom forms.

    - Use `forms.ModelForm` to connect to Models.

    - Example:

        - `admin_forms.py`

        ```python
        class PostAdminForm(forms.ModelForm):
            class Meta:
                model = Post
                fields = '__all__'
        ```

        - `admin.py`

        ```python
        @admin.register(Post)
        class PostAdmin(admin.AdminModel):
            form = PostAdminForm
            list_display = ('title','author','status')
        ```

!!! tip "Display Data Information"

    - models.py

        ```python
        class Post(models.Model):
            title = models.CharField(max_length=200)
            content = models.TextField()

            class Meta:
                verbose_name = 'Article'
                verbose_name_plural = 'Articles'
        ```

    - apps.py

        ```python
        class BlogConfig(AppConfig):
            name = 'blog'
            verbose_name = 'Blog Management'
        ```        
    
    - admin sidebar result:

        Blog Management

            - Articels
            - Comments

    **They are idependent from eachother, app section name defined by `apps.py` and `models.py ` define model labels in admin.py and admin view.**

---

### Define a Model

```python
class Product(models.Model):
    name = models.CharField(max_length=200, db_index=True)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    active = models.BooleanField(default=True)

    class Meta:
        ordering = ["name"]

    def __str__(self):
        return self.name
```

- Make sure to perform migrations:

```bash
python manage.py makemigrations
python manage.py migrate
```

### Create ModelAdmin Class

```python
@admin.register(Product)
class ProductAdmin(admin.ModelAdmin):

    # List View
    list_display = ("name", "price", "active")
    list_filter = ("active",)
    search_fields = ("name",)
    ordering = ("name",)
    list_per_page = 50

    # Performance
    list_select_related = ()

    # Form Layout
    fields = ("name", "price", "active")
    readonly_fields = ()

    # Bulk Actions
    actions = ["mark_inactive"]

    @admin.action(description="Mark selected products as inactive")
    def mark_inactive(self, request, queryset):
        queryset.update(active=False)
```

## Attributes & Methods

### Most Common Attributes

- Model

```python
from django.db import models


class Category(models.Model):
    name = models.CharField(max_length=120)

    def __str__(self):
        return self.name


class Tag(models.Model):
    name = models.CharField(max_length=80)

    def __str__(self):
        return self.name


class Product(models.Model):
    name = models.CharField(max_length=200)
    slug = models.SlugField(unique=True)
    category = models.ForeignKey(Category, on_delete=models.CASCADE)
    tags = models.ManyToManyField(Tag, blank=True)
    description = models.TextField()
    price = models.DecimalField(max_digits=10, decimal_places=2)
    active = models.BooleanField(default=True)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    def __str__(self):
        return self.name


class ProductImage(models.Model):
    product = models.ForeignKey(Product, on_delete=models.CASCADE)
    image = models.ImageField(upload_to="products/")
    alt_text = models.CharField(max_length=200)

    def __str__(self):
        return self.alt_text
```

- Admin (**for real projects, create `admin_forms.py` file to create forms used in admin view**)

```python
from django.contrib import admin
from django import forms
from .models import Product, Category, Tag, ProductImage


# --------------------------------------------------
# Custom Form
# --------------------------------------------------
class ProductAdminForm(forms.ModelForm):
    class Meta:
        model = Product
        fields = "__all__"

    def clean_price(self):
        price = self.cleaned_data["price"]
        if price < 0:
            raise forms.ValidationError("Price cannot be negative.")
        return price


# --------------------------------------------------
# Inline Configuration
# --------------------------------------------------
class ProductImageInline(admin.TabularInline):
    model = ProductImage
    extra = 1


# --------------------------------------------------
# ModelAdmin Implementation
# --------------------------------------------------
@admin.register(Product)
class ProductAdmin(admin.ModelAdmin):

    # --------------------------------------------------
    # LIST VIEW
    # --------------------------------------------------
    list_display = (
        "name",
        "category",
        "price",
        "active",
        "created_at",
    )
    list_filter = (
        "active",
        "category",
        "created_at",
    )
    search_fields = (
        "name",
        "description",
        "category__name",
    )
    ordering = ("-created_at",)
    date_hierarchy = "created_at"
    list_per_page = 25

    # Allow quick inline editing directly from list view
    list_editable = ("price", "active")

    # --------------------------------------------------
    # FORM CONFIGURATION
    # --------------------------------------------------
    form = ProductAdminForm

    prepopulated_fields = {
        "slug": ("name",)
    }

    readonly_fields = (
        "created_at",
        "updated_at",
    )

    fieldsets = (
        ("Basic Information", {
            "fields": ("name", "slug", "category", "tags")
        }),
        ("Content", {
            "fields": ("description",)
        }),
        ("Pricing & Status", {
            "fields": ("price", "active")
        }),
        ("Timestamps", {
            "fields": ("created_at", "updated_at"),
        }),
    )

    # --------------------------------------------------
    # RELATION HANDLING
    # --------------------------------------------------
    autocomplete_fields = ("category", "tags")

    # --------------------------------------------------
    # INLINE MODELS
    # --------------------------------------------------
    inlines = (ProductImageInline,)

    # --------------------------------------------------
    # BULK ACTIONS
    # --------------------------------------------------
    actions = ["mark_as_active", "mark_as_inactive"]

    @admin.action(description="Mark selected products as Active")
    def mark_as_active(self, request, queryset):
        queryset.update(active=True)

    @admin.action(description="Mark selected products as Inactive")
    def mark_as_inactive(self, request, queryset):
        queryset.update(active=False)
```

!!! example "Example Demonstrates"

    - List-Level Controls

        - Search across related fields

        - Sidebar filters

        - Date drill-down navigation

        - Inline editing (list_editable)

        - Pagination tuning

    - Form-Level Controls

        - Custom validation via form

        - Auto-slug generation

        - Structured layout using fieldsets

        - Read-only audit fields

    - Relational UX

        - Autocomplete for ForeignKey & ManyToMany

        - Inline child model editing

    - Bulk Operations

        - Multi-record status updates

---

### All attributes

```python
from django.contrib import admin
from .models import Product


@admin.register(Product)
class ProductAdmin(admin.ModelAdmin):

    # --------------------------------------------------
    # LIST VIEW CONFIGURATION (Changelist Page)
    # Controls table display and navigation behavior
    # --------------------------------------------------
    list_display = ("name", "price", "active")
    list_display_links = ("name",)
    list_editable = ()
    list_filter = ("active",)
    list_select_related = ()
    list_per_page = 50
    list_max_show_all = 200
    list_display_links = None
    search_fields = ("name",)
    search_help_text = "Search by product name"
    ordering = ("name",)
    date_hierarchy = None
    empty_value_display = "-"
    sortable_by = ("name", "price")
    show_full_result_count = True
    preserve_filters = True

    # --------------------------------------------------
    # FORM LAYOUT CONFIGURATION (Create / Update View)
    # Controls field rendering and structure
    # --------------------------------------------------
    fields = ("name", "price", "active")
    exclude = ()
    fieldsets = None
    readonly_fields = ()
    prepopulated_fields = {}
    filter_horizontal = ()
    filter_vertical = ()
    autocomplete_fields = ()
    raw_id_fields = ()
    radio_fields = {}
    view_on_site = True

    # --------------------------------------------------
    # FORM BEHAVIOR & VALIDATION
    # Allows custom forms and overrides
    # --------------------------------------------------
    form = None
    add_form_template = None
    change_form_template = None
    change_list_template = None
    delete_confirmation_template = None
    delete_selected_confirmation_template = None
    object_history_template = None
    popup_response_template = None

    # --------------------------------------------------
    # INLINE MODEL CONFIGURATION
    # Enables parent-child editing
    # --------------------------------------------------
    inlines = ()

    # --------------------------------------------------
    # ACTIONS (Bulk Operations)
    # --------------------------------------------------
    actions = ()
    actions_on_top = True
    actions_on_bottom = False
    actions_selection_counter = True
```