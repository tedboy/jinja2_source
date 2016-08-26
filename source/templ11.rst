.. highlight:: html+jinja

.. _list-of-control-structures:

List of Control Structures
--------------------------

A control structure refers to all those things that control the flow of a
program - conditionals (i.e. if/elif/else), for-loops, as well as things like
macros and blocks.  With the default syntax, control structures appear inside
``{% ... %}`` blocks.

.. _for-loop:

For
~~~

Loop over each item in a sequence.  For example, to display a list of users
provided in a variable called `users`::

    <h1>Members</h1>
    <ul>
    {% for user in users %}
      <li>{{ user.username|e }}</li>
    {% endfor %}
    </ul>

As variables in templates retain their object properties, it is possible to
iterate over containers like `dict`::

    <dl>
    {% for key, value in my_dict.iteritems() %}
        <dt>{{ key|e }}</dt>
        <dd>{{ value|e }}</dd>
    {% endfor %}
    </dl>

Note, however, that **Python dicts are not ordered**; so you might want to
either pass a sorted ``list`` of ``tuple`` s -- or a
``collections.OrderedDict`` -- to the template, or use the `dictsort` filter.

Inside of a for-loop block, you can access some special variables:

+-----------------------+---------------------------------------------------+
| Variable              | Description                                       |
+=======================+===================================================+
| `loop.index`          | The current iteration of the loop. (1 indexed)    |
+-----------------------+---------------------------------------------------+
| `loop.index0`         | The current iteration of the loop. (0 indexed)    |
+-----------------------+---------------------------------------------------+
| `loop.revindex`       | The number of iterations from the end of the loop |
|                       | (1 indexed)                                       |
+-----------------------+---------------------------------------------------+
| `loop.revindex0`      | The number of iterations from the end of the loop |
|                       | (0 indexed)                                       |
+-----------------------+---------------------------------------------------+
| `loop.first`          | True if first iteration.                          |
+-----------------------+---------------------------------------------------+
| `loop.last`           | True if last iteration.                           |
+-----------------------+---------------------------------------------------+
| `loop.length`         | The number of items in the sequence.              |
+-----------------------+---------------------------------------------------+
| `loop.cycle`          | A helper function to cycle between a list of      |
|                       | sequences.  See the explanation below.            |
+-----------------------+---------------------------------------------------+
| `loop.depth`          | Indicates how deep in a recursive loop            |
|                       | the rendering currently is.  Starts at level 1    |
+-----------------------+---------------------------------------------------+
| `loop.depth0`         | Indicates how deep in a recursive loop            |
|                       | the rendering currently is.  Starts at level 0    |
+-----------------------+---------------------------------------------------+

Within a for-loop, it's possible to cycle among a list of strings/variables
each time through the loop by using the special `loop.cycle` helper::

    {% for row in rows %}
        <li class="{{ loop.cycle('odd', 'even') }}">{{ row }}</li>
    {% endfor %}

Since Jinja 2.1, an extra `cycle` helper exists that allows loop-unbound
cycling.  For more information, have a look at the :ref:`builtin-globals`.

.. _loop-filtering:

Unlike in Python, it's not possible to `break` or `continue` in a loop.  You
can, however, filter the sequence during iteration, which allows you to skip
items.  The following example skips all the users which are hidden::

    {% for user in users if not user.hidden %}
        <li>{{ user.username|e }}</li>
    {% endfor %}

The advantage is that the special `loop` variable will count correctly; thus
not counting the users not iterated over.

If no iteration took place because the sequence was empty or the filtering
removed all the items from the sequence, you can render a default block
by using `else`::

    <ul>
    {% for user in users %}
        <li>{{ user.username|e }}</li>
    {% else %}
        <li><em>no users found</em></li>
    {% endfor %}
    </ul>

Note that, in Python, `else` blocks are executed whenever the corresponding
loop **did not** `break`.  Since Jinja loops cannot `break` anyway,
a slightly different behavior of the `else` keyword was chosen.

It is also possible to use loops recursively.  This is useful if you are
dealing with recursive data such as sitemaps or RDFa.
To use loops recursively, you basically have to add the `recursive` modifier
to the loop definition and call the `loop` variable with the new iterable
where you want to recurse.

The following example implements a sitemap with recursive loops::

    <ul class="sitemap">
    {%- for item in sitemap recursive %}
        <li><a href="{{ item.href|e }}">{{ item.title }}</a>
        {%- if item.children -%}
            <ul class="submenu">{{ loop(item.children) }}</ul>
        {%- endif %}</li>
    {%- endfor %}
    </ul>

