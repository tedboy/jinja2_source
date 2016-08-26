.. highlight:: html+jinja

HTML Escaping
-------------

When generating HTML from templates, there's always a risk that a variable will
include characters that affect the resulting HTML. There are two approaches:

a. manually escaping each variable; or
b. automatically escaping everything by default.

Jinja supports both. What is used depends on the application configuration.
The default configuration is no automatic escaping; for various reasons:

-   Escaping everything except for safe values will also mean that Jinja is
    escaping variables known to not include HTML (e.g. numbers, booleans)
    which can be a huge performance hit.

-   The information about the safety of a variable is very fragile.  It could
    happen that by coercing safe and unsafe values, the return value is
    double-escaped HTML.

Working with Manual Escaping
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If manual escaping is enabled, it's **your** responsibility to escape
variables if needed.  What to escape?  If you have a variable that *may*
include any of the following chars (``>``, ``<``, ``&``, or ``"``) you
**SHOULD** escape it unless the variable contains well-formed and trusted
HTML.  Escaping works by piping the variable through the ``|e`` filter::

    {{ user.username|e }}

Working with Automatic Escaping
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When automatic escaping is enabled, everything is escaped by default except
for values explicitly marked as safe.  Variables and expressions 
can be marked as safe either in:

a. the context dictionary by the application with `MarkupSafe.Markup`, or
b. the template, with the `|safe` filter
   
The main problem with this approach is that Python itself doesn't have the
concept of tainted values; so whether a value is safe or unsafe can get lost.

If a value is not marked safe, auto-escaping will take place; which means that
you could end up with double-escaped contents.  Double-escaping is easy to
avoid, however: just rely on the tools Jinja2 provides and *don't use builtin
Python constructs such as str.format or the string modulo operator (%)*.

Jinja2 functions (macros, `super`, `self.BLOCKNAME`) always return template
data that is marked as safe.

String literals in templates with automatic escaping are considered unsafe
because native Python strings (``str``, ``unicode``, ``basestring``) are not
`MarkupSafe.Markup` strings with an ``__html__`` attribute.
