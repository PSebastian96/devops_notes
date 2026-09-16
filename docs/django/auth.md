# `allauth` notes

## Overview

Integrated set of Django applications addressing authentication, registration, account management as well as 3rd party (social) account authentication.

## Installation

- Install with uv

```bash
uv add django-allauth
```

- Social logins

```bash
uv add "django-allauth[socialaccount]"
```

!!! warning "Post Installation"
    In your Django root execute the command below to create your database tables:

    `python manage.py migrate`

    Now start your server, visit your admin pages (e.g. http://localhost:8000/admin/) and follow these steps:

    For each OAuth based provider, either add a SocialApp (socialaccount app) containing the required client credentials, or make sure that these are configured via the SOCIALACCOUNT_PROVIDERS[<provider>]['APP'] setting.#

---

## `settings.py`

- What to add to `settings.py` .

```python
# Specify the context processors as follows:
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                # Already defined Django-related contexts here

                # `allauth` needs this from django
                'django.template.context_processors.request',
            ],
        },
    },
]

AUTHENTICATION_BACKENDS = [
    ...
    # Needed to login by username in Django admin, regardless of `allauth`
    'django.contrib.auth.backends.ModelBackend',

    # `allauth` specific authentication methods, such as login by email
    'allauth.account.auth_backends.AuthenticationBackend',
    ...
]

INSTALLED_APPS = [
    ...
    # The following apps are required:
    'django.contrib.auth',
    'django.contrib.messages',

    'allauth',
    'allauth.account',

    # Optional -- requires install using `django-allauth[socialaccount]`.
    'allauth.socialaccount',
]

# Authentication section

"""This is a Django setting, not an allauth setting, but it is fundamental to your setup.
accounts is custom django app for the web apps account management"""
AUTH_USER_MODEL = "accounts.User"

""" allauth for username or email login method """
ACCOUNT_LOGIN_METHODS = {"username"}

""" controls which fields appear on signup  """
ACCOUNT_SIGNUP_FIELDS = [
    "username*",
    "email*",
    "password1*",
    "password2*",
]

""" email verification, has 3 options:
mandatory - User must verify email before logging in
optional - Verification email is sent, but user can log in
none - No email verification """
ACCOUNT_EMAIL_VERIFICATION = "mandatory"

""" allauth defaults to True, declare it to make it explicit in code """
ACCOUNT_UNIQUE_EMAIL = True


""" redirects 
syntax - "app_name:url_name"
"""
ACCOUNT_SIGNUP_REDIRECT_URL = "accounts:read_profile"

LOGIN_REDIRECT_URL = "accounts:read_profile"

ACCOUNT_LOGOUT_REDIRECT_URL = "home:index"

""" This is django built in logout redirect, can be used with instead of allauth.
Allauth's ACCOUNT_LOGOUT_REDIRECT_URL defaults to Django's LOGOUT_REDIRECT_URL, or / if that isn't configured.
"""
LOGOUT_REDIRECT_URL = "home:index"

"""Allauth documents GET logout as something that can be dangerous because GET is not intended to modify server state. 

False is the current default value.
"""
ACCOUNT_LOGOUT_ON_GET = False

""" Session "Remember Me" - The default is None.
It means allauth can ask the user whether they want the session remembered.
Has 3 optional values: 
None  → ask the user
False → don't remember
True  → always remember
"""
ACCOUNT_SESSION_REMEMBER = None

""" this helps prevent someone from using password-reset/signup flows to determine whether a particular email address has an account.

Defaults to True
"""
ACCOUNT_PREVENT_ENUMERATION = True

""" password change behavior
If you want users to be logged out everywhere/after changing their password, this setting controls the behaviour.

"""
ACCOUNT_LOGOUT_ON_PASSWORD_CHANGE = False

""" configure reauthentication 
The timeout is five minutes by default.
"""
ACCOUNT_REAUTHENTICATION_REQUIRED = False
ACCOUNT_REAUTHENTICATION_TIMEOUT = 300

""" enable email change  """
ACCOUNT_CHANGE_EMAIL = True

""" override defualt forms provided by allauth """
ACCOUNT_FORMS = {
    'signup': 'accounts.forms.CustomSignupForm',
}

```

## Core account settings

| Setting                             | Type            | Default                                                | Purpose                      |
| ----------------------------------- | --------------- | -----------------------------------------------------: | -----------------------------|
| `ACCOUNT_ADAPTER`                   | string          |      `"allauth.account.adapter.DefaultAccountAdapter"` | Custom account behavior                        |
| `ACCOUNT_FORMS`                     | dict            |                                  Built-in form mapping | Replace allauth forms                          |
| `ACCOUNT_LOGIN_METHODS`             | set             |                                         `{"username"}` | Login with username and/or email               |
| `ACCOUNT_SIGNUP_FIELDS`             | list            | `["username*", "email*", "password1*", "password2*"]`* | Fields shown/required during signup            |
| `ACCOUNT_EMAIL_VERIFICATION`        | string          |                                           `"optional"` | `"mandatory"`, `"optional"`, or `"none"`       |
| `ACCOUNT_UNIQUE_EMAIL`              | bool            |                                                 `True` | Require unique email addresses                 |
| `ACCOUNT_EMAIL_MAX_LENGTH`          | int             |                                                  `254` | Maximum email length                           |
| `ACCOUNT_MAX_EMAIL_ADDRESSES`       | int/None        |                                                 `None` | Maximum emails attached to account             |
| `ACCOUNT_CHANGE_EMAIL`              | bool            |                                                `False` | Restrict account to one changeable email       |
| `ACCOUNT_USER_MODEL_USERNAME_FIELD` | string/None     |                                           `"username"` | User model's username field                    |
| `ACCOUNT_USER_MODEL_EMAIL_FIELD`    | string/None     |                                              `"email"` | User model's email field                       |
| `ACCOUNT_USER_DISPLAY`              | callable/string |                                        `user.username` | Controls displayed username/name               |
| `ACCOUNT_USERNAME_MIN_LENGTH`       | int             |                                                    `1` | Minimum username length                        |
| `ACCOUNT_USERNAME_BLACKLIST`        | list            |                                                   `[]` | Usernames that cannot be registered            |
| `ACCOUNT_USERNAME_VALIDATORS`       | string/None     |                                                 `None` | Custom username validators                     |
| `ACCOUNT_PRESERVE_USERNAME_CASING`  | bool            |                                                 `True` | Preserve username capitalization               |
| `ACCOUNT_PREVENT_ENUMERATION`       | bool            |                                                 `True` | Helps prevent revealing whether accounts exist |

---

## Login / logout

| Setting                                 | Type       |                               Default | Purpose                                         |
| --------------------------------------- | ---------- | ------------------------------------: | ----------------------------------------------- |
| `ACCOUNT_AUTHENTICATED_LOGIN_REDIRECTS` | bool       |                                `True` | Redirect logged-in users away from login/signup |
| `ACCOUNT_LOGIN_ON_EMAIL_CONFIRMATION`   | bool       |                               `False` | Automatically login after email confirmation    |
| `ACCOUNT_LOGIN_ON_PASSWORD_RESET`       | bool       |                               `False` | Automatically login after password reset        |
| `ACCOUNT_LOGIN_TIMEOUT`                 | int        |                                 `900` | Login-flow timeout in seconds                   |
| `ACCOUNT_LOGOUT_ON_GET`                 | bool       |                               `False` | Allow logout through GET instead of POST        |
| `ACCOUNT_LOGOUT_ON_PASSWORD_CHANGE`     | bool       |                               `False` | Logout user after password change               |
| `ACCOUNT_LOGOUT_REDIRECT_URL`           | string/URL | `settings.LOGOUT_REDIRECT_URL or "/"` | Where to go after logout                        |
| `ACCOUNT_SIGNUP_REDIRECT_URL`           | string/URL |         `settings.LOGIN_REDIRECT_URL` | Where to go after signup                        |

---

## Password / security 

| Setting                                       | Type   |                                                         Default | Purpose                                               |
| --------------------------------------------- | ------ | --------------------------------------------------------------: | ----------------------------------------------------- |
| `ACCOUNT_PASSWORD_INPUT_RENDER_VALUE`         | bool   |                                                         `False` | Whether password fields retain rendered values        |
| `ACCOUNT_PASSWORD_RESET_BY_CODE_ENABLED`      | bool   |                                                         `False` | Use code instead of reset link                        |
| `ACCOUNT_PASSWORD_RESET_BY_CODE_FORMAT`       | dict   |                             `settings.ALLAUTH_USER_CODE_FORMAT` | Password-reset code format                            |
| `ACCOUNT_PASSWORD_RESET_BY_CODE_MAX_ATTEMPTS` | int    |                                                             `3` | Maximum code attempts                                 |
| `ACCOUNT_PASSWORD_RESET_BY_CODE_TIMEOUT`      | int    |                                                           `180` | Code lifetime in seconds                              |
| `ACCOUNT_PASSWORD_RESET_TOKEN_GENERATOR`      | string | `"allauth.account.forms.EmailAwarePasswordResetTokenGenerator"` | Password-reset token generator                        |
| `ACCOUNT_REAUTHENTICATION_REQUIRED`           | bool   |                                                         `False` | Require recent reauthentication for sensitive changes |
| `ACCOUNT_REAUTHENTICATION_TIMEOUT`            | int    |                                                           `300` | Reauthentication validity in seconds                  |

---

## Email verification

| Setting                                           | Type |                             Default | Purpose                                  |
| ------------------------------------------------- | ---- | ----------------------------------: | ---------------------------------------- |
| `ACCOUNT_CONFIRM_EMAIL_ON_GET`                    | bool |                             `False` | Confirm email immediately from GET       |
| `ACCOUNT_EMAIL_CONFIRMATION_HMAC`                 | bool |                              `True` | Use HMAC-based confirmation keys         |
| `ACCOUNT_EMAIL_CONFIRMATION_EXPIRE_DAYS`          | int  |                                 `3` | Confirmation-link lifetime               |
| `ACCOUNT_EMAIL_VERIFICATION_BY_CODE_ENABLED`      | bool |                             `False` | Use verification code instead of link    |
| `ACCOUNT_EMAIL_VERIFICATION_BY_CODE_FORMAT`       | dict | `settings.ALLAUTH_USER_CODE_FORMAT` | Verification-code format                 |
| `ACCOUNT_EMAIL_VERIFICATION_BY_CODE_MAX_ATTEMPTS` | int  |                                 `3` | Maximum verification attempts            |
| `ACCOUNT_EMAIL_VERIFICATION_BY_CODE_TIMEOUT`      | int  |                               `900` | Verification-code lifetime               |
| `ACCOUNT_EMAIL_VERIFICATION_SUPPORTS_CHANGE`      | bool |                             `False` | Allow changing email during verification |
| `ACCOUNT_EMAIL_VERIFICATION_SUPPORTS_RESEND`      | bool |                             `False` | Allow resending verification code        |

---

## Templates

- allauth's `account/` template namespace — templates used to override allauth's UI.

Example of project file directory iwth allauth templates:

```text
myproject/
│
├── config/
│   ├── settings.py
│   └── urls.py
│
├── accounts/
│   ├── models.py
│   ├── forms.py
│   ├── views.py
│   ├── urls.py
│   └── ...
│
├── home/
│   └── ...
│
└── templates/
    ├── base.html
    │
    ├── account/              ← allauth templates
    │   ├── login.html
    │   ├── signup.html
    │   ├── logout.html
    │   ├── password_reset.html
    │   ├── password_change.html
    │   └── ...
    │
    ├── socialaccount/        ← if you use social login
    │   └── ...
    │
    └── accounts/             ← your own accounts templates
        ├── read_profile.html
        ├── edit_profile.html
        └── ...
```

---

## Social Accounts

| Setting                                           | Type   |                                                       Default | Purpose                                                           |
| ------------------------------------------------- | ------ | ------------------------------------------------------------: | ----------------------------------------------------------------- |
| `SOCIALACCOUNT_ADAPTER`                           | string | `"allauth.socialaccount.adapter.DefaultSocialAccountAdapter"` | Customize social-account behavior                                 |
| `SOCIALACCOUNT_AUTO_SIGNUP`                       | bool   |                                                        `True` | Automatically signup when provider supplies enough data           |
| `SOCIALACCOUNT_EMAIL_AUTHENTICATION`              | bool   |                                                       `False` | Allow verified provider email to authenticate an existing account |
| `SOCIALACCOUNT_EMAIL_AUTHENTICATION_AUTO_CONNECT` | bool   |                                                       `False` | Automatically connect that social account                         |
| `SOCIALACCOUNT_EMAIL_VERIFICATION`                | string |                                  `ACCOUNT_EMAIL_VERIFICATION` | Email-verification policy for social signup                       |
| `SOCIALACCOUNT_EMAIL_REQUIRED`                    | bool   |                           `"email*" in ACCOUNT_SIGNUP_FIELDS` | Require provider email                                            |
| `SOCIALACCOUNT_FORMS`                             | dict   |                                              Built-in mapping | Override social-account forms                                     |
| `SOCIALACCOUNT_LOGIN_ON_GET`                      | bool   |                                                       `False` | Allow initiating social login with GET                            |
| `SOCIALACCOUNT_PROVIDERS`                         | dict   |                                                          `{}` | Provider-specific configuration                                   |
| `SOCIALACCOUNT_QUERY_EMAIL`                       | bool   |                           `"email*" in ACCOUNT_SIGNUP_FIELDS` | Request email from provider                                       |
| `SOCIALACCOUNT_STORE_TOKENS`                      | bool   |                                                       `False` | Store OAuth access/refresh tokens                                 |
| `SOCIALACCOUNT_ONLY`                              | bool   |                                                       `False` | Disable local accounts and use only social login                  |
| `SOCIALACCOUNT_REQUESTS_TIMEOUT`                  | int    |                                                           `5` | Timeout for provider requests                                     |
| `SOCIALACCOUNT_OPENID_CONNECT_URL_PREFIX`         | string |                                                      `"oidc"` | URL prefix for OIDC providers                                     |

---

## Notes

### Quick reference

- Responsility and their locations

| Responsibility            | Where                                                 |
| ------------------------- | ----------------------------------------------------- |
| `User` model              | `accounts/models.py`                                  |
| `Profile` model           | `accounts/models.py`                                  |
| Profile views             | `accounts/views.py`                                   |
| Profile URLs              | `accounts/urls.py`                                    |
| Your custom account forms | `accounts/forms.py`                                   |
| Login UI                  | `templates/account/login.html`                        |
| Signup UI                 | `templates/account/signup.html`                       |
| Logout UI                 | `templates/account/logout.html`                       |
| Password reset UI         | `templates/account/password_reset.html`               |
| Profile page              | `templates/accounts/read_profile.html`                |
| Edit profile page         | `templates/accounts/edit_profile.html`                |
| Delete-account UI         | `templates/accounts/...` or modal in profile template |
| Social-login UI           | `templates/socialaccount/...`                         |

---

### Override Temaplates

1.Create a project-level templates directory

The `account/` name comes from allauth's template namespace.

```text
myproject/
├── manage.py
├── config/
├── accounts/
├── templates/
│   ├── base.html
│   └── account/
└── ...
```

2.Make sure Django searches for project templates

```python
TEMPLATES = [
    {
        "BACKEND": "django.template.backends.django.DjangoTemplates",
        "DIRS": [BASE_DIR / "templates"],
        "APP_DIRS": True,
        # ...
    },
]
```

3.Find the desired template

Create and add the `account/`, look up the documentation or github repo for the exact template html file name.

```text
templates/
└── account/
    ├── login.html
    ├── signup.html
    ├── logout.html
    ├── password_change.html
    ├── password_reset.html
    ├── password_reset_from_key.html
    └── email.html
```

4.Override by adding custom styling (Bootstrap, Tailwind etc.)

```html
{% extends "base.html" %}

{% block content %}

<div class="container py-5">
    <h1>Sign in</h1>

    <form method="post">
        {% csrf_token %}

        {{ form.non_field_errors }}

        <div class="mb-3">
            <label for="{{ form.login.id_for_label }}"
                   class="form-label">
                {{ form.login.label }}
            </label>

            {{ form.login }}

            {{ form.login.errors }}
        </div>

        <div class="mb-3">
            <label for="{{ form.password.id_for_label }}"
                   class="form-label">
                {{ form.password.label }}
            </label>

            {{ form.password }}

            {{ form.password.errors }}
        </div>

        <button type="submit" class="btn btn-primary">
            Sign in
        </button>
    </form>
</div>

{% endblock %}
```

---

### Override Formviews

- Overriding the form/view behavior — change what the page actually does.

- Consider overriding a view when you need to change view-level behavior, for example:

    * redirect somewhere unusual based on custom logic
    * add custom context
    * perform extra processing before/after the allauth operation
    * change how a particular request is handled
    * integrate some application-specific workflow

---

- Access the `allauth` views from : `from allauth.account.views import LoginView` .

- Access the `allauth` views from: `from allauth.account.forms import LoginForm` .


1.Import the Form view from allauth and override it

```python
from allauth.account.forms import LoginForm


class CustomLoginForm(LoginForm):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

        self.fields["login"].widget.attrs.update({
            "class": "form-control",
            "placeholder": "Username",
        })
```

2.`settings.py` add the custom login form

```python
ACCOUNT_FORMS = {
    "login": "accounts.forms.CustomLoginForm",
}
```

## Other

### When to override defaults

- Use a template override when:

>"I want the page to look different."

- Use a form override when:

>"I want the form fields/validation/behavior to be different."

- Use a view override when:

>"I want the actual request/response workflow to behave differently."