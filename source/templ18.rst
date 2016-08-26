.. highlight:: html+jinja

.. _autoescape-overrides:

Autoescape Extension
--------------------

.. versionadded:: 2.4

If the application enables the :ref:`autoescape-extension`, one can
activate and deactivate the autoescaping from within the templates.

Example::

    {% autoescape true %}
        Autoescaping is active within this block
    {% endautoescape %}

    {% autoescape false %}
        Autoescaping is inactive within this block
    {% endautoescape %}

After an `endautoescape` the behavior is reverted to what it was before.
