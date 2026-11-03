# Groups and Permissions

## Overview

Permissions in Django uses the following naming sequence:

`{app}.{action}_{model_name}`

`app` - Django App name that is associated model resides in.

`action` - add, change, view, delete or custom.

`model_name` - model name in lowercase.

Model `class Post(models.Model):` would create: `blog.add_post`,`blog.change_post`,`blog.delete_post`,`blog.view_post` .

User can be checked with `has_perm()` function if it has permission to perform an action.

---

- Groups: define roles such as Owner, Manager and Employee.

- Permissions: define allowed actions such as viewing or editing customers.

- Users: are assigned to groups.

- Views: check whether a user has permission to perform an action.

Every model gets four default permissions automatically: add, change, delete, and view. You can create custom permissions when you need additional control like publishing or featuring content.

Groups let you organize users into roles and assign permissions to many users at once. Always use groups instead of assigning permissions to individual users.

Protect your views using the permission_required decorator and check permissions in templates using the perms variable. This ensures users only see and access what they're allowed to.

Finally, remember that `superusers` bypass all permission checks. They're useful for development but be careful who gets `superuser` status in **production**.

---

- Define and create groups and roles:

Management command that creates the three groups with appropriate permissions.

```python
from django.core.management.base import BaseCommand
from django.contrib.auth.models import Group, Permission
from django.contrib.contenttypes.models import ContentType
from blog.models import Post

class Command(BaseCommand):
    help = 'Create user groups with permissions'
    
    def handle(self, *args, **options):
        # Get Post content type
        post_ct = ContentType.objects.get_for_model(Post)
        
        # Create Viewers group (can only view)
        viewers, created = Group.objects.get_or_create(name='Viewers')
        view_post = Permission.objects.get(codename='view_post', content_type=post_ct)
        viewers.permissions.add(view_post)
        self.stdout.write(f'Created/Updated Viewers group')
        
        # Create Editors group (can view, add, change)
        editors, created = Group.objects.get_or_create(name='Editors')
        add_post = Permission.objects.get(codename='add_post', content_type=post_ct)
        change_post = Permission.objects.get(codename='change_post', content_type=post_ct)
        editors.permissions.add(view_post, add_post, change_post)
        self.stdout.write(f'Created/Updated Editors group')
        
        # Create Publishers group (all permissions including custom)
        publishers, created = Group.objects.get_or_create(name='Publishers')
        delete_post = Permission.objects.get(codename='delete_post', content_type=post_ct)
        publish_post = Permission.objects.get(codename='can_publish_post', content_type=post_ct)
        publishers.permissions.add(view_post, add_post, change_post, delete_post, publish_post)
        self.stdout.write(f'Created/Updated Publishers group')
        
        self.stdout.write(self.style.SUCCESS('Successfully set up all groups'))
```

---

## Project Structure

```text
accounts/
│
├── models.py
├── views.py
├── urls.py
│
├── services/
│   ├── __init__.py
│   ├── roles.py
│   └── users.py
│
└── management/
    └── commands/
        └── setup_roles.py
```


The responsibilities are:

| File                                 | What it orchestrates                                 |
| ------------------------------------ | ---------------------------------------------------- |
| `services/roles.py`                  | Groups and their permissions                         |
| `services/users.py`                  | Assigning roles to users                             |
| `management/commands/setup_roles.py` | Running the initial role setup                       |
| `views.py`                           | Checking permissions when users access functionality |

---

## roles.py

It is almost like a configuration (central permission policy):

- Owner can add, view, change and delete customers.
- Manager can add, view and change customers.
- Employee can view and add customers.

`group.permissions.set()` makes the configured permissions the group's complete permission set. This means that if you remove a permission from the configuration, it will also be removed from the group the next time you run the setup.

```python
from django.contrib.auth.models import Group, Permission
from django.contrib.contenttypes.models import ContentType

from customers.models import Customer
from sales.models import Sale
from products.models import Product


ROLE_PERMISSIONS = {
    "Owner": {
        Customer: [
            "add_customer",
            "view_customer",
            "change_customer",
            "delete_customer",
        ],
        Sale: [
            "add_sale",
            "view_sale",
            "change_sale",
            "delete_sale",
        ],
        Product: [
            "add_product",
            "view_product",
            "change_product",
            "delete_product",
        ],
    },

    "Manager": {
        Customer: [
            "add_customer",
            "view_customer",
            "change_customer",
        ],
        Sale: [
            "add_sale",
            "view_sale",
            "change_sale",
        ],
        Product: [
            "view_product",
            "change_product",
        ],
    },

    "Employee": {
        Customer: [
            "view_customer",
            "add_customer",
        ],
        Sale: [
            "view_sale",
            "add_sale",
        ],
        Product: [
            "view_product",
        ],
    },
}


def setup_roles():
    """
    Create CRM groups and assign their permissions.
    """

    for role_name, model_permissions in ROLE_PERMISSIONS.items():

        group, _ = Group.objects.get_or_create(
            name=role_name
        )

        permissions = []

        for model, codenames in model_permissions.items():

            content_type = ContentType.objects.get_for_model(model)

            model_perms = Permission.objects.filter(
                content_type=content_type,
                codename__in=codenames,
            )

            permissions.extend(model_perms)

        group.permissions.set(permissions)
```

