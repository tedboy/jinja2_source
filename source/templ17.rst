.. highlight:: html+jinja

Extensions
----------

The following sections cover the built-in Jinja2 extensions that may be
enabled by an application.  An application could also provide further
extensions not covered by this documentation; in which case there should
be a separate document explaining said :ref:`extensions
<jinja-extensions>`.

.. _i18n-in-templates:

i18n
~~~~

If the i18n extension is enabled, it's possible to mark parts in the template
as translatable.  To mark a section as translatable, you can use `trans`::

    <p>{% trans %}Hello {{ user }}!{% endtrans %}</p>

To translate a template expression --- say, using template filters, or by just
accessing an attribute of an object --- you need to bind the expression to a
name for use within the translation block::

    <p>{% trans user=user.username %}Hello {{ user }}!{% endtrans %}</p>

If you need to bind more than one expression inside a `trans` tag, separate
the pieces with a comma (``,``)::

    {% trans book_title=book.title, author=author.name %}
    This is {{ book_title }} by {{ author }}
    {% endtrans %}

Inside trans tags no statements are allowed, only variable tags are.

To pluralize, specify both the singular and plural forms with the `pluralize`
tag, which appears between `trans` and `endtrans`::

    {% trans count=list|length %}
    There is {{ count }} {{ name }} object.
    {% pluralize %}
    There are {{ count }} {{ name }} objects.
    {% endtrans %}

By default, the first variable in a block is used to determine the correct
singular or plural form.  If that doesn't work out, you can specify the name
which should be used for pluralizing by adding it as parameter to `pluralize`::

    {% trans ..., user_count=users|length %}...
    {% pluralize user_count %}...{% endtrans %}

It's also possible to translate strings in expressions.  For that purpose,
three functions exist:

-   `gettext`: translate a single string
-   `ngettext`: translate a pluralizable string
-   `_`: alias for `gettext`

For example, you can easily print a translated string like this::

    {{ _('Hello World!') }}

To use placeholders, use the `format` filter::

    {{ _('Hello %(user)s!')|format(user=user.username) }}

For multiple placeholders, always use keyword arguments to `format`,
as other languages may not use the words in the same order.

.. versionchanged:: 2.5

If newstyle gettext calls are activated (:ref:`newstyle-gettext`), using
placeholders is a lot easier:

.. sourcecode:: html+jinja

    {{ gettext('Hello World!') }}
    {{ gettext('Hello %(name)s!', name='World') }}
    {{ ngettext('%(num)d apple', '%(num)d apples', apples|count) }}

Note that the `ngettext` function's format string automatically receives
the count as a `num` parameter in addition to the regular parameters.


Expression Statement
~~~~~~~~~~~~~~~~~~~~

If the expression-statement extension is loaded, a tag called `do` is available
that works exactly like the regular variable expression (``{{ ... }}``); except
it doesn't print anything.  This can be used to modify lists::

    {% do navigation.append('a string') %}


Loop Controls
~~~~~~~~~~~~~

If the application enables the :ref:`loopcontrols-extension`, it's possible to
use `break` and `continue` in loops.  When `break` is reached, the loop is
terminated;  if `continue` is reached, the processing is stopped and continues
with the next iteration.

Here's a loop that skips every second item::

    {% for user in users %}
        {%- if loop.index is even %}{% continue %}{% endif %}
        ...
    {% endfor %}

Likewise, a loop that stops processing after the 10th iteration::

    {% for user in users %}
        {%- if loop.index >= 10 %}{% break %}{% endif %}
    {%- endfor %}

Note that ``loop.index`` starts with 1, and ``loop.index0`` starts with 0
(See: :ref:`for-loop`).


With Statement
~~~~~~~~~~~~~~

.. versionadded:: 2.3

If the application enables the :ref:`with-extension`, it is possible to
use the `with` keyword in templates.  This makes it possible to create
a new inner scope.  Variables set within this scope are not visible
outside of the scope.

With in a nutshell::

    {% with %}
        {% set foo = 42 %}
        {{ foo }}           foo is 42 here
    {% endwith %}
    foo is not visible here any longer

Because it is common to set variables at the beginning of the scope,
you can do that within the `with` statement.  The following two examples
are equivalent::

    {% with foo = 42 %}
        {{ foo }}
    {% endwith %}

    {% with %}
        {% set foo = 42 %}
        {{ foo }}
    {% endwith %}
