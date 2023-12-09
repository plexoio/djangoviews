## HomepageProductServiceView

I am very exiciting to explain the following code. Let's explore the **HomepageProductServiceView** class, understanding its structure and functionality in the context of a Django web application.

### What is it?

It is a Django view class designed to display products and services on the homepage of a website. It inherits from `ProductBaseListView`, which is a specialized ListView for handling product data for the sake of modularity.

??? example "Code to Traverse"
    ```python
    from .models import Product, Service, Order
    from django.db.models import Count

    class ProductBaseListView(ListView):
        """Base view for listing products based on these conditions."""
        model = Product

        def get_queryset(self):
            """Return products with a status of 2, ordered by creation date."""
            products = Product.objects.filter(status=2).annotate(
                likescount=Count('likes'),
            ).order_by('-created_on')

            return products


    class HomepageProductServiceView(ProductBaseListView):
        """Frontend main page displaying the list of products & services."""
        template_name = 'home/homepage.html'
        context_object_name = 'products'

        def get_context_data(self, **kwargs):
            """
            1. Slice product instances to 3 (from parent class)
            2. Slice service instances to 3 (in current class)
            3. Define variables for separation of concerns & single view parameters
            4. Define a combined list to mix them as needed
            5. Fill single variables for a single display
            6. Extend products and services to the combined list, slice,
            and remove them for specific elements, avoiding redundancy and size
            7. Define metric variables
            8. Apply logic to the combined, product, and service rows
            9. Add variables holding data to the context to access them from
            the template
            """
            context = super().get_context_data(**kwargs)

            products = list(context['products'][:3])
            services = list(Service.objects.filter(
                status=2).annotate(
                    likescount=Count('likes'),
            ).order_by('-created_on')[:3])

            product_single = []
            service_single = []

            combined_list = []

            # 1 product, 1 services, 1 product INTENTION
            if products and services:
                product_single.extend(products[:3])
                service_single.extend(services[:3])

                combined_list.extend(products[:1])
                products = products[2:]

                combined_list.extend(services[:1])
                services = services[3:]

                combined_list.extend(products[:1])
                products = products[1:]

            # Deal with frontend Metrics (Like counts & Purchases) consulting DB
            order_count_combined = {}
            order_count_product = {}
            order_count_service = {}

            for item in combined_list:
                if item.instance == 0:
                    order_count = Order.objects.filter(
                        status=2, lineitems__product=item).count()
                    order_count_combined[item.title] = order_count
                else:
                    order_count = Order.objects.filter(
                        status=2, lineitems__service=item).count()
                    order_count_combined[item.title] = order_count

            for item in product_single:
                if item.instance == 0:
                    order_count = Order.objects.filter(
                        status=2, lineitems__product=item).count()
                    order_count_product[item.title] = order_count

            for item in service_single:
                if item.instance == 1:
                    order_count = Order.objects.filter(
                        status=2, lineitems__service=item).count()
                    order_count_service[item.title] = order_count

            # Add collected data to the context allowing access from the template
            context['combined_items'] = combined_list
            context['product_single'] = product_single
            context['service_single'] = service_single
            context['order_count_combined'] = order_count_combined
            context['order_count_product'] = order_count_product
            context['order_count_service'] = order_count_service
            return context
    ```

- **Purpose**: It's crafted to showcase a selection of products and services on the main page of the site.

### What Does it Do?

Firstly, **HomepageProductServiceView** (Custom Context Data)  extends the functionality of **ProductBaseListView** (Filter Products) to:

1. **Filter Products**: The `get_queryset` method is used to filter products based on specific criteria, such as their status and the order of creation. Django automatically adds these objects to the context, making them accessible in the template as `product_list`. Subsequently, we can change this default name and add it back to the context using the `get_context_data()` method on the `HomepageProductServiceView`. Alternatively, this can be achieved by setting the `context_object_name` attribute in the view class.

    - It is advisable to override it in this case, as we aim for a convenient and optimized interaction with our database.

    ```python title="HomepageProductServiceView"
    class ProductBaseListView(ListView):
        """Base view for listing products based on these conditions."""
        model = Product

        def get_queryset(self):
            """Return products with a status of 2, ordered by creation date."""
            products = Product.objects.filter(status=2).annotate(
                likescount=Count('likes'),
            ).order_by('-created_on')

            return products
    ```

    - filter(): Used for retrieving database objects that meet our specific criteria.
    - annotate(): Adds a temporary field to each object at the time of the query execution, without altering the database schema.
    - Count(): Utilized for counting the number of 'likes' instances associated with each Product object. It always returns an integer.
    - `return products`: This returns a local **variable** with the Product's queryset. Django internally uses this returned queryset to populate the context with these objects. The context variable name is typically derived from the model name (in lowercase, suffixed with _list, e.g., product_list for a model named Product).

    In the context of Django's ORM (Object-Relational Mapping), this combination is commonly used in Django to efficiently retrieve and manipulate data from the database with added computations and filters.

2. **Custom Context Data**: This overrides the `get_context_data` method to add custom data to the context. This process involves selecting a specific number of products and services, calculating metrics, and preparing them for display.

    This point might seem a bit tricky, but it's actually quite straightforward. We previously mentioned that we could change the default name used to access the `Products` object in the `context`. This can be achieved by either declaring the `context_object_name` in the `ProductBaseListView` class or in the `HomepageProductServiceView`. For the sake of consistency, I have chosen to do it in the latter.

    ```python title="get_context_data() & super()"
    class HomepageProductServiceView(ProductBaseListView):
        """Frontend main page displaying the list of products & services."""
        template_name = 'home/homepage.html'
        context_object_name = 'products'

        def get_context_data(self, **kwargs):
            context = super().get_context_data(**kwargs)
            # more code pending
            return context
    ```

    - By using `get_context_data()`, we are performing method overriding, where a method in a child class has the same name as a method in its parent class. This is necessary because `HomepageProductServiceView` extends or modifies the functionality of the `get_context_data` method from its parent class, accessing the returned `context` dictionary.
    - The overriding is done to customize or enhance the context data that is sent to the template. 
    - Within the overridden `get_context_data()` method, `super().get_context_data(**kwargs)` is used to call the same method from the parent class. This ensures that the base functionality of the method (as implemented in `ProductBaseListView`) is retained. This syntax should always be included.
    - After this, we can start manipulating the `context` dictionary for our purposes, as seen with the `products` and `services` local variables.

    ```python title="Products & Services Instances"
    class HomepageProductServiceView(ProductBaseListView):
        """Frontend main page displaying the list of products & services."""
        template_name = 'home/homepage.html'
        context_object_name = 'products'

        def get_context_data(self, **kwargs):
            context = super().get_context_data(**kwargs)

            products = list(context['products'][:3])
            services = list(Service.objects.filter(
                status=2).annotate(
                    likescount=Count('likes'),
            ).order_by('-created_on')[:3])
            
            # assignment pending
            return context
    ```

    - Here, we process the local variables `products` and `services`, manipulating the database to select the exact instances we need.
    - With our context dictionary ready, it's time to select our `products`. I access the `products` object within the `context` and select the first three instances using `[:3]`, which means from indices 0 to 2, totaling three instances. We convert these to a list using `list()` for immediate processing, instead of using the default `QuerySets` which are list-like and lazily evaluated.
    - We don't perform as many operations on `products` as on `services` because most processing is already done in the parent class.
    - For the `services` local variable, we follow a similar approach as with `products`, including slicing and converting the result to a list for all computations.
    - We filter the `services` objects, annotate each instance with a temporary value, and then sort them by date using `-created_on`. The minus sign `-` indicates reverse order, showing the newest objects first. Without the `-`, it would return the oldest objects first.

### How Does It Work?

1. **Inheritance and Extension**: Inherits from `ProductBaseListView` and extends its functionality for the specific needs of the homepage.