---

## setup_roles.py

```python
from django.core.management.base import BaseCommand

from accounts.services.roles import setup_roles


class Command(BaseCommand):

    help = "Configure CRM roles and permissions"

    def handle(self, *args, **options):

        setup_roles()

        self.stdout.write(
            self.style.SUCCESS(
                "CRM roles and permissions configured."
            )
        )
```

- Run migrations:

```bash
uv run python manage.py migrate
uv run python manage.py setup_roles
```

This is similar to running a database setup or maintenance operation. You execute it when setting up the application or updating the role policy, not during normal page requests.

---

## Best Practices

- Use Groups, Not Individual Permissions.
    
    Always assign permissions to groups and add users to groups. Avoid assigning permissions directly to individual users unless absolutely necessary.

    This makes your system easier to manage. When you need to change what Editors can do, 
    you update one group instead of dozens of users.

- Name Groups by Role
    
    Use role names like "Editor" or "Moderator" rather than permission-based names like "Can Edit Posts Users". Roles are easier to understand and communicate.

    Think about how you'd explain the system to someone: "She's an Editor" is clearer than "She has add and change permissions on posts."

- Check Permissions, Not Groups
    
    In your code, check for specific permissions rather than checking group membership.

    The permission-based approach works even if you reorganize groups later. If you decide Publishers and Editors can both publish, you just add the permission to both groups.

    ```python
        # Good: Check permission
        if request.user.has_perm('blog.can_publish_post'):
            # Publish the post

        # Avoid: Check group name
        if request.user.groups.filter(name='Publishers').exists():
            # Publish the post
    ```

- Use `raise_exception=True`

    When protecting views with permission_required, use raise_exception=True to show a 403 Forbidden page instead of redirecting to login:

    ```python
        @permission_required('blog.delete_post', raise_exception=True)
        def delete_post(request, post_id):
            # View code here
    ```

    This gives better feedback to logged-in users who lack permission. They know they're blocked by permissions, not because they need to log in.

- Document Your Permissions
    
    Keep a simple document or comment explaining what each role can do. This helps when onboarding new team members or deciding what group to assign to a user.

    ```python
        # Roles in our blog system:
        # - Viewers: Can only view published content
        # - Editors: Can create and edit posts but not publish
        # - Publishers: Full control including publish and delete
        # - Superusers: Complete system access including admin
    ```

---

## Common Permission Issues

Here are some problems you might encounter and how to solve them.

- Superuser vs Staff Status

    Users need is_staff=True to access the admin site, but this doesn't give them any permissions. Superusers (is_superuser=True) have all permissions automatically.

    If someone can log into admin but can't do anything, they're probably staff without permissions. Add them to appropriate groups or make them a superuser.

- Permissions Not Applying

    If you change a user's groups or permissions, they might need to log out and back in for changes to take effect. Django caches permission data during the session.

    In code, you can force a refresh with:

    ```python
    from django.contrib.auth.models import User

    user = User.objects.get(username='john')
    user.refresh_from_db()
    user = User.objects.get(pk=user.pk)  # Re-fetch from database
    ```

- Custom Permissions Not Appearing

    If you added custom permissions to your model but they're not showing up, make sure you ran migrations:

    ```bash
    python manage.py makemigrations
    python manage.py migrate
    ```
    
    Django creates permissions during the migration process. Without migrations, your custom permissions won't exist in the database.

---

## Notes

### Override `has_permission()`

One detail: Permission names follow the format app_label.codename. For example, "accounts.edit_details" is valid only if you have defined the edit_details permission in your accounts app. Django's built-in user-creation permission is "auth.add_user".

For most views, use the default AND behavior when the user needs every listed permission to perform the action.

Django requires ALL permissions by default
With the configuration above, the user must have both permissions to access the view.

| Configuration | Behavior |
|---|---|
| `"auth.add_user"` | Must have that permission |
| `["auth.add_user", "accounts.edit_details"]` | Must have both permissions |

Custom `has_permission()` Can implement OR logic or other rules.

If you want ANY one permission (a OR b) Override `has_permission()`:

```python
class EmployeeCreateView(
    LoginRequiredMixin,
    PermissionRequiredMixin,
    CreateView,
):
    permission_required = [
        "auth.add_user",
        "accounts.edit_details",
    ]

    def has_permission(self):
        return any(
            self.request.user.has_perm(permission)
            for permission in self.get_permission_required()
        )
```

> Permission names follow the format app_label.codename. For example, "accounts.edit_details" is valid only if you have defined the edit_details permission in your accounts app. Django's built-in user-creation permission is "auth.add_user".

For most views, use the default AND behavior when the user needs every listed permission to perform the action.

---

### 