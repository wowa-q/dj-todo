# `views.py` & urls.py

## General
Views are Python functions that receive an HttpRequest object and returns an HttpResponse object. Receive a request as a parameter and returns a response as a result. 
The view contains the logic for whatever is necessary to return response. The code itself can be anywhere, as long it is in the Python path. The view-functions are called by django, if it was wired with URL. This is why `request` is always the first parameter of the view function.

``` python
from  django.shortcuts  import  render  

# Create your views here.
def  index(request):
	return  render(request, 'l4app/index.html')

def  readModel(request):
	template = 'l4app/index.html'
	mod = MyModel("myName", "other Par")
	# disctionary, which is provided for rendering and can be used in template
	data = {
		'mymod': mod
	}
	return  render(request, template, data)
```

The data can be provided to be displaied via the template in the response as a dictionary like `data` in the listing above. In the template the data can be retrieved `mymod`.
To have this function beeing called, it needs to be wired via urls:
``` python
from django urls import path
from . import views

urlpatterns [
    # path("url", view_function, view_name)
    path("index", views.index(), name="index")
]
```
There is a project global url configuration and App specific url, which needs to be included into the global project url config.
> The order of the pathes in the list maters, since django opens the view, which fits as first to the url.

### Dynamic urls

`<id>`: here you can put any text. You don't care what is inside, but all these urls have to be handled the the assigned view. The `id` 
is the variable name, which holds the value and can be provided as a parameter to the view-function. The function needs to be defined 
like this: > `def foo(request, id)`

- `<str: id>`: Identifier is a string (e.g. domain.de/any_string)
- `<int: id>`: Identifier is an integer (e.g. domain.de/1)
- `<int:pk>/`: pk stands for primary key
- `<slug:slug>`:  Matches any slug string consisting of ASCII letters or numbers

