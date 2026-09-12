# `views.py` notes

## Class Based Views (CBV)

- Best Practice Summary:

| Recommendation               | Reason                         |
| ---------------------------- | ------------------------------ |
| Override `get_queryset()`    | Cleaner than modifying `model` |
| Override `form_valid()`      | Inject business logic          |
| Avoid heavy logic in `get()` | Use model/service layer        |
| Use `reverse_lazy`           | Prevent import-time errors     |
| Prefer `context_object_name` | Clearer templates              |

- Model

```python
# models.py
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title
```

- Form

```python
# forms.py
from django import forms
from .models import Post

class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ["title", "content"]
```

- Views

```python
# views.py
from django.urls import reverse_lazy
from django.views.generic import (
    ListView,
    DetailView,
    CreateView,
    UpdateView,
    DeleteView
)
from .models import Post
from .forms import PostForm

class PostCreateView(CreateView):
    model = Post
    form_class = PostForm
    template_name = "posts/post_form.html"
    success_url = reverse_lazy("post_list")

class PostListView(ListView):
    model = Post
    template_name = "posts/post_list.html"
    context_object_name = "posts"

class PostDetailView(DetailView):
    model = Post
    template_name = "posts/post_detail.html"
    context_object_name = "post"

class PostUpdateView(UpdateView):
    model = Post
    form_class = PostForm
    template_name = "posts/post_form.html"

    def get_success_url(self):
        return reverse_lazy("post_detail", kwargs={"pk": self.object.pk})

class PostDeleteView(DeleteView):
    model = Post
    template_name = "posts/post_confirm_delete.html"
    success_url = reverse_lazy("post_list")
```

- URL

```python
# urls.py
from django.urls import path
from .views import (
    PostListView,
    PostDetailView,
    PostCreateView,
    PostUpdateView,
    PostDeleteView
)

urlpatterns = [
    path("", PostListView.as_view(), name="post_list"),
    path("create/", PostCreateView.as_view(), name="post_create"),
    path("<int:pk>/", PostDetailView.as_view(), name="post_detail"),
    path("<int:pk>/edit/", PostUpdateView.as_view(), name="post_update"),
    path("<int:pk>/delete/", PostDeleteView.as_view(), name="post_delete"),
]
```

---

## TemplateView

| Class          | Purpose                | Key Attributes  | Built-in Methods                             |
| -------------- | ---------------------- | --------------- | -------------------------------------------- |
| `TemplateView` | Render static template | `template_name` | `get_context_data()`, `render_to_response()` |

## ListView

| Attribute             | Description          |
| --------------------- | -------------------- |
| `model`               | Model class          |
| `queryset`            | Custom queryset      |
| `template_name`       | Template override    |
| `context_object_name` | Rename `object_list` |
| `paginate_by`         | Enable pagination    |
| `ordering`            | Default ordering     |

| Method               | Purpose              |
| -------------------- | -------------------- |
| `get_queryset()`     | Customize query      |
| `get_context_data()` | Add extra context    |
| `get_paginate_by()`  | Customize pagination |

## DetailView

| Attribute             | Description       |
| --------------------- | ----------------- |
| `model`               | Model class       |
| `queryset`            | Custom queryset   |
| `template_name`       | Template override |
| `context_object_name` | Rename `object`   |
| `pk_url_kwarg`        | Default = `"pk"`  |
| `slug_field`          | Slug field        |
| `slug_url_kwarg`      | Slug param        |

| Method               | Purpose              |
| -------------------- | -------------------- |
| `get_object()`       | Retrieve object      |
| `get_queryset()`     | Customize base query |
| `get_context_data()` | Add context          |

## CreateView

| Attribute       | Purpose                   |
| --------------- | ------------------------- |
| `model`         | Model                     |
| `form_class`    | Custom form               |
| `fields`        | Alternative to form_class |
| `template_name` | Template                  |
| `success_url`   | Redirect target           |
| `http_method_names` | Allowed HTTP methods  |

| Method              | Purpose          |
| ------------------- | ---------------- |
| `get_form()`        | Instantiate form |
| `form_valid()`      | Save object      |
| `form_invalid()`    | Handle errors    |
| `get_success_url()` | Dynamic redirect |

## UpdateView

- Same structure as CreateView

- Additional behavior: Automatically binds instance=self.object

| Attribute       | Purpose                   |
| --------------- | ------------------------- |
| `model`         | Model                     |
| `form_class`    | Custom form               |
| `fields`        | Alternative to form_class |
| `template_name` | Template                  |
| `success_url`   | Redirect target           |

| Method              | Purpose          |
| ------------------- | ---------------- |
| `get_form()`        | Instantiate form |
| `form_valid()`      | Save object      |
| `form_invalid()`    | Handle errors    |
| `get_success_url()` | Dynamic redirect |

