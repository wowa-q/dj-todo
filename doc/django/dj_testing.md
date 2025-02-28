# Testing in Django
- [Youtube - Lektion 1: Intro](https://www.youtube.com/watch?v=qwypH3YvMKc&list=PLbpAWbHbi5rMF2j5n6imm0enrSD9eQUaM&index=1)
- [Youtube - Lektion 2: URLS](https://www.youtube.com/watch?v=0MrgsYswT1c&list=PLbpAWbHbi5rMF2j5n6imm0enrSD9eQUaM&index=2)
- [Youtube - Lektion 3: Views](https://www.youtube.com/watch?v=hA_VxnxCHbo&list=PLbpAWbHbi5rMF2j5n6imm0enrSD9eQUaM&index=3)
- [Youtube - Lektion 4: Models](https://www.youtube.com/watch?v=IKnp2ckuhzg&list=PLbpAWbHbi5rMF2j5n6imm0enrSD9eQUaM&index=4)
- [Youtube - Lektion 5: Forms](https://www.youtube.com/watch?v=zUl-Tf-UEzw&list=PLbpAWbHbi5rMF2j5n6imm0enrSD9eQUaM&index=5)
- [Youtube - Lektion 6: Functional Testing](https://www.youtube.com/watch?v=28zdhLPZ1Zk&list=PLbpAWbHbi5rMF2j5n6imm0enrSD9eQUaM&index=6)

## Organization
Every app brings own tests. When the app is generated a _tests.py_ is generated. However, if more tests in different files need to be orgonized, this file needs to be deleted and a folder with the same name needs to be created, where different _test_xyz.py_ files will be stored. Following makes sense for a bigger app:
- test
  - test_urls.py
  - test_views.py
  - test_forms.py
  - test_model.py

Test implementation usually starts with URL,then views, forms and then models.

## Test urls

[Youtube - Lektion 2: URLS](https://www.youtube.com/watch?v=0MrgsYswT1c&list=PLbpAWbHbi5rMF2j5n6imm0enrSD9eQUaM&index=2)

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

[Youtube - Lektion 3: Views](https://www.youtube.com/watch?v=hA_VxnxCHbo&list=PLbpAWbHbi5rMF2j5n6imm0enrSD9eQUaM&index=3)

## Server response code

|Response code   |meaning   |Notes   
|--|--|--|
|200   |success   				| if everything is ok   
|500   |internal server error   |error   
|404   |page not found			|when the url was not found - check the _urlpatterns_ in project urls.py 
