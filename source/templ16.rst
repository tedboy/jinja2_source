.. highlight:: html+jinja

.. _builtin-globals:

List of Global Functions
------------------------

The following functions are available in the global scope by default:

.. function:: range([start,] stop[, step])

    Return a list containing an arithmetic progression of integers.
    ``range(i, j)`` returns ``[i, i+1, i+2, ..., j-1]``;
    start (!) defaults to ``0``.
    When step is given, it specifies the increment (or decrement).
    For example, ``range(4)`` and ``range(0, 4, 1)`` return ``[0, 1, 2, 3]``.
    The end point is omitted!
    These are exactly the valid indices for a list of 4 elements.

    This is useful to repeat a template block multiple times, e.g.
    to fill a list.  Imagine you have 7 users in the list but you want to
    render three empty items to enforce a height with CSS::

        <ul>
        {% for user in users %}
            <li>{{ user.username }}</li>
        {% endfor %}
        {% for number in range(10 - users|count) %}
            <li class="empty"><span>...</span></li>
        {% endfor %}
        </ul>

.. function:: lipsum(n=5, html=True, min=20, max=100)

    Generates some lorem ipsum for the template.  By default, five paragraphs
    of HTML are generated with each paragraph between 20 and 100 words.
    If html is False, regular text is returned.  This is useful to generate simple
    contents for layout testing.

.. function:: dict(\**items)

    A convenient alternative to dict literals.  ``{'foo': 'bar'}`` is the same
    as ``dict(foo='bar')``.

.. class:: cycler(\*items)

    The cycler allows you to cycle among values similar to how `loop.cycle`
    works.  Unlike `loop.cycle`, you can use this cycler outside of
    loops or over multiple loops.

    This can be very useful if you want to show a list of folders and
    files with the folders on top but both in the same list with alternating
    row colors.

    The following example shows how `cycler` can be used::

        {% set row_class = cycler('odd', 'even') %}
        <ul class="browser">
        {% for folder in folders %}
          <li class="folder {{ row_class.next() }}">{{ folder|e }}</li>
        {% endfor %}
        {% for filename in files %}
          <li class="file {{ row_class.next() }}">{{ filename|e }}</li>
        {% endfor %}
        </ul>

    A cycler has the following attributes and methods:

    .. method:: reset()

        Resets the cycle to the first item.

    .. method:: next()

        Goes one item ahead and returns the then-current item.

    .. attribute:: current

        Returns the current item.

    **new in Jinja 2.1**

.. class:: joiner(sep=', ')

    A tiny helper that can be used to "join" multiple sections.  A joiner is
    passed a string and will return that string every time it's called, except
    the first time (in which case it returns an empty string).  You can
    use this to join things::

        {% set pipe = joiner("|") %}
        {% if categories %} {{ pipe() }}
            Categories: {{ categories|join(", ") }}
        {% endif %}
        {% if author %} {{ pipe() }}
            Author: {{ author() }}
        {% endif %}
        {% if can_edit %} {{ pipe() }}
            <a href="?action=edit">Edit</a>
        {% endif %}

    **new in Jinja 2.1**