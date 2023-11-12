## SignupView

Today, let's delve into the intricacies of your `CustomSignupView`. Just like the [CustomLoginView](../traversing/login_view.md), this view extends the functionality of a base class, in this case, the `SignupView` from `django-allauth`. Let's break it down:

```python
class CustomSignupView(SignupView):
    """This view renders our custom signup form"""
    form_class = CustomSignupForm
    template_name = 'account/signup.html'
```

### What is it?

Similar to the [CustomLoginView](../traversing/login_view.md), the `CustomSignupView` inherits from a class provided by the `django-allauth` package, specifically the `SignupView`. This allows you to customize the signup process according to your project's needs:

That said, you'll need to install the package beforehand:

```
pip install django-allauth
```

- This additional layer of functionality allows for features like social authentication and email verification.

```python
from allauth.account.views import SignupView
```

If you choose not to install it, you will retain the core functionality described below, minus the additional features provided by `allauth`.

???+ quote "Quote"
    The process is simple: install `django-allauth`, inherit from its `SignupView`, and override the default attributes to customize it according to your needs.

### What Does it Do?

The primary purpose of `CustomSignupView` is to handle the user registration or signup process. By inheriting from `SignupView`, you leverage the functionality provided by the `django-allauth` package to streamline the signup experience.

Here are the key aspects:

1. **Form Handling**: The `form_class` attribute specifies the custom form (`CustomSignupForm`) that will be used for user input during the signup process. This allows you to collect specific information from users based on your project's requirements.

2. **Template Rendering**: The `template_name` attribute designates the template (`'account/signup.html'`) where the signup form, along with any associated HTML, JavaScript, and CSS, will be rendered. This separation of concerns enhances maintainability and allows for a clean presentation layer.

3. **Customized Feedback**: In the `form_valid` and `form_invalid` methods, you customize the feedback messages displayed to the user after a successful signup or in case of errors. For example, the success message welcomes the user by their username and informs them that the account has been created successfully. On the other hand, the error message alerts the user if there are issues with the signup information.

4. **Superclass Integration**: The use of `super().form_valid(form)` and `super().form_invalid(form)` ensures that you're building on top of the existing functionality provided by the `SignupView`. This is a common practice in object-oriented programming, allowing you to extend the behavior of the parent class while preserving its core functionality.

In summary, `CustomSignupView` encapsulates the logic and configuration needed for a customized user signup process, building upon the foundation provided by `django-allauth` and promoting clean, maintainable code.

### How Does It Work?

1. **Create a Subclass**: Inherit from the imported `SignupView`:

```python
from allauth.account.views import SignupView

class CustomSignupView(SignupView):
    # code pending
```

2. **Specify Custom Functionality**: Override `SignupView`'s default attributes with your custom configurations:

```python
from .forms import CustomSignupForm

class CustomSignupView(SignupView):
    form_class = CustomSignupForm
```

Here, just like in the login view, you're importing a custom form to override the default form provided by `django-allauth`.

3. **Specify the Template**:

```python
class CustomSignupView(SignupView):
    """This view renders our custom signup form"""
    form_class = CustomSignupForm
    template_name = 'account/signup.html'
```

This line designates the template where your custom signup form, HTML, JavaScript, and CSS will be rendered.

### Insights & Clean Code

```python title="Insights"
from django.contrib import messages

class CustomSignupView(SignupView):
    form_class = CustomSignupForm
    template_name = 'account/signup.html'
    
    def form_valid(self, form):
        response = super().form_valid(form)
        username = form.cleaned_data.get('username')
        messages.success(self.request, f'Welcome, {username}! Your account has been created successfully.')
        return response
    
    def form_invalid(self, form):
        messages.error(self.request, 'Signup failed. Please check your information and try again.')
        return super().form_invalid(form)
```

In the `form_valid` and `form_invalid` methods, you're essentially doing the same logic as in the login view. The `response = super().form_valid(form)` line, again, captures the result of the superclass's `form_valid` method:

The `response = super().form_valid(form)` performs the following functions:

1. `super()`: This returns a temporary object of the superclass, allowing you to call its methods.
2. `.form_valid(form)`: This calls the form_valid method of the superclass. In Django, this method usually handles what to do with a valid form, such as saving a model object.
3. `response =`: This stores the result of `super().form_valid(form)`—often an HttpResponse object like a redirect—in the variable `response`.

The `form` argument passed into `form_valid` and `form_invalid` is an instance of `CustomLoginForm`, as specified by `form_class`.

The `cleaned_data` object is created after form validation upon submission. You can then use `.get()` to retri

```python title="Clean Code"
from django.contrib import messages
from allauth.account.views import SignupView

class CustomSignupView(SignupView):
    form_class = CustomSignupForm
    template_name = 'account/signup.html'
    
    def form_valid(self, form):
        response = super().form_valid(form)
        username = form.cleaned_data.get('username')
        messages.success(self.request, f'Welcome, {username}! Your account has been created successfully.')
        return response
    
    def form_invalid(self, form):
        messages.error(self.request, 'Signup failed. Please check your information and try again.')
        return super().form_invalid(form)
```

By keeping your code DRY (Don't Repeat Yourself), you ensure that any future changes or updates only need to be made in one place, promoting maintainability and reducing the likelihood of introducing bugs.