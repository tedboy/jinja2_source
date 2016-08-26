.. highlight:: html+jinja

.. _tests:

Tests
-----

Beside filters, there are also so-called "tests" available.  Tests can be used
to test a variable against a common expression.  To test a variable or
expression, you add `is` plus the name of the test after the variable.  For
example, to find out if a variable is defined, you can do ``name is defined``,
which will then return true or false depending on whether `name` is defined
in the current template context.

Tests can accept arguments, too.  If the test only takes one argument, you can
leave out the parentheses.  For example, the following two
expressions do the same thing::

    {% if loop.index is divisibleby 3 %}
    {% if loop.index is divisibleby(3) %}

The :ref:`builtin-tests` below describes all the builtin tests.