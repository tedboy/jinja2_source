.. highlight:: html+jinja

.. _line-statements:

Line Statements
---------------

If line statements are enabled by the application, it's possible to mark a
line as a statement.  For example, if the line statement prefix is configured
to ``#``, the following two examples are equivalent::

    <ul>
    # for item in seq
        <li>{{ item }}</li>
    # endfor
    </ul>

    <ul>
    {% for item in seq %}
        <li>{{ item }}</li>
    {% endfor %}
    </ul>

The line statement prefix can appear anywhere on the line as long as no text
precedes it.  For better readability, statements that start a block (such as
`for`, `if`, `elif` etc.) may end with a colon::

    # for item in seq:
        ...
    # endfor


.. admonition:: Note

    Line statements can span multiple lines if there are open parentheses,
    braces or brackets::

        <ul>
        # for href, caption in [('index.html', 'Index'),
                                ('about.html', 'About')]:
            <li><a href="{{ href }}">{{ caption }}</a></li>
        # endfor
        </ul>

Since Jinja 2.2, line-based comments are available as well.  For example, if
the line-comment prefix is configured to be ``##``, everything from ``##`` to
the end of the line is ignored (excluding the newline sign)::

    # for item in seq:
        <li>{{ item }}</li>     ## this comment is ignored
    # endfor