# Templates

Django needs a convenient way to generate HTML dynamically. For providing HTML Django can be used with different templates. Django provides own Django Template Language, but also supports [Jinja2](https://jinja.palletsprojects.com/en/stable/) template. Even own [Template](https://docs.djangoproject.com/en/5.1/howto/custom-template-backend/) can be created.

Loading template consists of finding the template for a given identifier and preprocessing it, usually compiling it to an in-memory representation. Rendering means interpolating the template with context data and returning the resulting string.


## Settings

For processing the templates, the template engine needs to be configured:

``` python
# settings.py

TEMPLATES = [
    {
        "BACKEND": "django.template.backends.django.DjangoTemplates",   # configure the template engine to be used
        "DIRS": [],                                                     # configure the template location e.g. [BASE_DIR / "templates"]
        "APP_DIRS": True,
        "OPTIONS": {
            # ... some options here ...
        },
    },
]
```

- `BACKEND`: Python path to a template engine. For Jinja ` django.template.backends.jinja2.Jinja2.` can be configured
- `DIRS`:  list of directories where the engine should look for template source files
- `APP_DIRS`: tells whether the engine should look for templates inside installed applications 
- `OPTIONS`: backend-specific settings


## [The Django template language](https://docs.djangoproject.com/en/5.0/ref/templates/language/)
### Short summary of basic syntax

- Comments: `{# this won't be rendered #}`
- Variable: `{{ variable_name }}`

**Dictionary** lookup, **attribute** lookup and **list-index** lookups are implemented with a dot notation:

```
{{ my_dict.key }}
{{ my_object.attribute }}
{{ my_list.0 }}
```

### Tag

> Tags are surrounded by `{%` and `%}` like this: `{% csrf_token %}`
> Tags can accept arguments: `{% cycle 'odd' 'even' %}`
> Some tags require beginning and ending tags: `{% if %}...{% endif %}`

#### IF Tag

Iterate through the object list:

```
{% if %}... {% else %}... {% endif %}
```
Example:
```
{% if Data.loggedIn is True %}
    {{ You are logged in as Data.name }}
{% else %}
    <h1> you are a guest here! </h1>
{% endif %}

{% if not Data.loggedIn is True %}
    {{ You are logged in as Data.name }}
{% else %}
    <h1> you are a guest here! </h1>
{% endif %}

{% if Data.name != 'Wowa' %}
    {{ You are logged in as Data.name }}
{% else %}
    <h1> you are a guest here! </h1>
{% endif %}
```
else is optional
> __Hint__: try to put the logc into the view, instead of into the template

##### operators

- not, or, and
- ==
- !=
- < or >
- in (to search in a list, dictionary etc.)
- is None (as in Python)

If conditions can be nested.

#### Iteration

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
'boards" is the list from the view
Is usually used to create a table, lists etc.:

```
...
<tbody>
    {% for board in boards %}
        <tr>{{ board.id }}</tr>
        <tr>{{ board.name }}</tr>
        <tr>{{ board.value }}</tr>
    {% endfor %}
</tbody>
```
{{ forloop.counter }}

### To render:

```
{{variable}}
```
Example:
. 
```
{{ board.name }}
```
Can also be used as html attributes:
```
<ol class="list-unstyled"> 
    {% for board in boards %}
        <li> 
            <input type="checkbox" id={{board.id}}> "{{board.name}}"
        </li>
    {% endfor %}
</ol>
{{ board.name }}
```

> put the variables in quotes "", to ensure the strings are displaied correctly, including spaces.

See [Iteration](#iteration)

### Page Layout

first create a base/master page which has the repeated items (e.g. nav bar). The mater page holds place holders like this:

```
{% block block_name %}
{% endblock block_name%}
```
Other teamplates can extend this template. This should be the very first line in the template:

```
{% extends 'base.html %}

{% block block_name %}
    <h1> Block content </h1>
{% endblock block_name%}
```

### Partial view

Some parts of the template can be maintained in a different file, which is included:

```
{% block block_name %}

{% include path/to/_template.html %}

{% endblock block_name%}
```
While parameter can be passed to the partial view:
```
{% for board in boards %}
    {{ board.name }}
    {% include path/to/_template.html with parname=board %}
{% endfor %}
```

## Filters

### add Filter
```
<!-- add Filter -->
{{2 | add:2}} <!-- add two ints -->
{{FirstName | add:" " | add: LastName}} <!-- add two ints -->
```

### String-Filter

```
<!-- String operation Filter -->
{{AboutMe | addslashes}} <!-- adds slash before every '. Can be useful for generation csv files -->
{{AboutMe | capfirst}} <!-- Makes the first letter capital. -->
{{AboutMe | lower}} 
{{AboutMe | upper}} 
{{AboutMe | title}} <!-- First letter of every word is capitlized -->

<!-- If AboutMe.name is empty -->
<p>AboutMe: {{ AboutMe.name|default:"Not available"}}</p>
<!-- If AboutMe.name is None -->
<p>AboutMe: {{ AboutMe.name|default_if_none:"Not available"}}</p>

{{ AboutMe.amount | floatformat: '3' }} <!-- 3 decimal  -->
{{ AboutMe.amount | floatformat: '3g' }} <!-- 31000 separator  -->
```

### Date Filter

```
{{ now }}
<!-- Date time Filter Week -->
{{now | date:"d"}} <!-- Day of month with leading zeros -->
{{now | date:"j"}} <!-- Day of month without leading zeros -->
{{now | date:"D"}} <!-- Day of the week e.g. "Sat" -->
{{now | date:"l"}} <!-- Day of the week full e.g. "Saterday" -->
{{now | date:"w"}} <!-- Day of the week as a number -->
{{now | date:"z"}} <!-- Day of the year as a number -->
{{now | date:"W"}} <!-- Week number -->
<!-- Date time Filter Month -->
{{now | date:"m"}} <!-- month number with leading zeros -->
{{now | date:"n"}} <!-- month number without leading zeros -->
{{now | date:"M"}} <!-- month name Uppeer e.g. "Dec" -->
{{now | date:"b"}} <!-- month name lower e.g. "dec" -->
{{now | date:"F"}} <!-- month name full e.g. December -->
{{now | date:"N"}} <!-- month name abraviation e.g. Dec -->
{{now | date:"t"}} <!-- number of days in the month -->

<!-- Date time Filter Year -->
{{now | date:"y"}} <!-- year number with leading zeros -->
{{now | date:"Y"}} <!-- year number without leading zeros -->

<!-- Date time Filter Time -->
{{now | date:"g"}} <!-- 12 hours format without leading zero -->
{{now | date:"G"}} <!-- 24 hours format without leading zero -->
{{now | date:"h"}} <!-- 12 hours format -->
{{now | date:"H"}} <!-- 24 hours format -->
{{now | date:"i"}} <!-- Minutes -->
{{now | date:"s"}} <!-- seconds -->
{{now | date:"u"}} <!-- micro secods -->
{{now | date:"a"}} <!-- a.m. or p.m. -->
{{now | date:"A"}} <!-- A.M. or P.M. -->
{{now | date:"f"}} <!-- Time in 12 hours format -->
{{now | date:"P"}} <!-- Time in 12 hours format with a.m. or p.m. -->
<!-- multiple Fiters are possible -->
{{now | date:"D d M Y"}} <!-- Sat 23 Dec 2023 -->
{{now | time:"H:i:s:u"}} <!-- 17:25:30:123456 -->
```

### Dictionary sort Filter

```
{{Processors | dictsort:"cores"}}

{% for Processor in Processors | dictsort:"cores" %}
    {{ Processor.name}} - {{Processor.cores}}

{% endfor %}

{{Processors | dictsortreversed:"cores"}}

{% for Processor in Processors | dictsortreversed:"cores" %}
    {{ Processor.name}} - {{Processor.cores}}
{% endfor %}
```

