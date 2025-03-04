
# [form](https://docs.djangoproject.com/en/5.1/topics/forms/) `forms.py`

With a Form a user interacts with the Application, enters or moduifies data and sends it back to the server.
The _form_ must specify two things:

- _where_: The url to which data shall be sent
- _how_: The HTTP method (`GET` or `POST`), which shall be used to send the data
    - `POST`: Shall be used for any request, which makes changes on a system (e.g. changes in a data base).
    - `GET`: Shall be used only for request which can't change the system.

Django handles 3 parts of the forms:

- preparing and restructuring data to make it ready for rendering
- create HTML forms for the data
- receive and process submitted forms and data from the client

## Form class
Dajngo provide the [`Form` class](https://docs.djangoproject.com/en/5.1/ref/forms/api/#django.forms.Form). 
Similar to the `model`, it describes how the object works and appears. And like the model fields refer to the 
database fields, the From fields refer to the HTML `<input>` elements.

The [ModelForm](https://docs.djangoproject.com/en/5.1/topics/forms/modelforms/#django.forms.ModelForm) maps the 
model fields with its representation in a form `<input>` elements.

``` python
from django.forms import ModelForm
from myapp.models import Article

# Create the form class.
class ArticleForm(ModelForm):
    class Meta:
        model = Article
        fields = ["pub_date", "headline", "content", "reporter"]
```
The field types are automatically maped btw. model and form.

### How to use forms in django

A best practise is to create an own `forms.py` file in the application folder, where different App-Forms can be defined. 

For rendering an object for a form:

- Fetch an object through a view (e.g. fetch it from the data base)
- Pass it to the template context 
- expnad it by using template variables

> Rendering a from object is almost the same like other objects with some differences. (e.g. displaying an empty model doesn't make sense unlike the empty form).

> When we are working with a model in a view, we typically retrive it from DB. If we work with a form in a view we instantiate it. 

``` python
from django import forms

class NameForm(forms.Form):
    your_name = forms.CharField(label="Your name", max_length=100)
```
The HTML `<label>` will be displaied as "Your name".

A Form instance has an [is_valid()](https://docs.djangoproject.com/en/5.1/ref/forms/api/#django.forms.Form.is_valid) method, which runs validation routines for all its fields. When this method is called, if all fields contain valid data, it will:

- return True
- place the form’s data in its cleaned_data attribute.

The form can be displaied and received via the same view:
``` python
def get_name(request):
    # if this is a POST request we need to process the form data
    if request.method == "POST":
        # create a form instance and populate it with data from the request:
        form = NameForm(request.POST)
        # check whether it's valid:
        if form.is_valid():
            # process the data in form.cleaned_data as required
            # redirect to a new URL:
            return HttpResponseRedirect("/thanks/")

    # if a GET (or any other method) we'll create a blank form
    else:
        form = NameForm()

    return render(request, "name.html", {"form": form})
```
The empty form will be displaied if the view was arrived via `GET` method or if form is not valid.

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
