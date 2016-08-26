.. highlight:: html+jinja

Whitespace Control
------------------

In the default configuration:

* a single trailing newline is stripped if present
* other whitespace (spaces, tabs, newlines etc.) is returned unchanged

If an application configures Jinja to `trim_blocks`, the first newline after a
template tag is removed automatically (like in PHP). The `lstrip_blocks`
option can also be set to strip tabs and spaces from the beginning of a
line to the start of a block. (Nothing will be stripped if there are
other characters before the start of the block.)

With both `trim_blocks` and `lstrip_blocks` enabled, you can put block tags
on their own lines, and the entire block line will be removed when
rendered, preserving the whitespace of the contents.  For example,
without the `trim_blocks` and `lstrip_blocks` options, this template::

    <div>
        {% if True %}
            yay
        {% endif %}
    </div>

gets rendered with blank lines inside the div::

    <div>
    
            yay
    
    </div>

But with both `trim_blocks` and `lstrip_blocks` enabled, the template block
lines are removed and other whitespace is preserved::
    
    <div>
            yay
    </div>

You can manually disable the `lstrip_blocks` behavior by putting a
plus sign (``+``) at the start of a block::

    <div>
            {%+ if something %}yay{% endif %}
    </div>

You can also strip whitespace in templates by hand.  If you add a minus
sign (``-``) to the start or end of a block (e.g. a :ref:`for-loop` tag), a
comment, or a variable expression, the whitespaces before or after
that block will be removed::

    {% for item in seq -%}
        {{ item }}
    {%- endfor %}

This will yield all elements without whitespace between them.  If `seq` was
a list of numbers from ``1`` to ``9``, the output would be ``123456789``.

If :ref:`line-statements` are enabled, they strip leading whitespace
automatically up to the beginning of the line.

By default, Jinja2 also removes trailing newlines.  To keep single
trailing newlines, configure Jinja to `keep_trailing_newline`.

.. admonition:: Note

    You must not add whitespace between the tag and the minus sign.

    **valid**::

        {%- if foo -%}...{% endif %}

    **invalid**::

        {% - if foo - %}...{% endif %}