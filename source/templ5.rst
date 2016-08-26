.. highlight:: html+jinja

.. _comments:

Comments
--------

To comment-out part of a line in a template, use the comment syntax which is
by default set to ``{# ... #}``.  This is useful to comment out parts of the
template for debugging or to add information for other template designers or
yourself::

    {# note: commented-out template because we no longer use this
        {% for user in users %}
            ...
        {% endfor %}
    #}