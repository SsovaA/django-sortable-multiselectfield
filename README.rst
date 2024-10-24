django-sortable-multiselectfield
=======================

Originally forked from: https://github.com/goinnn/django-multiselectfield && https://github.com/kunalgrover05/django-sortable-multiselectfield

A new model field and form field. With this you can get a multiple select from a choices. Stores to the database as a CharField of comma-separated values. The form field allows sorting of the selected choices. The selected choices are shown at top in the order they were selected.

Supported Python versions: Only 3+

Sorting behavior
---------------
- All list of items are sortable. Inside a choice group, all items are sortable too.
- When the form is reloaded, all selected items show on top in sorted order, and unselected items go at bottom(in the same order they are defined).
- When sorting is changed, the form is marked as dirty and saved again.

Installation
============


Install with pip
----------------

.. code-block:: bash

    $ pip install git+https://github.com/SsovaA/django-sortable-multiselectfield

Configure your models.py
------------------------

.. code-block:: python

    from multiselectfield import MultiSelectField
    
    # ...
    
    MY_CHOICES = (('item_key1', 'Item title 1.1'),
                  ('item_key2', 'Item title 1.2'),
                  ('item_key3', 'Item title 1.3'),
                  ('item_key4', 'Item title 1.4'),
                  ('item_key5', 'Item title 1.5'))
    
    MY_CHOICES2 = ((1, 'Item title 2.1'),
                   (2, 'Item title 2.2'),
                   (3, 'Item title 2.3'),
                   (4, 'Item title 2.4'),
                   (5, 'Item title 2.5'))
    
    class MyModel(models.Model):
        
        # .....
        
        my_field = MultiSelectField(choices=MY_CHOICES)
        my_field2 = MultiSelectField(choices=MY_CHOICES2,
                                     max_choices=3,
                                     max_length=3)


In your settings.py
-------------------

.. code-block:: python

    INSTALLED_APPS = (
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.sites',
        'django.contrib.admin',

        #.....................#

        'multiselectfield',
    )


Customizing templates
---------------------

It is possible to customize the HTML of this widget in your form template. To do so, you will need to loop through ``form.{field}.field.choices``. Here is an example that displays the field label underneath/after the checkbox for a ``MultiSelectField`` called ``providers``:

.. code-block:: HTML+Django

    {% for value, text in form.providers.field.choices %}
      <div class="ui slider checkbox">
        <input id="id_providers_{{ forloop.counter0 }}" name="{{ form.providers.name }}" type="checkbox" value="{{ value }}"{% if value in checked_providers %} checked="checked"{% endif %}>
        <label>{{ text }}</label>
      </div>
    {% endfor %}


Django REST Framework
---------------------

Django REST Framework comes with a ``MultipleChoiceField`` that works perfectly with this:

.. code-block:: python

    from rest_framework import fields, serializers
    
    from myapp.models import MY_CHOICES, MY_CHOICES2

    class MyModelSerializer(serializers.HyperlinkedModelSerializer):
        # ...
        my_field = fields.MultipleChoiceField(choices=MY_CHOICES)
        my_field2 = fields.MultipleChoiceField(choices=MY_CHOICES2)
        # ...

Dependencies
=========================
Includes [JQuery UI](https://jqueryui.com/) library to allow sorting. It should be able to replace with any simpler JS libraries as well.

Known Bugs and Limitations
==========================

All tests pass on Django 1.4, 1.5, and 1.8+, so if you can, use a modern version of Django. However, if you must use Django 1.6 or 1.7 there are two known issues you will need to be aware of:

1. `Named groups <https://github.com/goinnn/django-multiselectfield/pull/30#issue-52149983>`_ do not render properly in Django 1.6. The workaround is to manually render the field in your form or use a custom widget. If your workaround is suitably generic, please submit a pull request with it.

2. Only in Django 1.6 and 1.7, due to `Django bug #9619 <https://code.djangoproject.com/ticket/9619>`_, passing a MultiSelectField to ``values()`` or ``values_list()`` will return the database representation of the field (a string of comma-separated values). The workaround is to manually call ``.split(',')`` on the result.

   The Django bug was introduced in Django 1.6 and is fixed in Django 1.8 and onward, so ``values()`` and ``values_list()`` return a vanilla Python list of values for Django <= 1.5 and Django >= 1.8.

   See `issue #40 <https://github.com/goinnn/django-multiselectfield/issues/40>`_ for discussion about this bug.

