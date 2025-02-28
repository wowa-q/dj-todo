# Dajngo App

## `views.py` 

Views are Python functions that receive an HttpRequest object and returns an HttpResponse object. Receive a request as a parameter and returns a response as a result.
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

> Often regular expression is useful. [Here the regular expression can be tested](https://regex101.com/)

### Class based view [CBV](https://docs.djangoproject.com/en/5.1/topics/class-based-views/)
Instead of function a class can be used and is the more flexible and convinient way to create views. In the class the methods `get` and `post` need to be implemented:

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

This is how the url shall be adapted.
``` python
# urls.py
urlpatterns = [
    path("about/", TemplateView.as_view(template_name="about.html")),
	path("", views.ReviewView.as_view(), name='review'),
]

``` 

Besides the generic View-Class, there are plenty of spetialized View-Classes e.g. `TemplateView`:

#### TemplateView


``` python
from  django 

# Create your views here.
from django.views.generic import View, TemplateView

class IndexView(TemplateView):
	template_name = 'index.html' # the field holds the name of the template

	# this function injects data into the template
	def get_context_data(self, **kwargs):
		context = super().get_context_data(**kwargs)
		context['injectme'] = 'BASIC Injection'
		return context

```
Django provides some built-in view classes for different use cases. Here are the most frequently used:
- ListView 
- DetailView
- DeleteView
- UpdateView 
- CreateView

``` python
class SchoolListView(ListView):
    model = models.School
    # returns a context modelname_list -> school_list and it can be used in the templates
    # better however is to define own name:
    context_object_name = 'schools'
    template_name = 'app1/schools.html'
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


## `models.py`

Is representation of the data base of the web site. Each class will be transformed into database tables.

A models represents a table in the data base.


### model shell operations

| Operation |Example  |Notes  |
|--|--|--|
|Create an object without saving, |_board=Board()_, |_Board_ inherits from models.Model 
|Save an object (create or update), |_board.save()_, |built in API 
|Create and save an object in a data base, |_Board.objects.create(name='', description='')_, |built in API 
|Get a single object, identified by a field, |_Boards.objects.get(id=1)_, |the field must be unique, otherwise more objects will be returned 

To be able to update the model in the admin site, the model needs to be registered in the app/admin.py:
``` python
from django.contrib import admin
from l5app.models import UserProfileInfo
# Register your models here.
admin.site.register(UserProfileInfo)
```
Here an example for a model:

``` python
class School(models.Model):
    name = models.CharField(max_length=256)
    principal = models.CharField(max_length=256)

    def __str__(self) -> str:
        return self.name

class Student(models.Model):
    name = models.CharField(max_length=256)
    age = models.PositiveIntegerField()
    location = models.ForeignKey(School,
								# related_name will be used in the templates
                                 related_name='students',    
                                 on_delete=models.CASCADE)

    def __str__(self) -> str:
        return self.name
```
Django provides different predefined fields.

### Database queries
models.Model classes provides methods to save, create and delete data in database Model.objects is a field.

Django cashes the queries. For perfomance it is better to store the quesry in a variable `db_index=True` - helps to find the field quicker

```python

from .models import App

App.objects.all() # is a query to get all entries from the table App
App.objects.filter(title='my-Name') # searching for the entry with this title

```

#### Bulk operations

- You can delete multiple model instances (i.e. database records) at once: [dajngo delete object](https://docs.djangoproject.com/en/5.1/topics/db/queries/#deleting-objects)

- You can update multiple model instances (i.e. database records) at once: [django update objects](https://docs.djangoproject.com/en/5.0/ref/models/querysets/#bulk-update)
- You can create multiple model instances (i.e. database records) at once: [django create](https://docs.djangoproject.com/en/5.0/ref/models/querysets/#bulk-create)

### Table fields

Django has different options to setup relationships btw. the models:
- one-to-many: `models.ForeignKey('ModelName', on_delete=models.CASCADE)`
- one-to-one: `models.OneToManyOne('Product', on_delete=models.CASCADE)`
- many-to-many: `models.ManyToManyField('Product')` no `on_delete` attribute.

#### spcial relationships

1. Circular relationship: one model depends on the other and vice versa - can be realized like this:

```python
# circular relationship
class Product(models.Model):
  # ... other fields ...
  last_buyer = models.ForeignKey('User')
  
class User(models.Model):
  # ... other fields ...
  created_products = models.ManyToManyField('Product')

```

2. Relation with itself - depends on the instances of the same table

```python
# same model relationship
class User(models.Model):
  # ... other fields ...
  friends = models.ManyToManyField('self') 

```

3. Relationship with models other apps (built-in or custom apps)

```python
# relationship with tables from other apps
class Review(models.Model):
  # ... other fields ...
  product = models.ForeignKey('store.Product') # '<appname>.<modelname>'
```


## `forms.py`
The file can be created in an Application to define own Forms.


### Defining a user Form

``` python
def  check_for_z(value): # can be used as input validator e.g. to validate name field
	if  value[0].lower() !=  'z':
		raise  forms.ValidationError("Name needs to start with z")

class  FormName(forms.Form):
	name  =  forms.CharField(validators=[check_for_z])
	email  =  forms.EmailField()
	verify_email  =  forms.EmailField(label='repeat your email')
	text  =  forms.CharField(widget=forms.Textarea)
	# this is to catch the bots
	botcatcher  =  forms.CharField( required=False,	
									# The field is hidden - user can't enter any data here
									widget=forms.HiddenInput, 
									# field length must be 0, if not it was filled by a bot
									validators=[validators.MaxLengthValidator(0)]) 
	  
	def clean(self):
		all_clean_data  =  super().clean()
		email  =  all_clean_data['email'] 		# email is der name des fields
		vemail  =  all_clean_data['verify_email']
		if  email  !=  vemail:
			raise  forms.ValidationError('email not the same!')
```
The listing is showing how to catch bots in the Form, where user can enter some data.

#### Setting up form fields

``` python
# forms.py
class ReviewForm(forms.Form):
    user_name = forms.CharField(label='Enter your name',
                                max_length=10, 
                                error_messages={
                                    "required": "Your name must not be empty",
                                    "max_length": "Your maximum length was achieved"
                                })
    rating = forms.IntegerField(max_value=5,
                                min_value=1,
                                label='Your rating',
                                error_messages={
                                    'max_value': 'Rating is higher than allowed',
                                    'min_value': 'Rating is lower than allowed'
                                })
    review_text = forms.CharField(label='Your feedback',
                                  widget=forms.Textarea,
                                  )
```
- the error message can be adjusted per form-field
- different types of the fields are provided by django
- the widget can be adjusted e.g. user_name is html input with the type=text and review_text is html textarea tag
- some validations can be configured for the form

``` python
# views.py
def review(request):
    if request.method == 'POST':
        form = ReviewForm(request.POST)
        # check if entered form data were valid
		if form.is_valid():
            print(form.cleaned_data)
			# will redirect to the url with the name 'thanks'
            return HttpResponseRedirect('thanks')
        else:
            pass
    else:
        form = ReviewForm()
    # form instance is provided and can be used in the template
    return render(request, "reviews/review.html", {'form':form})
```

The From can be built in a for Loop:
- the class "error" will be set only if from is not is_valid()
- the 'error' class has own styling -> the field causing error will get own styling and user can focus on fixing those
- the fields from the `ReviewForm` will be displaid by the for loop

``` django html
{% comment %} review.html {% endcomment %}
{% for field in form  %}

    <div class="form-control {% if field.errors %}errors{% endif %}">
        {{field.label_tag}}
        {{field}}
        {{field.errors}}  
    </div>

{% endfor %}
```
### Defining a model Form

Instead of `forms.Form` inherit from `forms.ModelForm`. This simplifies the updating the datbase from the data provided by the form. THe configuration of the form is happening in the Meta class.

``` python
# forms.py
from django import forms
from .models import Review

class ReviewModelForm(forms.ModelForm):    

    class Meta:
        model = Review
        fields = '__all__'
        labels = {
            'user_name': 'Your Name',
            'text': 'Your review text',
            'rating': 'Your Rating'
        }
        error_messages={
            
            'user_name': {
                'required': 'Your name must not be empty',
                'max_length': 'Your maximum length was achieved'
            },
            'rating': {
                'max_value': 'Rating is higher than allowed',
                'min_value': 'Rating is lower than allowed'
            },
            'text': {}
        }
```
This allows to directly save the data provided via form to the database:

``` python
# views.py
...

def review(request):
    if request.method == 'POST':
        form = ReviewModelForm(request.POST)
        if form.is_valid():
            # this is possible just because it is instance of ModelForm
            form.save()
            return HttpResponseRedirect('thanks')
    else:
        form = ReviewModelForm()
    
    return render(request, "reviews/review.html", {'form':form})

```

Steps how to use ModelForm:

``` python
1 from  django  import  forms
2 from  l5app.models  import  UserProfileInfo  

3 class  UserProfileInfoForm(forms.ModelForm):
4	portfolio  =  forms.URLField(required=False)
5	picture  =  forms.ImageField(required=False)  

6	class  Meta():
7		model  =  UserProfileInfo
8		exclude  = ('user', )
```
1. import django forms
2. import app model. Here `UserProfileInfo` model will be used
3. create a class which inherits from django `forms.ModelForm`
4. define the fields from the model and the Form type from django
5. define another field
6. define a Meta class within the user-defined Form class (needs to be there)
7. mapping to the model from the app
8. exclude `user` field from the form. There are different way to exclude or include fields e.g. `fields  ='__all__` can be used to include all fields from the model.

## File handling in Django

### configuration

In _settings.py_ the `MEDIA_ROOT` needs to be configured to set the path where django will store the files. This allows only storage of the files however. To be able also to serve the files, the URL needs to be set via `MEDIA_URL`. The reason is, that browser can't access the file system by security reason. It can acces data only by an URL.
```python
# settings.py

MEDIA_ROOT = BASE_DIR / "uploads"
MEDIA_URL = "/user-madia/"
```

After this configuration the URL needs to be made visible to django for serving the uploaded files. Therefore the project urls needs to be updated:

```python
# urls.py

from django.contrib import admin
from django.urls import path, include
from django.conf.urls.static import static
from django.conf import settings

urlpatterns = [
    path('admin/', admin.site.urls),
    path("", include('reviews.urls')),
    path("prof", include('profiles.urls'))
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)

```
With adding static function, django will handle the uploaded files as additional static files.


The dajngo object of `class UploadedFile`
[UploadedFile](https://docs.djangoproject.com/en/5.1/ref/files/uploads/)
The instance of this class will be provided by the `request.POST` attribute:

```python
def post(self, request):
        uploaded_file = request.FILES["image"]  # 'image' is defined in html name='image'
```

The instance of the `UploadedFile` provides methods:
- read
- multiple_chunks
- chunks
- 
and attributes:
- name
- size
- content_type
- content_type_extra
- charset

There are some childs of the `UploadedFile`:
- `TemporaryUploadedFile`
- `InMemoryUploadedFile`

### Using Form for File-Upload

```python
# forms.py
from django import forms

class ProfileForm(forms.Form):
    image = forms.FileField(
        label='User image file:'
    )
```

- `FileField` defines the name of the file which is stored 
  - The label of the file-input widget can be configured with the parameter `label`

The Form can be used then in the views as following:

```python
# views.py
from .forms import ProfileForm

def store_file(file):
    """helper function it stores the file in chuks
    Args: file (FileUploaded): File uploaded via form and post method
    """
    with open("temp/image.jpg", "wb+") as dest:
        for chunk in file.chunks():
            dest.write(chunk)

class CreateProfileView(View):
    # Opens the Form, to be filled and submitted by the user
    def get(self, request):
        form_instance = ProfileForm()

        return render(request, 
                      "profiles/create_profile.html", 
                      {"form": form_instance})
    # submits the form after validation 
    def post(self, request):
        submitted_form = ProfileForm(request.POST, request.FILES)
        if submitted_form.is_valid():
            if 'image' in request.FILES:
                store_file(request.FILES['image']) # defined in form-fields (see above)
            # Redirection if submitting was successful
            return HttpResponseRedirect("prof")
        else:
            # in case of an error the form will be just reloaded
            form_instance = ProfileForm()
            return render(request, 
                      "profiles/create_profile.html", 
                      {"form": form_instance})
```

### Using `ModelForm` for File-Upload

```python
# forms.py
from django import forms

class ProfileForm(forms.Form):
    image = forms.FileField(
        label='User image file:'
    )
```

### Using Model for File-Upload

Django soesn't store the files in a data base, but only the path to the file on the hard drive.

```python
# models.py
from django import models

class UserProfile(models.Model):
    # the path is the subfolder of MEDIA_ROOT
    image = models.FileField(upload_to="images")
    # if Pillow package is installed for images an ImageField can be used
    # the ImageField has extra logic to validate if the selected file is an image
    # image = models.ImageField(upload_to="images")
```
Using model for File-Uploads simplifies the logic:
```python
# views.py
from .forms import ProfileForm
from .models import UserProfile

class CreateProfileView(View):
    # Opens the Form, to be filled and submitted by the user
    def get(self, request):
        form_instance = ProfileForm()
        return render(request, 
                      "profiles/create_profile.html", 
                      {"form": form_instance})
    # submits the form after validation 
    def post(self, request):
        submitted_form = ProfileForm(request.POST, request.FILES)
        if submitted_form.is_valid():
            # model field is set to request.FILES['name of the forms field']
            profile = UserProfile(image=request.FILES['image'])
            # Django updates the database and stores the file under configured location 
            # -> manual storage is not needed anymore
            profile.save()  
            return HttpResponseRedirect("prof")
        else:
            # in case of an error the form will be just reloaded
            form_instance = ProfileForm()
            return render(request, 
                      "profiles/create_profile.html", 
                      {"form": form_instance})
```

The file properties can be read after upload from the model object:

```python
from profiles.models import UserProfile
# returns the absolute path of the file:
UserProfile.objects.all()[0].image.path
# returns the file size as integer
UserProfile.objects.all()[0].image.size
```

If models is used for File Upload, the view can be simplified further by using `CreateView`:

```python
# views.py
from django.views.generic.edit import CreateView
from django.views.generic import ListView

from .forms import ProfileForm
from .models import UserProfile

class CreateProfileView(CreateView):
    template_name = "profiles/create_profile.html"
    model = UserProfile
    fields = "__all__" # to process all the fields of the model in the template
    success_url = "profiles"
```

## Sessions

Sessions are meant to have temporary, but long living data. Espcially for User specific data Sessions are very useful. To store Session data coockies are stored on the client and the server can read the session data from there.

To use Django Session features the `SessionMiddleware` needs to be included in the _settings.py_ as well as the app `'django.contrib.sessions'` is installed:
```python
# settings.py

MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

INSTALLED_APPS = [
    'reviews',
    'profiles',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]

SESSION_COOKIE_AGE = 12000 # seconds (default is set to two weeks)
```
With this configuration django features are supported, including the coockies starage etc.
