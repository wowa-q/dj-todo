
- [The Django template language](https://docs.djangoproject.com/en/5.0/ref/templates/language/)

# Django Template Language
Iterate through the object list:
```
{% for ... in ... %}
```
Example:
```
{% for board in boards %}
    {{ board.name }}
{% endfor %}
```
To render:
```
{{variable}}
```
Example:
. 
```
{{ board.name }}
```