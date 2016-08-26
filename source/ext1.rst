Adding Extensions
-----------------

Extensions are added to the Jinja2 environment at creation time.  Once the
environment is created additional extensions cannot be added.  To add an
extension pass a list of extension classes or import paths to the
`extensions` parameter of the :class:`Environment` constructor.  The following
example creates a Jinja2 environment with the i18n extension loaded::

    jinja_env = Environment(extensions=['jinja2.ext.i18n'])