.. highlight:: html+jinja

Escaping
--------

It is sometimes desirable -- even necessary -- to have Jinja ignore parts
it would otherwise handle as variables or blocks.  For example, if, with
the default syntax, you want to use ``{{`` as a raw string in a template and
not start a variable, you have to use a trick.

The easiest way to output a literal variable delimiter (``{{``) is by using a
variable expression::

    {{ '{{' }}

For bigger sections, it makes sense to mark a block `raw`.  For example, to
include example Jinja syntax in a template, you can use this snippet::

    {% raw %}
        <ul>
        {% for item in seq %}
            <li>{{ item }}</li>
        {% endfor %}
        </ul>
    {% endraw %}