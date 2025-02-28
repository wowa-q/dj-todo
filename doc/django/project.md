# Django project

## `settings.py`

### Register new apps into the project

> **INSTALLED_APPS**: has all Apps in the project, including base Django Apps
```python
 # Application definition
INSTALLED_APPS  = [
'django.contrib.admin',
'django.contrib.auth',			# built in authentification app
'django.contrib.contenttypes',	# built in authentification app
'django.contrib.sessions',
'django.contrib.messages',
'django.contrib.staticfiles',
'l4app' # Name of the App which shall be used in the projects
]
```

### Django Templates configuration

To use predefined templates the *settings.py* needs to be updated.

> **DIRS**: Is a dictionary within the **TEMPLATES** list and needs to include the path for the templates

```python
BASE_DIR  =  Path(__file__).resolve().parent.parent
TEMPLATES_DIR  =  BASE_DIR  /  "templates"
STATIC_DIR  =  BASE_DIR  /  "static"		# these files are used by the server
MEDIA_DIR  =  BASE_DIR  /  "media"			# these files are from the users (clients)
```
```python
TEMPLATES  = [
	{
		'BACKEND': 'django.template.backends.django.DjangoTemplates',
		'DIRS': [TEMPLATES_DIR],	# the path pointing to the templates folder
		'APP_DIRS': True,
		'OPTIONS': {
			'context_processors': [
				'django.template.context_processors.debug',
				'django.template.context_processors.request',
				'django.contrib.auth.context_processors.auth',
				'django.contrib.messages.context_processors.messages',
			],
		},
	},
]
```
### Django static files configuration [doc](https://docs.djangoproject.com/en/5.0/howto/static-files/)

To use static files, such as images, css etc. the path needs to be configured in *settings.py*.
Firt make sure make sure this APP is included:
`'django.contrib.staticfiles',`
```python
# Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/5.0/howto/static-files/

STATIC_URL  =  '/static/'
STATICFILES_DIRS  = [
	STATIC_DIR,
]
```
> STATIC_DIR was defined (see the listing above): `STATIC_DIR  =  BASE_DIR  /  "static"`

The path for the media is configured similarly:
```python
# MEDIA

MEDIA_ROOT = MEDIA_DIR
MEDIA_URL  =  '/media/'
```

### User and password management [doc](https://docs.djangoproject.com/en/5.0/ref/settings/#auth-password-validators)
Django provides built in apps to handle the password validation. Make sure these Apps are installed
```python
INSTALLED_APPS  = [
'django.contrib.auth',			# built in authentification app
'django.contrib.contenttypes',	# built in authentification app
```
Different password hashers can be used to not store the password as plain text. To use them the _settings.py_ needs to be have following configuration:

```python
PASSWORD_HASHERS  =[
	'django.contrib.auth.hashers.Argon2PasswordHasher',
	'django.contrib.auth.hashers.BCryptPasswordHasher',
	'django.contrib.auth.hashers.BCryptSHA256PasswordHasher',
	'django.contrib.auth.hashers.PBKDF2PasswordHasher',
	'django.contrib.auth.hashers.PBKDF2SHA1PasswordHasher' 
]
AUTH_PASSWORD_VALIDATORS  = [
	{
		'NAME': 'django.contrib.auth.password_validation.UserAttributeSimilarityValidator',
	},
	{
		'NAME': 'django.contrib.auth.password_validation.MinimumLengthValidator',
		'OPTIONS': {'min_length': 9},	# the password will be required as minimum 9 characters long
	},
	{
		'NAME': 'django.contrib.auth.password_validation.CommonPasswordValidator',
	},
	{
		'NAME': 'django.contrib.auth.password_validation.NumericPasswordValidator',
	},
]
```

## `urls.py` [doc](https://docs.djangoproject.com/en/5.0/topics/http/urls/)

>`from APP_NAME import views`
The Application views need to be imported, so they views can be called at the url match
- `url(r'^$', views.FUNCTIONNAME, name='home')`, part of url-patterns
	- `'^$'` regex, will match an empty path

```python
1 from  django.contrib  import  admin
2 from  django.urls  import  path, include
3 from  l4app  import  views

4 urlpatterns  = [
5 	path('admin/', admin.site.urls),		# django built in url for admin page
6	path('l4app/', include('l4app.urls')), 	# includes application specific url definition
]
```
>3: import the views from the application with the name *l4app*.

>6: all urls, starting with *l4app/* will be routed to the application url definition. The App must have a *urls.py* where `urlpatterns` is defined.

For debugging following statements can help:
```python
print(request.build_absolute_uri())
print(reverse("favorite")) # favorite is the url name
```