The `loop` variable always refers to the closest (innermost) loop. If we
have more than one level of loops, we can rebind the variable `loop` by
writing `{% set outer_loop = loop %}` after the loop that we want to
use recursively. Then, we can call it using `{{ outer_loop(...) }}`

.. _if:

If
~~

The `if` statement in Jinja is comparable with the Python if statement.
In the simplest form, you can use it to test if a variable is defined, not
empty or not false::

    {% if users %}
    <ul>
    {% for user in users %}
        <li>{{ user.username|e }}</li>
    {% endfor %}
    </ul>
    {% endif %}

For multiple branches, `elif` and `else` can be used like in Python.  You can
use more complex :ref:`expressions` there, too::

    {% if kenny.sick %}
        Kenny is sick.
    {% elif kenny.dead %}
        You killed Kenny!  You bastard!!!
    {% else %}
        Kenny looks okay --- so far
    {% endif %}

If can also be used as an :ref:`inline expression <if-expression>` and for
:ref:`loop filtering <loop-filtering>`.

.. _macros:

Macros
~~~~~~

Macros are comparable with functions in regular programming languages.  They
are useful to put often used idioms into reusable functions to not repeat
yourself ("DRY").

Here's a small example of a macro that renders a form element::

    {% macro input(name, value='', type='text', size=20) -%}
        <input type="{{ type }}" name="{{ name }}" value="{{
            value|e }}" size="{{ size }}">
    {%- endmacro %}

The macro can then be called like a function in the namespace::

    <p>{{ input('username') }}</p>
    <p>{{ input('password', type='password') }}</p>

If the macro was defined in a different template, you have to
:ref:`import <import>` it first.

Inside macros, you have access to three special variables:

`varargs`
    If more positional arguments are passed to the macro than accepted by the
    macro, they end up in the special `varargs` variable as a list of values.

`kwargs`
    Like `varargs` but for keyword arguments.  All unconsumed keyword
    arguments are stored in this special variable.

`caller`
    If the macro was called from a :ref:`call<call>` tag, the caller is stored
    in this variable as a callable macro.

Macros also expose some of their internal details.  The following attributes
are available on a macro object:

`name`
    The name of the macro.  ``{{ input.name }}`` will print ``input``.

`arguments`
    A tuple of the names of arguments the macro accepts.

`defaults`
    A tuple of default values.

`catch_kwargs`
    This is `true` if the macro accepts extra keyword arguments (i.e.: accesses
    the special `kwargs` variable).

`catch_varargs`
    This is `true` if the macro accepts extra positional arguments (i.e.:
    accesses the special `varargs` variable).

`caller`
    This is `true` if the macro accesses the special `caller` variable and may
    be called from a :ref:`call<call>` tag.

If a macro name starts with an underscore, it's not exported and can't
be imported.


.. _call:

Call
~~~~

In some cases it can be useful to pass a macro to another macro.  For this
purpose, you can use the special `call` block.  The following example shows
a macro that takes advantage of the call functionality and how it can be
used::

    {% macro render_dialog(title, class='dialog') -%}
        <div class="{{ class }}">
            <h2>{{ title }}</h2>
            <div class="contents">
                {{ caller() }}
            </div>
        </div>
    {%- endmacro %}

    {% call render_dialog('Hello World') %}
        This is a simple dialog rendered by using a macro and
        a call block.
    {% endcall %}

It's also possible to pass arguments back to the call block.  This makes it
useful as a replacement for loops.  Generally speaking, a call block works
exactly like a macro without a name.

Here's an example of how a call block can be used with arguments::

    {% macro dump_users(users) -%}
        <ul>
        {%- for user in users %}
            <li><p>{{ user.username|e }}</p>{{ caller(user) }}</li>
        {%- endfor %}
        </ul>
    {%- endmacro %}

    {% call(user) dump_users(list_of_user) %}
        <dl>
            <dl>Realname</dl>
            <dd>{{ user.realname|e }}</dd>
            <dl>Description</dl>
            <dd>{{ user.description }}</dd>
        </dl>
    {% endcall %}


Filters
~~~~~~~

Filter sections allow you to apply regular Jinja2 filters on a block of
template data.  Just wrap the code in the special `filter` section::

    {% filter upper %}
        This text becomes uppercase
    {% endfilter %}


.. _assignments:

