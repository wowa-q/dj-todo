# Testing in Django

## Organization
Every app brings own tests. When the app is generated a _tests.py_ is generated. However, if more tests in different files need to be orgonized, this file needs to be deleted and a folder with the same name needs to be created, where different _test_xyz.py_ files will be stored. Following makes sense for a bigger app:
- test
  - test_urls.py
  - test_views.py
  - test_forms.py
  - test_model.py

Test implementation usually starts with URL,then views, forms and then models.

## Test urls

``` python
from django.test import SimpleTestCase
from django.urls import reverse, resolve
from app1.views import IndexView, SchoolListView, SchoolDetailView

class MyTest(SimpleTestCase):

    def test_index_url_is_resolved(self):
        ''' test if the right view is used for the given url
        '''
        url = reverse('index')
        self.assertEquals(resolve(url).func.view_class, IndexView)

    #  path('schools/', views.SchoolListView.as_view(),name='list'),
    def test_school_list_is_resolved(self):
        '''test if the school list url is correctly resolved'''
        url = reverse('app1:list')
        self.assertEquals(resolve(url).func.view_class, SchoolListView)
    
    def test_school_detail_is_resolved(self):
        '''test if school/<int:pk> url is correctly resolved'''
        url = reverse('app1:school_detail', args=[200000])   # irgendein integer für pk
        self.assertEquals(resolve(url).func.view_class, SchoolDetailView)
```
The reverse function calculates the url from the view name. After that this url can be tested if the correct view will be retrieved from this url.
If function based view is implemented `resolve(url).func` is used to verify if the same function is retrieved. If Class Based View is implemented `view_class` needs to be used to verify if the same class is used.

## Test Views