[Custom identifier](https://docs.djangoproject.com/en/5.1/topics/http/urls/#registering-custom-path-converters) can be created. 

[urls](https://docs.djangoproject.com/en/5.1/topics/http/urls/)

### Redirect function

This function can redirect to the different URL:

``` python
def numeric_index(request, identifier):
    if identifier == 0:
        # challenges is the path from the project url configuration
        return HttpResponseRedirect("/challenges/" + "index") 
```
Then the url-dispatcher will call different view function. With this differnt urls can be catched and redirected to the single view-function. 

To not hard code the url path, the [`reverse`](https://docs.djangoproject.com/en/5.1/ref/urlresolvers/#django.urls.reverse) function can be used:
``` python
def numeric_index(request, identifier):
    if identifier == 0:
        new_url = reverse("index")
        # if the redirected url is dynamic, the identifier is provided in the args-list
        # new_url = reverse("challenge", args=[month]) 
        return HttpResponseRedirect(new_url)
```
[short cut function](https://docs.djangoproject.com/en/5.1/topics/http/shortcuts/)

Each view function is responsible for returning an HttpResponse object.
Django provides help for returning HTTP error codes:

``` python
from django.http import HttpResponse, HttpResponseNotFound

def my_view(request):
    # ...
    if foo:
        return HttpResponseNotFound("<h1>Page not found</h1>")
    else:
        return HttpResponse("<h1>Page was found</h1>")
```
The error codes are implemented as subclasses of HttpResponse: [request/response](https://docs.djangoproject.com/en/5.1/ref/request-response/#ref-httpresponse-subclasses). Even own HttpResponse can be created by providing HTTP status code into [HttpResonse](https://docs.djangoproject.com/en/5.1/ref/request-response/#django.http.HttpResponse).

### The Http404 exception

``` python
# instead
return HttpResponseNotFound("<h1>Page not found</h1>")

from django.http import Http404
from django.shortcuts import render
from polls.models import Poll


def detail(request, poll_id):
    try:
        p = Poll.objects.get(pk=poll_id)
    except Poll.DoesNotExist:
        # use
        raise Http404("Poll does not exist")
    return render(request, "polls/detail.html", {"poll": p})    
```
In order to show customized HTML when Django returns a 404, you can create an HTML template named 404.html and place it in the top level of your template tree. This template will then be served when DEBUG is set to False.

### Customizing error views

The django default error view can be adapted according to the project needs:

``` python

handler404 = "mysite.views.my_custom_page_not_found_view"
handler500 = "mysite.views.my_custom_error_view"
handler403 = "mysite.views.my_custom_permission_denied_view"
handler400 = "mysite.views.my_custom_bad_request_view"
```

### [Async views](https://docs.djangoproject.com/en/5.1/topics/async/)

As well as being synchronous functions, views can also be asynchronous (“async”) functions, normally defined using Python’s async def syntax. Django will automatically detect these and run them in an async context. However, you will need to use an async server based on ASGI to get their performance benefits.

``` python
import datetime
from django.http import HttpResponse


async def current_datetime(request):
    now = datetime.datetime.now()
    html = '<html lang="en"><body>It is now %s.</body></html>' % now
    return HttpResponse(html)
```

## Class based view [CBV](https://docs.djangoproject.com/en/5.1/topics/class-based-views/)

Instead of function a class can be used and is the more flexible and convinient way to create views. in the `URLconf` the class method `as_view()` needs to be called.
If only few parameters on the view shall be changed, these can be passed directly to the `as_view()` function:
``` python
# urls.py
urlpatterns = [
    path("about/", TemplateView.as_view(template_name="about.html")),
	path("", views.ReviewView.as_view(), name='review'),
]

``` 
See [List of Views](https://docs.djangoproject.com/en/5.1/ref/class-based-views/)

### [Generic views](https://docs.djangoproject.com/en/5.1/ref/class-based-views/base/#django.views.generic.base)
In the class the methods `get` and `post` need to be implemented:
``` python
class ReviewView(View):

    def post(self, request):
        form = ReviewModelForm(request.POST)
        if form.is_valid():
            # this is possible just because it is instance of ModelForm
            form.save()
            return HttpResponseRedirect('thanks')
        
        return render(request, "reviews/review.html", {'form':form})
    
    def get(self, request):
        form = ReviewModelForm()
        return render(request, "reviews/review.html", {'form':form})

```

#### [TemplateView](https://docs.djangoproject.com/en/5.1/ref/class-based-views/base/#django.views.generic.base.TemplateView)
For use-cases where just a template shall be displaied. The view has the mandatory field `template_name` (which template shall be displaied) and
`get_context_data()` method, to provide data.

``` python
from  django 

# Create your views here.
from django.views.generic import View, TemplateView

class IndexView(TemplateView):
	# the field holds the name of the template to be displaied
    template_name = 'index.html' 

	# this function injects data into the template
	def get_context_data(self, **kwargs):
		context = super().get_context_data(**kwargs)
		context['injectme'] = 'BASIC Injection'
		return context

```
`get_context_data(**kwargs)` Returns a dictionary representing the template context.
>Context

>- Populated (through ContextMixin) with the keyword arguments captured from the URL pattern that served the view.
>- You can also add context using the extra_context keyword argument for as_view().


### Special views

Django provides some built-in view classes for different use cases. Here are the most frequently used:

- [ListView ](https://docs.djangoproject.com/en/5.1/topics/class-based-views/generic-display/)
- [DetailView](https://docs.djangoproject.com/en/5.1/ref/class-based-views/generic-display/#django.views.generic.detail.DetailView)
- [DeleteView]()
- [UpdateView ]()
- [CreateView]()

#### ListView

The view must provide the model from which the data shall be loaded in the field `model`. Other fields are optional. 
The field `context_object_name` defines the context name, which can e refered from the template.
``` python
class SchoolListView(ListView):
    model = models.School
    # returns a context modelname_list -> school_list and it can be used in the templates
    # better however is to define own name, which can be used in a template:
    context_object_name = 'schools'
    template_name = 'app1/schools.html'
```

``` django
{% for publisher in schools %}
    <li>{{ publisher.name }}</li>
{% endfor %}
```
#### DetailView

`DetailView` and provide your own implementation of the `get_context_data` method. As default the DetailView provides the object (the model) to the template. 
But you can override it to send more:

``` python
class PublisherDetailView(DetailView):
    model = Publisher

    def get_context_data(self, **kwargs):
        # Call the base implementation first to get a context
        context = super().get_context_data(**kwargs)
        # Add in a QuerySet of all the books
        context["book_list"] = Book.objects.all()
        return context
```

Specifying model = Publisher is shorthand for saying queryset = Publisher.objects.all().
``` python
from django.views.generic import DetailView
from books.models import Publisher


class PublisherDetailView(DetailView):
    context_object_name = "publisher"
    queryset = Publisher.objects.all()
```
### **CRUD**
Usually project require to **create**, **read**, **update** and **delte** objects. **CRUD**

Here are examples for URL:
``` python
# to list all objects from the model School
path('schools/', views.SchoolListView.as_view(),name='list'),
# to show one specific school with a primary key: pk
path('schools/<int:pk>/', views.SchoolDetailView.as_view(), name='school_detail'),
path('create/', views.SchoolCreateView.as_view(), name='create'),
path('update/<int:pk>/', views.SchoollUpdateView.as_view(),name='update'), 
path('delete/<int:pk>/', views.SchoolDeleteView.as_view(),name='delete'), 
```

Views:
``` python
class SchoolDeleteView(DeleteView):
    model = models.School
	# is required to direct to another page after deleting
    success_url = reverse_lazy('app1:list')
class SchoollUpdateView(UpdateView):
	# only these fields from the model will be updated
    fields = ('name', 'principal')
    model=models.School
class SchoolCreateView(CreateView):
    model = models.School
    fields = ('name', 'principal', 'location')
```

