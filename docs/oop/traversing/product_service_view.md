

# Cards Display

# Product & Services


```python title="Clean Code"
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