## DeleteView

| Attribute       | Purpose          |
| --------------- | ---------------- |
| `model`         | Model            |
| `template_name` | Confirm template |
| `success_url`   | Redirect         |

| Method         | Purpose         |
| -------------- | --------------- |
| `delete()`     | Delete object   |
| `get_object()` | Retrieve object |

## FormView (Non-model forms)

| Attribute       | Purpose    |
| --------------- | ---------- |
| `form_class`    | Form class |
| `template_name` | Template   |
| `success_url`   | Redirect   |

| Method           | Purpose             |
| ---------------- | ------------------- |
| `form_valid()`   | Custom processing   |
| `form_invalid()` | Validation handling |

## Date-Based Generic Views

| Class              | Purpose              |
| ------------------ | -------------------- |
| `ArchiveIndexView` | Main archive page    |
| `YearArchiveView`  | Year view            |
| `MonthArchiveView` | Month view           |
| `WeekArchiveView`  | Week view            |
| `DayArchiveView`   | Day view             |
| `TodayArchiveView` | Today view           |
| `DateDetailView`   | Date-specific detail |

| Attribute      | Purpose            |
| -------------- | ------------------ |
| `model`        | Model              |
| `date_field`   | Date field name    |
| `allow_future` | Allow future items |
| `paginate_by`  | Pagination         |

| Method            | Purpose              |
| ----------------- | -------------------- |
| `get_queryset()`  | Customize filtering  |
| `get_date_list()` | Build date hierarchy |

## Authentication Views

| Class                      | Purpose         |
| -------------------------- | --------------- |
| `LoginView`                | Login page      |
| `LogoutView`               | Logout          |
| `PasswordChangeView`       | Change password |
| `PasswordResetView`        | Request reset   |
| `PasswordResetConfirmView` | Confirm reset   |

### 1.LoginView

| Attribute                     | Purpose                          |
| ----------------------------- | -------------------------------- |
| `template_name`               | Login template                   |
| `authentication_form`         | Custom authentication form class |
| `redirect_authenticated_user` | Redirect already-logged-in users |
| `next_page`                   | Redirect after login             |
| `extra_context`               | Extra template context           |
| `success_url_allowed_hosts`   | Allowed redirect hosts           |

| Method              | Purpose                 |
| ------------------- | ----------------------- |
| `form_valid()`      | Logs in user            |
| `form_invalid()`    | Handles invalid login   |
| `get_success_url()` | Determines redirect     |
| `get_form_class()`  | Choose form dynamically |

### 2.LogoutView

| Attribute           | Purpose                        |
| ------------------- | ------------------------------ |
| `next_page`         | Redirect after logout          |
| `template_name`     | Optional confirmation template |
| `extra_context`     | Extra context                  |
| `http_method_names` | Allowed HTTP methods           |

| Method            | Purpose                |
| ----------------- | ---------------------- |
| `dispatch()`      | Handles logout process |
| `get_next_page()` | Determine redirect URL |

### 3.PasswordChangeView

| Attribute       | Purpose                     |
| --------------- | --------------------------- |
| `template_name` | Password change template    |
| `form_class`    | Custom password change form |
| `success_url`   | Redirect after success      |
| `extra_context` | Extra template context      |

| Method              | Purpose             |
| ------------------- | ------------------- |
| `form_valid()`      | Saves new password  |
| `get_success_url()` | Determines redirect |

### 4.PasswordChangeDoneView

| Attribute       | Purpose               |
| --------------- | --------------------- |
| `template_name` | Confirmation template |
| `extra_context` | Additional context    |

### 5.PasswordResetView

| Attribute                  | Purpose                    |
| -------------------------- | -------------------------- |
| `template_name`            | Reset request template     |
| `form_class`               | Custom reset form          |
| `email_template_name`      | Email body template        |
| `subject_template_name`    | Email subject template     |
| `success_url`              | Redirect after submission  |
| `from_email`               | Sender email address       |
| `html_email_template_name` | Optional HTML email        |
| `extra_context`            | Additional context         |
| `token_generator`          | Password reset token logic |

| Method               | Purpose             |
| -------------------- | ------------------- |
| `form_valid()`       | Sends reset email   |
| `get_form_kwargs()`  | Customize form args |
| `get_context_data()` | Extra context       |

### 6.PasswordResetDoneView

| Attribute       | Purpose               |
| --------------- | --------------------- |
| `template_name` | Confirmation template |
| `extra_context` | Additional context    |

### 7.PasswordResetConfirmView

| Attribute                  | Purpose                  |
| -------------------------- | ------------------------ |
| `template_name`            | Password reset form      |
| `form_class`               | Custom set-password form |
| `success_url`              | Redirect after success   |
| `token_generator`          | Token verification       |
| `post_reset_login`         | Auto-login after reset   |
| `post_reset_login_backend` | Backend to use           |
| `extra_context`            | Extra template context   |

