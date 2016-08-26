.. highlight:: html+jinja

.. _expressions:

Expressions
-----------

Jinja allows basic expressions everywhere.  These work very similarly to
regular Python; even if you're not working with Python
you should feel comfortable with it.

Literals
~~~~~~~~

The simplest form of expressions are literals.  Literals are representations
for Python objects such as strings and numbers.  The following literals exist:

"Hello World":
    Everything between two double or single quotes is a string.  They are
    useful whenever you need a string in the template (e.g. as
    arguments to function calls and filters, or just to extend or include a
    template).

42 / 42.23:
    Integers and floating point numbers are created by just writing the
    number down.  If a dot is present, the number is a float, otherwise an
    integer.  Keep in mind that, in Python, ``42`` and ``42.0``
    are different (``int`` and ``float``, respectively).

['list', 'of', 'objects']:
    Everything between two brackets is a list.  Lists are useful for storing
    sequential data to be iterated over.  For example, you can easily
    create a list of links using lists and tuples for (and with) a for loop::

        <ul>
        {% for href, caption in [('index.html', 'Index'), ('about.html', 'About'),
                                 ('downloads.html', 'Downloads')] %}
            <li><a href="{{ href }}">{{ caption }}</a></li>
        {% endfor %}
        </ul>

('tuple', 'of', 'values'):
    Tuples are like lists that cannot be modified ("immutable").  If a tuple
    only has one item, it must be followed by a comma (``('1-tuple',)``).
    Tuples are usually used to represent items of two or more elements.
    See the list example above for more details.

{'dict': 'of', 'key': 'and', 'value': 'pairs'}:
    A dict in Python is a structure that combines keys and values.  Keys must
    be unique and always have exactly one value.  Dicts are rarely used in
    templates; they are useful in some rare cases such as the :func:`xmlattr`
    filter.

true / false:
    true is always true and false is always false.

.. admonition:: Note

    The special constants `true`, `false`, and `none` are indeed lowercase.
    Because that caused confusion in the past, (`True` used to expand
    to an undefined variable that was considered false),
    all three can now also be written in title case
    (`True`, `False`, and `None`).
    However, for consistency, (all Jinja identifiers are lowercase)
    you should use the lowercase versions.

Math
~~~~

Jinja allows you to calculate with values.  This is rarely useful in templates
but exists for completeness' sake.  The following operators are supported:

\+
    Adds two objects together. Usually the objects are numbers, but if both are
    strings or lists, you can concatenate them this way.  This, however, is not
    the preferred way to concatenate strings!  For string concatenation, have
    a look-see at the ``~`` operator.  ``{{ 1 + 1 }}`` is ``2``.

\-
    Subtract the second number from the first one.  ``{{ 3 - 2 }}`` is ``1``.

/
    Divide two numbers.  The return value will be a floating point number.
    ``{{ 1 / 2 }}`` is ``{{ 0.5 }}``.
    (Just like ``from __future__ import division``.)

//
    Divide two numbers and return the truncated integer result.
    ``{{ 20 // 7 }}`` is ``2``.

%
    Calculate the remainder of an integer division.  ``{{ 11 % 7 }}`` is ``4``.

\*
    Multiply the left operand with the right one.  ``{{ 2 * 2 }}`` would
    return ``4``.  This can also be used to repeat a string multiple times.
    ``{{ '=' * 80 }}`` would print a bar of 80 equal signs.

\**
    Raise the left operand to the power of the right operand.  ``{{ 2**3 }}``
    would return ``8``.

Comparisons
~~~~~~~~~~~

==
    Compares two objects for equality.

!=
    Compares two objects for inequality.

>
    `true` if the left hand side is greater than the right hand side.

>=
    `true` if the left hand side is greater or equal to the right hand side.

<
    `true` if the left hand side is lower than the right hand side.

<=
    `true` if the left hand side is lower or equal to the right hand side.

Logic
~~~~~

For `if` statements, `for` filtering, and `if` expressions, it can be useful to
combine multiple expressions:

and
    Return true if the left and the right operand are true.

or
    Return true if the left or the right operand are true.

not
    negate a statement (see below).

(expr)
    group an expression.

.. admonition:: Note

    The ``is`` and ``in`` operators support negation using an infix notation,
    too: ``foo is not bar`` and ``foo not in bar`` instead of ``not foo is bar``
    and ``not foo in bar``.  All other expressions require a prefix notation:
    ``not (foo and bar).``


Other Operators
~~~~~~~~~~~~~~~

The following operators are very useful but don't fit into any of the other
two categories:

in
    Perform a sequence / mapping containment test.  Returns true if the left
    operand is contained in the right.  ``{{ 1 in [1, 2, 3] }}`` would, for
    example, return true.

is
    Performs a :ref:`test <tests>`.

\|
    Applies a :ref:`filter <filters>`.

~
    Converts all operands into strings and concatenates them.

    ``{{ "Hello " ~ name ~ "!" }}`` would return (assuming `name` is set
    to ``'John'``) ``Hello John!``.

()
    Call a callable: ``{{ post.render() }}``.  Inside of the parentheses you
    can use positional arguments and keyword arguments like in Python:

    ``{{ post.render(user, full=true) }}``.

. / []
    Get an attribute of an object.  (See :ref:`variables`)


.. _if-expression:

If Expression
~~~~~~~~~~~~~

It is also possible to use inline `if` expressions.  These are useful in some
situations.  For example, you can use this to extend from one template if a
variable is defined, otherwise from the default layout template::

    {% extends layout_template if layout_template is defined else 'master.html' %}

The general syntax is ``<do something> if <something is true> else <do
something else>``.

The `else` part is optional.  If not provided, the else block implicitly
evaluates into an undefined object::

    {{ '[%s]' % page.title if page.title }}