2. **Data Processing in `get_context_data`**:
   - **Slicing Instances**: Limits the number of products and services displayed by slicing them.
   - **Separation of Concerns**: Defines separate variables for products, services, and combined lists to manage them effectively.
   - **Combining Lists**: Intelligently combines products and services into a single list for a diversified display.
   - **Metrics Calculation**: Computes metrics like likes and purchase counts for each item.

3. **Custom Logic Application**:
   - Applies logic to arrange products and services in a particular order.
   - Ensures there's no redundancy in the items displayed.

4. **Context Enhancement**:
   - Adds these processed lists and metrics to the context, making them accessible in the template.

### Insights & Clean Code

```python title="HomepageProductServiceView"
class ProductBaseListView(ListView):
    """Base view for listing products based on these conditions."""
    model = Product

    def get_queryset(self):
        """Return products with a status of 2, ordered by creation date."""
        products = Product.objects.filter(status=2).annotate(
            likescount=Count('likes'),
        ).order_by('-created_on')

        return products


class HomepageProductServiceView(ProductBaseListView):
    """Frontend main page displaying the list of products & services."""
    template_name = 'home/homepage.html'
    context_object_name = 'products'

    def get_context_data(self, **kwargs):
        """
        1. Slice product instances to 3 (from parent class)
        2. Slice service instances to 3 (in current class)
        3. Define variables for separation of concerns & single view parameters
        4. Define a combined list to mix them as needed
        5. Fill single variables for a single display
        6. Extend products and services to the combined list, slice,
        and remove them for specific elements, avoiding redundancy and size
        7. Define metric variables
        8. Apply logic to the combined, product, and service rows
        9. Add variables holding data to the context to access them from
        the template
        """
        context = super().get_context_data(**kwargs)

        products = list(context['products'][:3])
        services = list(Service.objects.filter(
            status=2).annotate(
                likescount=Count('likes'),
        ).order_by('-created_on')[:3])

        product_single = []
        service_single = []

        combined_list = []

        # 1 product, 1 services, 1 product INTENTION
        if products and services:
            product_single.extend(products[:3])
            service_single.extend(services[:3])

            combined_list.extend(products[:1])
            products = products[2:]

            combined_list.extend(services[:1])
            services = services[3:]

            combined_list.extend(products[:1])
            products = products[1:]

        # Deal with frontend Metrics (Like counts & Purchases) consulting DB
        order_count_combined = {}
        order_count_product = {}
        order_count_service = {}

        for item in combined_list:
            if item.instance == 0:
                order_count = Order.objects.filter(
                    status=2, lineitems__product=item).count()
                order_count_combined[item.title] = order_count
            else:
                order_count = Order.objects.filter(
                    status=2, lineitems__service=item).count()
                order_count_combined[item.title] = order_count

        for item in product_single:
            if item.instance == 0:
                order_count = Order.objects.filter(
                    status=2, lineitems__product=item).count()
                order_count_product[item.title] = order_count

        for item in service_single:
            if item.instance == 1:
                order_count = Order.objects.filter(
                    status=2, lineitems__service=item).count()
                order_count_service[item.title] = order_count

        # Add collected data to the context allowing access from the template
        context['combined_items'] = combined_list
        context['product_single'] = product_single
        context['service_single'] = service_single
        context['order_count_combined'] = order_count_combined
        context['order_count_product'] = order_count_product
        context['order_count_service'] = order_count_service
        return context
```

This structure allows the `HomepageProductServiceView` to:

- **Efficiently Display Data**: By processing and preparing the data in the backend, it ensures an efficient and tailored display on the frontend.
- **Leverage Django's Features**: Utilizes Django's powerful ORM and class-based views for effective data handling and presentation.
- **Maintain Clean and Readable Code**: Keeps the code organized and easy to understand, facilitating future modifications and enhancements.

In summary, `HomepageProductServiceView` is a well-structured Django class that demonstrates effective use of inheritance, method overriding, and context management to display a curated list of products and services on a website's homepage.

-----------------------