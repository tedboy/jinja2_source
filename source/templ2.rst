.. highlight:: html+jinja

.. _variables:

Variables
---------

Template variables are defined by the context dictionary passed to the
template.

You can mess around with the variables in templates provided they are passed in
by the application.  Variables may have attributes or elements on them you can
access too.  What attributes a variable has depends heavily on the application
providing that variable.

You can use a dot (``.``) to access attributes of a variable in addition
to the standard Python ``__getitem__`` "subscript" syntax (``[]``).

The following lines do the same thing::

    {{ foo.bar }}
    {{ foo['bar'] }}

It's important to know that the outer double-curly braces are *not* part of the
variable, but the print statement.  If you access variables inside tags don't
put the braces around them.

If a variable or attribute does not exist, you will get back an undefined
value.  What you can do with that kind of value depends on the application
configuration: the default behavior is to evaluate to an empty string if
printed or iterated over, and to fail for every other operation.

.. _notes-on-subscriptions:

.. admonition:: Implementation

    For the sake of convenience, ``foo.bar`` in Jinja2 does the following
    things on the Python layer:

    -   check for an attribute called `bar` on `foo`
        (``getattr(foo, 'bar')``)
    -   if there is not, check for an item ``'bar'`` in `foo`
        (``foo.__getitem__('bar')``)
    -   if there is not, return an undefined object.

    ``foo['bar']`` works mostly the same with a small difference in sequence:

    -   check for an item ``'bar'`` in `foo`.
        (``foo.__getitem__('bar')``)
    -   if there is not, check for an attribute called `bar` on `foo`.
        (``getattr(foo, 'bar')``)
    -   if there is not, return an undefined object.

    This is important if an object has an item and attribute with the same
    name.  Additionally, the :func:`attr` filter only looks up attributes.