.. highlight:: html+jinja

.. _filters:

Filters
-------

Variables can be modified by **filters**.  Filters are separated from the
variable by a pipe symbol (``|``) and may have optional arguments in
parentheses.  Multiple filters can be chained.  The output of one filter is
applied to the next.

For example, ``{{ name|striptags|title }}`` will remove all HTML Tags from
variable `name` and title-case the output (``title(striptags(name))``).

Filters that accept arguments have parentheses around the arguments, just like
a function call.  For example: ``{{ listx|join(', ') }}`` will join a list with
commas (``str.join(', ', listx)``).

The :ref:`builtin-filters` below describes all the builtin filters.
