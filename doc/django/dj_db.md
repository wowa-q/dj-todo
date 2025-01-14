
# Django DB

## DB operations


## Bulk operations

You can delete multiple model instances (i.e. database records) at once:
https://docs.djangoproject.com/en/5.1/topics/db/queries/#deleting-objects
You can update multiple model instances (i.e. database records) at once:
https://docs.djangoproject.com/en/5.0/ref/models/querysets/#bulk-update
You can create multiple model instances (i.e. database records) at once:
https://docs.djangoproject.com/en/5.0/ref/models/querysets/#bulk-create

## DB
Django cashes the queries. For perfomance it is better to store the quesry in a variable
db_index=True - helps to find the field quicker

models.Model classes provides methods to save, create and delete data in database
Model.objects is a field which provides