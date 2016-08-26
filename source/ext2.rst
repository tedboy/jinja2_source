.. _i18n-extension:

i18n Extension
--------------

**Import name:** `jinja2.ext.i18n`

The i18n extension can be used in combination with `gettext`_ or `babel`_.  If 
the i18n extension is enabled Jinja2 provides a `trans` statement that marks 
the wrapped string as translatable and calls `gettext`.

After enabling, dummy `_` function that forwards calls to `gettext` is added
to the environment globals.  An internationalized application then has to
provide a `gettext` function and optionally an `ngettext` function into the
namespace, either globally or for each rendering.

Environment Methods
~~~~~~~~~~~~~~~~~~~

After enabling the extension, the environment provides the following
additional methods:

.. method:: jinja2.Environment.install_gettext_translations(translations, newstyle=False)

    Installs a translation globally for that environment.  The translations
    object provided must implement at least `ugettext` and `ungettext`.
    The `gettext.NullTranslations` and `gettext.GNUTranslations` classes
    as well as `Babel`_\s `Translations` class are supported.

    .. versionchanged:: 2.5 newstyle gettext added

.. method:: jinja2.Environment.install_null_translations(newstyle=False)

    Install dummy gettext functions.  This is useful if you want to prepare
    the application for internationalization but don't want to implement the
    full internationalization system yet.

    .. versionchanged:: 2.5 newstyle gettext added

.. method:: jinja2.Environment.install_gettext_callables(gettext, ngettext, newstyle=False)

    Installs the given `gettext` and `ngettext` callables into the
    environment as globals.  They are supposed to behave exactly like the
    standard library's :func:`gettext.ugettext` and
    :func:`gettext.ungettext` functions.

    If `newstyle` is activated, the callables are wrapped to work like
    newstyle callables.  See :ref:`newstyle-gettext` for more information.

    .. versionadded:: 2.5

.. method:: jinja2.Environment.uninstall_gettext_translations()

    Uninstall the translations again.

.. method:: jinja2.Environment.extract_translations(source)

    Extract localizable strings from the given template node or source.

    For every string found this function yields a ``(lineno, function,
    message)`` tuple, where:

    * `lineno` is the number of the line on which the string was found,
    * `function` is the name of the `gettext` function used (if the
      string was extracted from embedded Python code), and
    *  `message` is the string itself (a `unicode` object, or a tuple
       of `unicode` objects for functions with multiple string arguments).

    If `Babel`_ is installed, :ref:`the babel integration <babel-integration>`
    can be used to extract strings for babel.

For a web application that is available in multiple languages but gives all
the users the same language (for example a multilingual forum software
installed for a French community) may load the translations once and add the
translation methods to the environment at environment generation time::

    translations = get_gettext_translations()
    env = Environment(extensions=['jinja2.ext.i18n'])
    env.install_gettext_translations(translations)

The `get_gettext_translations` function would return the translator for the
current configuration.  (For example by using `gettext.find`)

The usage of the `i18n` extension for template designers is covered as part
:ref:`of the template documentation <i18n-in-templates>`.

.. _gettext: http://docs.python.org/dev/library/gettext
.. _Babel: http://babel.pocoo.org/

.. _newstyle-gettext:

Newstyle Gettext
~~~~~~~~~~~~~~~~

.. versionadded:: 2.5

Starting with version 2.5 you can use newstyle gettext calls.  These are
inspired by trac's internal gettext functions and are fully supported by
the babel extraction tool.  They might not work as expected by other
extraction tools in case you are not using Babel's.

What's the big difference between standard and newstyle gettext calls?  In
general they are less to type and less error prone.  Also if they are used
in an autoescaping environment they better support automatic escaping.
Here are some common differences between old and new calls:

standard gettext:

.. sourcecode:: html+jinja

    {{ gettext('Hello World!') }}
    {{ gettext('Hello %(name)s!')|format(name='World') }}
    {{ ngettext('%(num)d apple', '%(num)d apples', apples|count)|format(
        num=apples|count
    )}}

newstyle gettext looks like this instead:

.. sourcecode:: html+jinja

    {{ gettext('Hello World!') }}
    {{ gettext('Hello %(name)s!', name='World') }}
    {{ ngettext('%(num)d apple', '%(num)d apples', apples|count) }}

The advantages of newstyle gettext are that you have less to type and that
named placeholders become mandatory.  The latter sounds like a
disadvantage but solves a lot of troubles translators are often facing
when they are unable to switch the positions of two placeholder.  With
newstyle gettext, all format strings look the same.

Furthermore with newstyle gettext, string formatting is also used if no
placeholders are used which makes all strings behave exactly the same.
Last but not least are newstyle gettext calls able to properly mark
strings for autoescaping which solves lots of escaping related issues many
templates are experiencing over time when using autoescaping.