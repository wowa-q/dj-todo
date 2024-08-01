Password must not be stored as a plain text, but hashed.

Django prebuilt apps need to be used:
- “django.contrib.auth” and 
- “django.contrib.contenttypes”


The apps we will use are 
- “django.contrib.auth” and 
- “django.contrib.contenttypes”


```
pip install bcrypt
pip install django[argon2]
```
> [Password validation doc](https://docs.djangoproject.com/en/5.0/ref/settings/#auth-password-validators)

```python
PASSWORD_HASHERS  =[
    
	'django.contrib.auth.hashers.Argon2PasswordHasher',
	'django.contrib.auth.hashers.BCryptPasswordHasher',
	'django.contrib.auth.hashers.BCryptSHA256PasswordHasher',
	'django.contrib.auth.hashers.PBKDF2PasswordHasher',
	'django.contrib.auth.hashers.PBKDF2SHA1PasswordHasher' 
]
```
here the password validotors can be configured to define rules for the password
```python
AUTH_PASSWORD_VALIDATORS = [
    {
        'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
    },
    {
        'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
    },
]
```

The user files will be stored in the media directory and not in the static. settings.py need to have configuration accordingly.

Import in the application model.py:
```python
from django.contrib.auth.models import User 
```

The User is build in into django. You must not inherit from the User class. Instead create own model to extend the User and make a one to one relation to the User table User and table UserProfile for example will look like this:

```python
from django.db import models
from django.contrib.auth.models import User     # default User model from django


# Create your models here.
class UserProfileInfo(models.Model):
    # create relationship (don't inherit from User)
    user = models.OneToOneField(User, on_delete=models.CASCADE)

    # Add any additional attributes you want
    portfolio = models.URLField(blank=True)
    picture = models.ImageField(upload_to='portfolio_pics', blank=True)  # to make python work with imagies pillow needs to be installed

    def __str__(self) -> str:
        return self.user.username
```

The user registration input is validated in the views:

```python
# registration view
def register(request):
    # this field will be checked in the template to root weither to the registration page or to any different page if the user is already registered.
    registered = False

    if request.method == 'POST':
        user_form = UserForm(data=request.POST)
        profile_form = UserProfileInfoForm(data=request.POST)

        if user_form.is_valid() and profile_form.is_valid():
            # to save the information to the data-base
            user = user_form.save()   
            # set_password does the hashing of the password
            user.set_password(user.password)
            user.save()

            # commit False to not cause coalisiion with the user.save() from above
            profile = profile_form.save(commit=False) 
            profile.user = user # 1:1 relationship as defined in models.py

            if 'profile_pic' in request.FILES:
                profile.picture = request.FILES['profile_pic']
            profile.save()
            
            registered = True
        else:
            print(user_form.errors, profile_form.errors)
    else:
        user_form = UserForm()
        profile_form = UserProfileInfoForm()

    return render(request, 'l5app/registration.html', 
                    {
                      'user_form':user_form,
                      'profile_form':profile_form,
                      'registeres':registered
                      }
                    )
```

