# Customizing admin pages

**base_site.html** can be copied and modified in the templates folder. Django will use this instead of standard, which is located in: [git](https://github.com/django/django/tree/main/django/contrib/admin/templates/admin): 
contrib/admin/templates/admin. This folder has the templates for admin standard page.

## Admin page configuration

``` python
# admin.py example
from django.contrib import admin
from . import models
# Convention: ModelnameAdmin
class MovieAdmin(admin.ModelAdmin):
    fileds = [
        'year',
        'title',
        # 'length'
    ]
    search_fields = ['year']
    readonly_fields = ("title", "year") # these fields can't be modified
    prepopulated_fields = {"slug": ("title",)} # Voransicht - darf nicht readonly_filed sein
    
admin.site.register(models.Customer)
admin.site.register(models.Movie, MovieAdmin) # register the admin class from above
```


### Add search in admin pages

add: `search_fields = ['field_name_of_the_model', 'another_filed]`
This will add a search field to the admin page, wich can be used in the configured fields.
``` python
# admin.py example
from django.contrib import admin
from . import models
# Convention: ModelnameAdmin
class MovieAdmin(admin.ModelAdmin):
    fileds = [
        'year',
        'title',
        # 'length'
    ]
    search_fields = ['year']
    
admin.site.register(models.Customer)
admin.site.register(models.Movie, MovieAdmin) # register the admin class from above
```

## Filter

add `list_filter = ['filead_name', 'another_filed]]` to the Admin-class

## Display table view

add `list_display = ['filead_name', 'another_filed]]` to the Admin-class and thee columns will be shown in the admin page