Assignments
~~~~~~~~~~~

Inside code blocks, you can also assign values to variables.  Assignments at
top level (outside of blocks, macros or loops) are exported from the template
like top level macros and can be imported by other templates.

Assignments use the `set` tag and can have multiple targets::

    {% set navigation = [('index.html', 'Index'), ('about.html', 'About')] %}
    {% set key, value = call_something() %}


Block Assignments
~~~~~~~~~~~~~~~~~

.. versionadded:: 2.8

Starting with Jinja 2.8, it's possible to also use block assignments to
capture the contents of a block into a variable name.  This can be useful
in some situations as an alternative for macros.  In that case, instead of
using an equals sign and a value, you just write the variable name and then
everything until ``{% endset %}`` is captured.

Example::

    {% set navigation %}
        <li><a href="/">Index</a>
        <li><a href="/downloads">Downloads</a>
    {% endset %}

The `navigation` variable then contains the navigation HTML source.


.. _extends:

Extends
~~~~~~~

The `extends` tag can be used to extend one template from another.  You can
have multiple `extends` tags in a file, but only one of them may be executed at
a time.

See the section about :ref:`template-inheritance` above.


.. _blocks:

Blocks
~~~~~~

Blocks are used for inheritance and act as both placeholders and replacements
at the same time.  They are documented in detail in the
:ref:`template-inheritance` section.


Include
~~~~~~~

The `include` statement is useful to include a template and return the
rendered contents of that file into the current namespace::

    {% include 'header.html' %}
        Body
    {% include 'footer.html' %}

Included templates have access to the variables of the active context by
default.  For more details about context behavior of imports and includes,
see :ref:`import-visibility`.

From Jinja 2.2 onwards, you can mark an include with ``ignore missing``; in
which case Jinja will ignore the statement if the template to be included
does not exist.  When combined with ``with`` or ``without context``, it must
be placed *before* the context visibility statement.  Here are some valid
examples::

    {% include "sidebar.html" ignore missing %}
    {% include "sidebar.html" ignore missing with context %}
    {% include "sidebar.html" ignore missing without context %}

.. versionadded:: 2.2

You can also provide a list of templates that are checked for existence
before inclusion.  The first template that exists will be included.  If
`ignore missing` is given, it will fall back to rendering nothing if
none of the templates exist, otherwise it will raise an exception.

Example::

    {% include ['page_detailed.html', 'page.html'] %}
    {% include ['special_sidebar.html', 'sidebar.html'] ignore missing %}

.. versionchanged:: 2.4
   If a template object was passed to the template context, you can
   include that object using `include`.

.. _import:

Import
~~~~~~

Jinja2 supports putting often used code into macros.  These macros can go into
different templates and get imported from there.  This works similarly to the
import statements in Python.  It's important to know that imports are cached
and imported templates don't have access to the current template variables,
just the globals by default.  For more details about context behavior of
imports and includes, see :ref:`import-visibility`.

There are two ways to import templates.  You can import a complete template
into a variable or request specific macros / exported variables from it.

Imagine we have a helper module that renders forms (called `forms.html`)::

    {% macro input(name, value='', type='text') -%}
        <input type="{{ type }}" value="{{ value|e }}" name="{{ name }}">
    {%- endmacro %}

    {%- macro textarea(name, value='', rows=10, cols=40) -%}
        <textarea name="{{ name }}" rows="{{ rows }}" cols="{{ cols
            }}">{{ value|e }}</textarea>
    {%- endmacro %}

The easiest and most flexible way to access a template's variables
and macros is to import the whole template module into a variable.
That way, you can access the attributes::

    {% import 'forms.html' as forms %}
    <dl>
        <dt>Username</dt>
        <dd>{{ forms.input('username') }}</dd>
        <dt>Password</dt>
        <dd>{{ forms.input('password', type='password') }}</dd>
    </dl>
    <p>{{ forms.textarea('comment') }}</p>


Alternatively, you can import specific names from a template into the current
namespace::

    {% from 'forms.html' import input as input_field, textarea %}
    <dl>
        <dt>Username</dt>
        <dd>{{ input_field('username') }}</dd>
        <dt>Password</dt>
        <dd>{{ input_field('password', type='password') }}</dd>
    </dl>
    <p>{{ textarea('comment') }}</p>

Macros and variables starting with one or more underscores are private and
cannot be imported.

.. versionchanged:: 2.4
   If a template object was passed to the template context, you can
   import from that object.