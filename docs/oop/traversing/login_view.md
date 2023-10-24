## LoginView

Today, we are excited to traverse the abstractions used in the following CustomLoginView & **LoginView** classes:

```python
class CustomLoginView(LoginView):
    """This view renders our custom login form"""
    form_class = CustomLoginForm
    template_name = 'account/login.html'

```

### What is it?

Django is a framework, which means it offers built-in reusable components. One of these is `LoginView`. In our case, we're enhancing this by inheriting from it via the `django-allauth` package, sourced from the `allauth.account.views` module:

```python
from allauth.account.views import LoginView
```

This additional layer of functionality allows for features like social authentication and email verification.

That said, you'll need to install the package beforehand:

```
pip install django-allauth
```

If you choose not to install it, you will retain the core functionality described below, minus the additional features provided by `allauth`.

### What Does it Do?

`LoginView` enables code reusability, eliminating the need to "reinvent the wheel." You'd otherwise have to build your own login mechanism, which would require significant effort in both logic and security—elements that have already been taken care of.

You have access to methods like `get()`, `post()`, `get_form_class()`, `form_valid()`, `get_context_data()`, and `get_success_url()`.

The `form_class` attribute (another name for a variable but in OOP terms) specifies the form we intend to use. Likewise, `template_name` designates the template where our form, HTML, JavaScript, and CSS will be rendered. Both `form_class` and `template_name` are attributes of the class.

You can also extend the context to include more information by overriding `get_context_data()`:

```python
# Inside our CustomLoginView class:

def get_context_data(self, **kwargs):
    context = super().get_context_data(**kwargs)
    context['extra_info'] = 'This is some extra information.'
    return context
```

### How Does It Work?

1. **Create a Subclass**: Inherit from the imported `LoginView`:

```python
from allauth.account.views import LoginView

class CustomLoginView(LoginView):
    # code pending
```

2. **Specify Custom Functionality**: Override `LoginView`'s default attributes with your custom configurations:

```python
from .forms import CustomLoginForm

class CustomLoginView(LoginView):
    form_class = CustomLoginForm
```

Note how we're importing a custom form, thus overriding either the package's or Django's default form.

3. **Specify the Template**:

```python
class CustomLoginView(LoginView):
    """This view renders our custom login form"""
    form_class = CustomLoginForm
    template_name = 'account/login.html'
```

This allows you to render your form within a customizable template.

### Final Word

The process is simple: install `django-allauth`, inherit from its `LoginView`, and override the default attributes to customize it according to your needs.

### Insights & Clean Code

```python title="Insights"
from django.contrib import messages

class CustomLoginView(LoginView):
    form_class = CustomLoginForm
    template_name = 'account/login.html'
    
    def form_valid(self, form):
        # Call the parent class's form_valid method and store its return value
        response = super().form_valid(form)
        # Custom logic
        username = form.cleaned_data.get('username')
        messages.success(self.request, f'Welcome, {username}!')
        # Return the HttpResponse object
        return response
    
    def form_invalid(self, form):
        messages.error(self.request, 'Login failed. Please try again.')
        # Call the parent class's form_invalid method and return its value
        return super().form_invalid(form)
```

Here, `response = super().form_valid(form)` performs the following functions:

1. `super()`: This returns a temporary object of the superclass, allowing you to call its methods.
2. `.form_valid(form)`: This calls the form_valid method of the superclass. In Django, this method usually handles what to do with a valid form, such as saving a model object.
3. `response =`: This stores the result of `super().form_valid(form)`—often an HttpResponse object like a redirect—in the variable `response`.

The `form` argument passed into `form_valid` and `form_invalid` is an instance of `CustomLoginForm`, as specified by `form_class`.

The `cleaned_data` object is created after form validation upon submission. You can then use `.get()` to retrieve specific key-value pairs.

```python title="Clean Code"
from django.contrib import messages
from allauth.account.views import LoginView

class CustomLoginView(LoginView):
    form_class = CustomLoginForm
    template_name = 'account/login.html'
    
    def form_valid(self, form):
        response = super().form_valid(form)
        username = form.cleaned_data.get('username')
        messages.success(self.request, f'Welcome, {username}!')
        return response
    
    def form_invalid(self, form):
        messages.error(self.request, 'Login failed. Please try again.')
        return super().form_invalid(form)
```
