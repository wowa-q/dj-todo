# Dajngo App

## `views.py` 

Views are Python functions that receive an HttpRequest object and returns an HttpResponse object. Receive a request as a parameter and returns a response as a result.
``` python
from  django.shortcuts  import  render  

# Create your views here.
def  index(request):
	return  render(request, 'l4app/index.html')
```

> Often regular expression is useful. [Here the regular expression can be tested](https://regex101.com/)

## `models.py`

Is representation of the data base of the web site. Each class will be transformed into database tables.

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

## `forms.py`
The file can be created in an Application to define own Forms.

### Defining a model Form
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