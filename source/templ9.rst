.. highlight:: html+jinja

.. _template-inheritance:

Template Inheritance
--------------------

The most powerful part of Jinja is template inheritance. Template inheritance
allows you to build a base "skeleton" template that contains all the common
elements of your site and defines **blocks** that child templates can override.

Sounds complicated but is very basic. It's easiest to understand it by starting
with an example.


Base Template
~~~~~~~~~~~~~

This template, which we'll call ``base.html``, defines a simple HTML skeleton
document that you might use for a simple two-column page. It's the job of
"child" templates to fill the empty blocks with content::

    <!DOCTYPE html>
    <html lang="en">
    <head>
        {% block head %}
        <link rel="stylesheet" href="style.css" />
        <title>{% block title %}{% endblock %} - My Webpage</title>
        {% endblock %}
    </head>
    <body>
        <div id="content">{% block content %}{% endblock %}</div>
        <div id="footer">
            {% block footer %}
            &copy; Copyright 2008 by <a href="http://domain.invalid/">you</a>.
            {% endblock %}
        </div>
    </body>
    </html>

In this example, the ``{% block %}`` tags define four blocks that child templates
can fill in. All the `block` tag does is tell the template engine that a
child template may override those placeholders in the template.

Child Template
~~~~~~~~~~~~~~

A child template might look like this::

    {% extends "base.html" %}
    {% block title %}Index{% endblock %}
    {% block head %}
        {{ super() }}
        <style type="text/css">
            .important { color: #336699; }
        </style>
    {% endblock %}
    {% block content %}
        <h1>Index</h1>
        <p class="important">
          Welcome to my awesome homepage.
        </p>
    {% endblock %}

The ``{% extends %}`` tag is the key here. It tells the template engine that
this template "extends" another template.  When the template system evaluates
this template, it first locates the parent.  The extends tag should be the
first tag in the template.  Everything before it is printed out normally and
may cause confusion.  For details about this behavior and how to take
advantage of it, see :ref:`null-master-fallback`.

The filename of the template depends on the template loader.  For example, the
:class:`FileSystemLoader` allows you to access other templates by giving the
filename.  You can access templates in subdirectories with a slash::

    {% extends "layout/default.html" %}

But this behavior can depend on the application embedding Jinja.  Note that
since the child template doesn't define the ``footer`` block, the value from
the parent template is used instead.

You can't define multiple ``{% block %}`` tags with the same name in the
same template.  This limitation exists because a block tag works in "both"
directions.  That is, a block tag doesn't just provide a placeholder to fill
- it also defines the content that fills the placeholder in the *parent*.
If there were two similarly-named ``{% block %}`` tags in a template,
that template's parent wouldn't know which one of the blocks' content to use.

If you want to print a block multiple times, you can, however, use the special
`self` variable and call the block with that name::

    <title>{% block title %}{% endblock %}</title>
    <h1>{{ self.title() }}</h1>
    {% block body %}{% endblock %}


Super Blocks
~~~~~~~~~~~~

It's possible to render the contents of the parent block by calling `super`.
This gives back the results of the parent block::

    {% block sidebar %}
        <h3>Table Of Contents</h3>
        ...
        {{ super() }}
    {% endblock %}


Named Block End-Tags
~~~~~~~~~~~~~~~~~~~~

Jinja2 allows you to put the name of the block after the end tag for better
readability::

    {% block sidebar %}
        {% block inner_sidebar %}
            ...
        {% endblock inner_sidebar %}
    {% endblock sidebar %}

However, the name after the `endblock` word must match the block name.


Block Nesting and Scope
~~~~~~~~~~~~~~~~~~~~~~~

Blocks can be nested for more complex layouts.  However, per default blocks
may not access variables from outer scopes::

    {% for item in seq %}
        <li>{% block loop_item %}{{ item }}{% endblock %}</li>
    {% endfor %}

This example would output empty ``<li>`` items because `item` is unavailable
inside the block.  The reason for this is that if the block is replaced by
a child template, a variable would appear that was not defined in the block or
passed to the context.

Starting with Jinja 2.2, you can explicitly specify that variables are
available in a block by setting the block to "scoped" by adding the `scoped`
modifier to a block declaration::

    {% for item in seq %}
        <li>{% block loop_item scoped %}{{ item }}{% endblock %}</li>
    {% endfor %}

When overriding a block, the `scoped` modifier does not have to be provided.


Template Objects
~~~~~~~~~~~~~~~~

.. versionchanged:: 2.4

If a template object was passed in the template context, you can
extend from that object as well.  Assuming the calling code passes
a layout template as `layout_template` to the environment, this
code works::

    {% extends layout_template %}

Previously, the `layout_template` variable had to be a string with
the layout template's filename for this to work.