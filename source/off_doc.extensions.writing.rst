.. _writing-extensions:

Writing Extensions
------------------

.. contents:: `Contents`
   :depth: 2
   :local:


.. module:: jinja2.ext

By writing extensions you can add custom tags to Jinja2.  This is a non-trivial
task and usually not needed as the default tags and expressions cover all
common use cases.  The i18n extension is a good example of why extensions are
useful. Another one would be fragment caching.

When writing extensions you have to keep in mind that you are working with the
Jinja2 template compiler which does not validate the node tree you are passing
to it.  If the AST is malformed you will get all kinds of compiler or runtime
errors that are horrible to debug.  Always make sure you are using the nodes
you create correctly.  The API documentation below shows which nodes exist and
how to use them.

Example Extension
~~~~~~~~~~~~~~~~~

The following example implements a `cache` tag for Jinja2 by using the
`Werkzeug`_ caching contrib module:

.. literalinclude:: cache_extension.py
    :language: python

And here is how you use it in an environment::

    from jinja2 import Environment
    from werkzeug.contrib.cache import SimpleCache

    env = Environment(extensions=[FragmentCacheExtension])
    env.fragment_cache = SimpleCache()

Inside the template it's then possible to mark blocks as cacheable.  The
following example caches a sidebar for 300 seconds:

.. sourcecode:: html+jinja

    {% cache 'sidebar', 300 %}
    <div class="sidebar">
        ...
    </div>
    {% endcache %}

.. _Werkzeug: http://werkzeug.pocoo.org/

Extension API
~~~~~~~~~~~~~

Extensions always have to extend the :class:`jinja2.ext.Extension` class:

:class:`jinja2.ext.Extension`
"""""""""""""""""""""""""""""

Parser API
~~~~~~~~~~

The parser passed to :meth:`Extension.parse` provides ways to parse
expressions of different types.  The following methods may be used by
extensions:

:class:`jinja2.parser.Parser`
"""""""""""""""""""""""""""""


jinja2.lexer.TokenStream
""""""""""""""""""""""""
.. autoclass:: jinja2.lexer.TokenStream
   :members: push, look, eos, skip, next, next_if, skip_if, expect

   .. attribute:: current

        The current :class:`~jinja2.lexer.Token`.

jinja2.lexer.Token
""""""""""""""""""
.. autoclass:: jinja2.lexer.Token

    :members: test, test_any

    .. attribute:: lineno

        The line number of the token

    .. attribute:: type

        The type of the token.  This string is interned so you may compare
        it with arbitrary strings using the `is` operator.

    .. attribute:: value

        The value of the token.

There is also a utility function in the lexer module that can count newline
characters in strings:

jinja2.lexer.count_newlines
"""""""""""""""""""""""""""
.. autofunction:: jinja2.lexer.count_newlines

AST
~~~

The AST (Abstract Syntax Tree) is used to represent a template after parsing.
It's build of nodes that the compiler then converts into executable Python
code objects.  Extensions that provide custom statements can return nodes to
execute custom Python code.

The list below describes all nodes that are currently available.  The AST may
change between Jinja2 versions but will stay backwards compatible.

For more information have a look at the repr of :meth:`jinja2.Environment.parse`.

See `jinja2.nodes module <./generated/jinja2.nodes.html>`__

.. module:: jinja2.nodes



.. autoexception:: Impossible