| Method         | Purpose                  |
| -------------- | ------------------------ |
| `dispatch()`   | Validates token          |
| `form_valid()` | Sets new password        |
| `get_user()`   | Retrieve user from token |

### 8.PasswordResetCompleteView

| Attribute       | Purpose                |
| --------------- | ---------------------- |
| `template_name` | Final success template |
| `extra_context` | Additional context     |


## Mixins

- A mixin is a reusable class that provides a specific piece of functionality and is designed to be combined with other classes through multiple inheritance.

- A mixin:

    Does not represent a standalone view

    Adds behavior to a view

    Is meant to be composed

### Core Mixins:

| Mixin                   | Purpose                   |
| ----------------------- | ------------------------- |
| `ContextMixin`          | Add context data          |
| `TemplateResponseMixin` | Render template           |
| `SingleObjectMixin`     | Retrieve single object    |
| `MultipleObjectMixin`   | Handle queryset lists     |
| `FormMixin`             | Add form handling         |
| `ModelFormMixin`        | Model-bound form behavior |
| `DeletionMixin`         | Deletion logic            |

### Authentication & Authorization Mixins:

| Mixin                     | Purpose                |
| ------------------------- | ---------------------- |
| `LoginRequiredMixin`      | Require authentication |
| `PermissionRequiredMixin` | Require permission     |
| `UserPassesTestMixin`     | Custom access test     |
| `AccessMixin`             | Base auth mixin        |

### How to Use Mixins:

- Mixin must come before the base view class.

```python
from django.contrib.auth.mixins import LoginRequiredMixin
from django.views.generic import ListView
from .models import Post

class PostListView(LoginRequiredMixin, ListView):
    model = Post
    template_name = "posts/post_list.html"
```

- Correct:

```python
class MyView(LoginRequiredMixin, ListView):
```

- Incorrect:

```python
class MyView(ListView, LoginRequiredMixin):  # Won’t work properly
```

- Python uses Method Resolution Order (MRO).

```python
# Execution order:
# Left → Right

class MyView(A, B, C):

# Method lookup order:
# MyView → A → B → C → Base Classes
# That's why mixins go first.
```
- Example: Composed Production View

```python
class PostUpdateView(
    LoginRequiredMixin,
    OwnerRequiredMixin,
    TitleMixin,
    UpdateView
):
    model = Post
    form_class = PostForm
    page_title = "Edit Post"
```

!!! tip "When to Use Mixins"

    You repeat logic across views

    You need reusable access control

    You inject context consistently

    You modify queryset behavior globally

    Avoid mixins when:

    Logic is view-specific

    Behavior is highly complex

    You’re hiding critical business logic

## Custom Mixins:

### 1. Add Extra Context

```python
class TitleMixin:
    page_title = None

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context["page_title"] = self.page_title
        return context

```
```python
class PostListView(TitleMixin, ListView):
    model = Post
    page_title = "All Posts"
```

### 2. Ownership Protection

```python
from django.core.exceptions import PermissionDenied

class OwnerRequiredMixin:
    def dispatch(self, request, *args, **kwargs):
        obj = self.get_object()
        if obj.user != request.user:
            raise PermissionDenied
        return super().dispatch(request, *args, **kwargs)
```

```python
class PostUpdateView(LoginRequiredMixin, OwnerRequiredMixin, UpdateView):
    model = Post
```

### 3. Queryset Filtering Mixin

```python
class UserQuerysetMixin:
    def get_queryset(self):
        qs = super().get_queryset()
        return qs.filter(user=self.request.user)
```

### 4. Notes on CustomMixins

- Best Practices:

| Rule                            | Reason             |
| ------------------------------- | ------------------ |
| One responsibility per mixin    | Maintain clarity   |
| Always call `super()`           | Preserve MRO chain |
| Avoid heavy logic               | Move to services   |
| Name clearly (`SomethingMixin`) | Communicate intent |
| Keep them reusable              | Avoid coupling     |

- Common Methods Overridden in Mixins

| Method               | Why Override            |
| -------------------- | ----------------------- |
| `dispatch()`         | Intercept request early |
| `get()`              | Customize GET           |
| `post()`             | Customize POST          |
| `get_queryset()`     | Filter data             |
| `get_object()`       | Modify object retrieval |
| `get_context_data()` | Inject template data    |
| `form_valid()`       | Modify save behavior    |

!!! tip "Scaling Considerations"

    Create a mixins.py per app

    Separate:

    auth_mixins.py

    queryset_mixins.py

    ui_mixins.py

    Keep mixins pure and decoupled

    Consider service layer for business logic