.. _autoescape-extension:

Autoescape Extension
--------------------

**Import name:** `jinja2.ext.autoescape`

.. versionadded:: 2.4

The autoescape extension allows you to toggle the autoescape feature from
within the template.  If the environment's :attr:`~Environment.autoescape`
setting is set to `False` it can be activated, if it's `True` it can be
deactivated.  The setting overriding is scoped.
