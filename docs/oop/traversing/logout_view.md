## LogoutView

In this section, we'll delve into the features and customization options of your `CustomLogoutView`. Similar to the [CustomSignupView](../traversing/signup_view.md), this view extends the functionality of a base class, specifically the `LogoutView` from `django-allauth`:

```python
class CustomLogoutView(LogoutView):
    """This view renders our logout page."""
    template_name = 'account/logout.html'
```

### What is it?

Similar to the [CustomSignupView](../traversing/signup_view.md), the `CustomLogoutView` inherits from a class provided by the `django-allauth` package, specifically the `LogoutView`. It allows you to customize the logout process according to your project's needs.

Before using the `CustomLogoutView`, make sure to install the required package:

```bash
pip install django-allauth
```

In your views module, import the necessary class:

```python
from allauth.account.views import LogoutView
```

???+ quote "Quote"
    The process is simple: install `django-allauth`, inherit from its `LogoutView`, and override the default attributes to customize it according to your needs.

### What Does it Do?

The primary purpose of `CustomLogoutView` is to handle the user logout process. By inheriting from `LogoutView`, you leverage the functionality provided by the `django-allauth` package to streamline the logout experience.

1. **Redirect After Logout** 

    You can specify a custom redirect URL after logout by either `get_next_url ` or `LOGOUT_REDIRECT_URL`:

    - Overriding the `get_next_url` method:

    ```python
    class CustomLogoutView(LogoutView):
        """This view renders our logout page."""
        template_name = 'account/logout.html'

        def get_next_url(self):
            # Specify the custom redirect URL after logout
            return '/custom-redirect/'
    ```

    You can explore other options such as:

    ```python
    class CustomLogoutView(LogoutView):
        """This view renders our logout page."""
        template_name = 'account/logout.html'

        def get_next_url(self):
            # Check user type and redirect accordingly
            if self.request.user.is_staff:
                return '/admin-dashboard/'
            else:
                return '/user-dashboard/'
    ```

    - Setting the `LOGOUT_REDIRECT_URL` in your `settings.py`:

    ```python
    # settings.py
    LOGOUT_REDIRECT_URL = '/custom-redirect/'
    ```

    By utilizing the `LOGOUT_REDIRECT_URL` setting, you can centrally manage the redirect URL for all logout views in your project.

2. **Confirmation Messages** 

    Provide a confirmation message to the user upon successful logout using the Django messages framework `from django.contrib import messages`:

    ```python
    from django.contrib import messages

    class CustomLogoutView(LogoutView):
        """This view renders our logout page."""
        template_name = 'account/logout.html'

        def logout(self):
            response = super().logout()
            messages.success(self.request, 'You have been successfully logged out.')
            return response
    ```

3. **Custom Logic Before Logout**

    Execute custom logic before the user is logged out by overriding the `logout` method:

    ```python
    class CustomLogoutView(LogoutView):
        """This view renders our logout page."""
        template_name = 'account/logout.html'

        def logout(self):
            # Your custom logic before logout
            custom_cleanup()

            response = super().logout()
            return response
    ```

4. **Security Considerations**

    Ensure that only authenticated users can access the logout view by using the `@method_decorator(login_required)` decorator:

    ```python
    from django.contrib.auth.decorators import login_required
    from django.utils.decorators import method_decorator
    from allauth.account.views import LogoutView

    @method_decorator(login_required, name='dispatch')
    class CustomLogoutView(LogoutView):
        """This view renders our logout page."""
        template_name = 'account/logout.html'
    ```

### How Does It Work?

1. **Create a Subclass**: Inherit from the imported `LogoutView`:

    ```python
    from allauth.account.views import LogoutView

    class CustomLogoutView(LogoutView):
        # code pending
    ```

2. **Specify Custom Functionality**: Override `LogoutView`'s default attributes with your custom configurations, such as the template:

    ```python
    class CustomLogoutView(LogoutView):
        """This view renders our logout page."""
        template_name = 'account/logout.html'
    ```

### Insights & Clean Code

```python title="Insights"
# Import necessary modules and classes from Django and django-allauth
from django.contrib.auth.decorators import login_required
from django.utils.decorators import method_decorator
from django.contrib import messages  
from allauth.account.views import LogoutView  

# Apply the login_required decorator to the dispatch method of CustomLogoutView
@method_decorator(login_required, name='dispatch')
class CustomLogoutView(LogoutView):
    """Custom logout view that extends the functionality of LogoutView."""

    # Specify the template to be used for rendering the logout page
    template_name = 'account/logout.html'

```

This code defines a custom Django view for logging out (`CustomLogoutView`) based on the `LogoutView` from the `django-allauth` package. Here's a step-by-step explanation:

1. **Import Statements:**
    - `from django.contrib.auth.decorators import login_required`: This import brings in the `login_required` decorator from Django. It ensures that only authenticated users can access the view.
    - `from django.utils.decorators import method_decorator`: This import is used to apply decorators to class-based views.
    - `from django.contrib import messages`: This import includes the messaging framework in Django, allowing you to display messages to users.
    - `from allauth.account.views import LogoutView`: This import brings in the `LogoutView` class from the `django-allauth` package, which handles the default logout behavior.

2. **Decorator Application:**
    - `@method_decorator(login_required, name='dispatch')`: This decorator is applied to the `CustomLogoutView` class. It ensures that the `dispatch` method of the class (which is a standard entry point for a view) is decorated with the `login_required` decorator. This means that only authenticated users can access the `dispatch` method, effectively restricting access to the entire view.

3. **Class Definition:**
    - `class CustomLogoutView(LogoutView):`: This line defines a new class, `CustomLogoutView`, which inherits from the `LogoutView` class provided by `django-allauth`. Inheriting from `LogoutView` allows you to extend and customize the default behavior of the logout process.

4. **Class Attributes:**
    - `template_name = 'account/logout.html'`: This attribute specifies the template that should be used to render the HTML for the logout page. In this case, it points to `'account/logout.html'`.

5. **Docstring:**
    - `"""This view renders our logout page."""`: This is a docstring, a string used to document the purpose or behavior of the class. In this case, it indicates that the view is responsible for rendering the logout page.

```python title="Clean Code"
# In this case, we've chosen to deal with the redirection using LOGOUT_REDIRECT_URL in the settings.py

from django.contrib.auth.decorators import login_required
from django.utils.decorators import method_decorator
from django.contrib import messages
from allauth.account.views import LogoutView

@method_decorator(login_required, name='dispatch')
class CustomLogoutView(LogoutView):
    """This view renders our logout page."""
    template_name = 'account/logout.html'
```

In summary, `CustomLogoutView` encapsulates the logic and configuration needed for a customized user logout process, building upon the foundation provided by `django-allauth`. Customize the view based on your project requirements, considering factors like user experience and security best practices.
