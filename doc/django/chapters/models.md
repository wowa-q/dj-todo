
# `models.py`

Is representation of the data base of the web site. Each class will be transformed into database tables. A models represents a table in the data base.

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
    # Table specific logic, can be implemented in the model methods
    def __str__(self) -> str:
        return f"Student: {self.name} {self.age}"
```
Django provides different predefined fields.

A model can have an inner class [Meta](https://docs.djangoproject.com/en/5.1/ref/models/options/) for defining additional options, which are not part of any field.


```python
class Person(models.Model):
  name = models.CharField(max_length=60) 

  class Meta:
        ordering = ["name"]        
```

## [Fields:](https://docs.djangoproject.com/en/5.1/ref/models/fields/#model-field-types)

The most important part of the model are the fields - class attributes. Each field should be instance of the appropriate Field class. Field class type determines:

- DB table coulmn type - which kind of data is stored in this column
- Default HTML widget
- Validation requirements - used in django admin and in automatically generated forms

Custom field types can be created: [custom fled types](https://docs.djangoproject.com/en/5.1/howto/custom-model-fields/)

### Field options

- `null` If True, Django will store empty values as NULL in the database. Default is False. 
- `blank` If True, the field is allowed to be blank. Default is False. 
- `help_text` Extra “help” text to be displayed with the form widget. It’s useful for documentation even if your field isn’t used on a form.
- `primary_key` If True, this field is the primary kex in the table
- `unique` If True, the field value must be unique in the table
- `choices` A sequence of 2-value tuples 

```python
class Person(models.Model):
  SHIRT_SIZES = { 
    "S": "Small", 
    "M": "Medium", 
    "L": "Large", 
  }
  name = models.CharField(max_length=60) 
  shirt_size = models.CharField(max_length=1, choices=SHIRT_SIZES) 
```
Each Field can have verbose name, which is taken as a first positional argument:

```python
  name = models.CharField("person's first name", max_length=60) 
```
> `ForeignKey`, `ManyToManyField` and `OneToOneField` require the first argument to be a model class

## Table Relationships

Django has different options to setup relationships btw. the models:

- one-to-many: `models.ForeignKey('ModelName', on_delete=models.CASCADE)` 
- one-to-one: `models.OneToOneField('Product', on_delete=models.CASCADE)` 
- many-to-many: `models.ManyToManyField('Product')` no `on_delete` attribute. 

#### ([One-to-Many](https://docs.djangoproject.com/en/5.1/ref/models/fields/#django.db.models.ForeignKey)) relationship
The normal relationship.

#### ([One-to-One](https://docs.djangoproject.com/en/5.1/ref/models/fields/#django.db.models.OneToOneField)) relationship
Similar to one-to-many relationship with `uniqu=True`

#### ([Many-to-Many](https://docs.djangoproject.com/en/5.1/ref/models/fields/#django.db.models.ManyToManyField)) relationship

#### spcial relationships

- Circular relationship: one model depends on the other and vice versa - can be realized like this:

```python
# circular relationship
class Product(models.Model):
  # ... other fields ...
  last_buyer = models.ForeignKey('User')
  
class User(models.Model):
  # ... other fields ...
  created_products = models.ManyToManyField('Product')

```

- Relation with itself - depends on the instances of the same table [Recursive](https://docs.djangoproject.com/en/5.1/ref/models/fields/#recursive-relationships)

```python
# same model relationship
class User(models.Model):
  # ... other fields ...
  friends = models.ManyToManyField('self') 

```

- Relationship with models other apps (built-in or custom apps)

```python
# relationship with tables from other apps
from django.db import models
from geography.models import ZipCode

class Restaurant(models.Model):
    # ...
    zip_code = models.ForeignKey(
        ZipCode,
        on_delete=models.SET_NULL,
        blank=True,
        null=True,
    )
```


## Misc

To be able to update the model in the admin site, the model needs to be registered in the app/admin.py:

``` python
from django.contrib import admin
from l5app.models import UserProfileInfo
# Register your models here.
admin.site.register(UserProfileInfo)
```

To make django aware about the existng model following steps have to be executed:

- register the app in the `settings.py`
- manage.py makemigrations: creates rules to do the migrations
- manage.py migrate: migrate (create/update) to the DB for all registered Apps


### Model shell operations

The shell can be strated with `manage.py shell`

| Operation |Example  |Notes  |
|--|--|--|
|Create an object without saving, |_board1=Board(name='', description='')_, |_Board_ inherits from models.Model and is a model of the App
|Save an object (create or update), |_board1.save()_, |built in API 
|Create and save an object in a data base, |_Board.objects.create(name='', description='')_, |built in API to create objects directly in the DB table
|Get a single object, identified by a field, |_Boards.objects.get(id=1)_, |the field must be unique, otherwise more objects will be returned 

```shell
books = Book.objects.all()
book = Book.objects.all()[0]
book.autor = "J.K. Rowling"
book.save()
book.delete()
Book.objects.create(autor="Max Meier")
Book.objects.filter(autor="Max Meier")
ctr = Book.objects.count()
book2 = Book.objects.get(id=2)
```

1. books is the list of the entries of the objects in the Book model
2. book is the first object in the list
3. update of the model field
4. save the changed data in DB
5. The object will be deleted from the table
6. Creates a new object directly in the DB Table
7. Returns the table entries with the requested parameters
8. Returns number of entries in the table
9. Get the book from the table by id - if not found raise model.DoesNotExist: Book matching query does not exist 

### [Database queries](https://docs.djangoproject.com/en/5.1/ref/models/querysets/#django.db.models.query.QuerySet)
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


#### DB Best Practise
Django cashes the queries. For perfomance it is better to store the quesry in a variable
`db_index=True` - helps to find the field quicker

`models.Model` classes provides methods to save, create and delete data in database
`Model.objects` is a field which provides 