.. wsgithumb documentation master file, created by
   sphinx-quickstart on Thu Dec 29 15:13:50 2011.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to wsgithumb's documentation!
=====================================

Installation
-------------

Use easy_install or pip::

    $ pip install wsgithumb

Generating thumbnails
----------------------

You want to serve images located in ``document_root``. Use the
:func:`~wsgithumb.get_image_response` helper::

    >>> from wsgithumb import get_image_response
    >>> from tests import document_root
    >>> resp = get_image_response(document_root=document_root,
    ...                           cache_directory='/tmp/cache',
    ...                           size=(500, 500), path='tests/image.jpg',
    ...                           accel_header=None)
    >>> print resp
    200 OK
    Content-Type: image/jpeg
    Last-Modified: ... GMT
    ETag: "..."
    Content-Length: 23590
    ...

This will return a ``webob.Response`` containing a resized version of
``document_root/tests/image.jpg``. If size is None then the file is returned without resizing.

Serving files
-------------

While it's not it's primary function, wsgithumb allow to efficiently serve a
file with the :func:`~wsgithumb.get_file_response` helper::

    >>> from wsgithumb import get_file_response
    >>> from tests import filename
    >>> resp = get_file_response(filename, document_root=document_root,
    ...                          accel_header=None)
    >>> print resp
    200 OK
    Content-Type: text/x-python; charset=UTF-8
    Last-Modified: ... GMT
    ETag: "..."
    Content-Length: 114
    ...

Accel headers
--------------

Moderns web servers like NGinx or Apache implement the ``X-Sendfile`` feature.
This allow your application to only return the file path in a response header
the the web server will use this path to serve the file without blocking your
application.

wsgithumb allow you to use this feature. Just use the accel_header parameter.

For NGinx, set ``accel_header="x-accel-redirect:/path/bound/by/nginx"``

For Apache, set ``accel_header="x-sendfile"``

WSGI Applications
=================

wsgithumb provide 2 entry points for PasteDeploy:

.. sourcecode:: ini

    [app:thumbs]
    use = wsgithumb#thumbs

    [app:files]
    use = wsgithumb#files

See :func:`~wsgithumb.make_thumb_app` and :func:`~wsgithumb.make_file_app` for
available options.

Using wsgithumb in Pyramid
--------------------------

There is an helper in wsgithumb to help you to serve thumnails in your application.

Add this to your ``.ini`` file:

.. sourcecode:: ini

    [app:main]
    use = egg:MyApp

    # wsgithumb config
    thumbs.document_root = %(here)s/www/images
    thumbs.cache_directory = %(here)s/www/cache
    # if you want to use the accel_header when in production
    #thumbs.accel_header = x-accel-redirect:/thumbs/cache

Add a :func:`~wsgithumb.add_thumb_view` to your ``__init__.py``::

    sizes = {
        'thumb': (100, 100),
        'large': (500, 500),
        'original': None,
        }
    config.add_thumb_view('thumbs', sizes=sizes)

If you do not provide some sizes then those are used::

    DEFAULT_SIZES = {
        'icon': (16, 16),
        'small': (50, 50),
        'thumb': (100, 100),
        'medium': (300, 300),
        'large': (500, 500),
        'xlarge': (800, 800),
        'original': None,
        }

You can now retrieve your images with::

    >>> print request.route_url('thumbs', size='small',
    ...                         path=['tests', 'image.png']
    ...                        ) #doctest: +SKIP
    'http://localhost/thumbs/small/tests/image.png'


API
===

.. toctree::
   :maxdepth: 2

   api


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

