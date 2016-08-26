.. highlight:: html+jinja

.. _import-visibility:

Import Context Behavior
-----------------------

By default, included templates are passed the current context and imported
templates are not.  The reason for this is that imports, unlike includes,
are cached; as imports are often used just as a module that holds macros.

This behavior can be changed explicitly: by adding `with context`
or `without context` to the import/include directive, the current context
can be passed to the template and caching is disabled automatically.

Here are two examples::

    {% from 'forms.html' import input with context %}
    {% include 'header.html' without context %}

.. admonition:: Note

    In Jinja 2.0, the context that was passed to the included template
    did not include variables defined in the template.  As a matter of
    fact, this did not work::

        {% for box in boxes %}
            {% include "render_box.html" %}
        {% endfor %}

    The included template ``render_box.html`` is *not* able to access
    `box` in Jinja 2.0. As of Jinja 2.1, ``render_box.html`` *is* able
    to do so.