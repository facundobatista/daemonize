daemonize
========================


.. image:: https://readthedocs.org/projects/daemonize/badge/?version=latest
    :target: http://daemonize.readthedocs.org/en/latest/?badge=latest
    :alt: Latest version

.. image:: https://secure.travis-ci.org/thesharp/daemonize.png
    :target: http://travis-ci.org/thesharp/daemonize
    :alt: Travis CI


**daemonize** is a library for writing system daemons in Python. It has
some bits from
`daemonize.sourceforge.net <http://daemonize.sourceforge.net>`__. It is
distributed under MIT license.

Dependencies
------------

It is tested under following Python versions:

-  2.6
-  2.7
-  3.3
-  3.4
-  3.5

Installation
------------

You can install it from Python Package Index (PyPI):

::

    $ pip install daemonize

Usage
-----

.. code-block:: python

    from time import sleep
    from daemonize import Daemonize

    pid = "/tmp/test.pid"


    def main():
        while True:
            sleep(5)

    daemon = Daemonize(app="test_app", pid=pid, action=main)
    daemon.start()


Parameters
----------

Daemonize expects three arguments (``app``, ``pid`` and ``action``), the
rest are optional.

- ``app``: contains the application name which will be sent to syslog.

- ``pid``: path to the pidfile.

- ``action``: your custom function which will be executed after daemonization.

- ``keep_fds``: optional list of fds which should not be closed. See the section
  below for more information about this parameter. Defaults to ``None``.

- ``auto_close_fds``: optional parameter to not close opened fds. Defaults
  to ``True``.

- ``privileged_action``: action that will be executed before drop privileges
  if user or group parameter is provided.  If you want to transfer anything
  from privileged_action to action, such as opened privileged file
  descriptor, you should return it from privileged_action function and catch
  it inside action function. Defaults to ``None``.

- ``user``: drop privileges to this user if provided. Defaults to ``None``.

- ``group``: drop privileges to this group if provided. Defaults to ``None``.

- ``verbose``: send debug messages to logger if provided. Defaults
  to ``False``.

- ``logger``: use this logger object instead of creating new one, if provided.
  Defaults to ``None``.

- ``foreground``: stay in foreground; do not fork (for debugging) Defaults to
  ``False``.

- ``chdir``: change working directory. Defaults to ``"/"``.


File descriptors
----------------

Daemonize object's constructor understands the optional argument
**keep\_fds** which contains a list of FDs which should not be closed.
For example:

.. code-block:: python

    import logging
    from daemonize import Daemonize

    pid = "/tmp/test.pid"
    logger = logging.getLogger(__name__)
    logger.setLevel(logging.DEBUG)
    logger.propagate = False
    fh = logging.FileHandler("/tmp/test.log", "w")
    fh.setLevel(logging.DEBUG)
    logger.addHandler(fh)
    keep_fds = [fh.stream.fileno()]


    def main():
        logger.debug("Test")

    daemon = Daemonize(app="test_app", pid=pid, action=main, keep_fds=keep_fds)
    daemon.start()
