.. datastore documentation master file, created by
   sphinx-quickstart on Thu Dec 22 01:26:05 2011.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

========================================================
datastore - simple, unified API for multiple data stores
========================================================

Overview
========

datastore is a generic layer of abstraction for data store and database access.
It is a **simple** API with the aim to enable application development in a
datastore-agnostic way, allowing datastores to be swapped seamlessly without
changing application code. Thus, one can leverage different datastores with
different strengths without committing the application to one datastore
throughout its lifetime.

In addition, grouped datastores significantly simplify complex data access
patterns, such as caching and sharding.


Documentation
=============

The :ref:`api` contains documentation of the core library.

.. toctree::
   :maxdepth: 2

   api/index


Package Hierarchy:

.. toctree::
   :maxdepth: 4

   package/datastore

The :py:mod:`impl Package <datastore.impl>` contains reference to multiple
datastore implementations included with this datastore.


Install
=======

From pypi (using pip)::

    sudo pip install datastore

From pypi (using setuptools)::

    sudo easy_install datastore

From source::


    git clone https://github.com/jbenet/datastore/
    cd datastore
    sudo python setup.py install

License
-------

datastore is under the MIT Licence


Contact
-------

datastore is written by [Juan Batiz-Benet](https://github.com/jbenet). It
was originally part of
`py-dronestore<https://github.com/jbenet/py-dronestore>`_
On December 2011, it was re-written as a standalone project.

Project Homepage: https://github.com/jbenet/datastore

Feel free to contact me. But please file issues in github first. Cheers!



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`



Hello Worlds
============

To illustrate the api and how it works across different data storage systems,
here is Hello World in various datastores. Note the common code.

Hello Dict
----------

    >>> import datastore
    >>>
    >>> ds = datastore.basic.DictDatastore()
    >>>
    >>> hello = datastore.Key('hello')
    >>> ds.put(hello, 'world')
    >>> ds.contains(hello)
    True
    >>> ds.get(hello)
    'world'
    >>> ds.delete(hello)
    >>> ds.get(hello)
    None

Hello memcache
--------------

    >>> import pylibmc
    >>> import datastore
    >>> from datastore.impl.memcached import MemcachedDatastore
    >>>
    >>> mc = pylibmc.Client(['127.0.0.1'])
    >>> ds = MemcachedDatastore(mc)
    >>>
    >>> hello = datastore.Key('hello')
    >>> ds.put(hello, 'world')
    >>> ds.contains(hello)
    True
    >>> ds.get(hello)
    'world'
    >>> ds.delete(hello)
    >>> ds.get(hello)
    None

Hello mongo
-----------

    >>> import pymongo
    >>> import datastore
    >>> from datastore.impl.mongo import MongoDatastore
    >>>
    >>> conn = pymongo.Connection()
    >>> ds = MongoDatastore(conn.test_db)
    >>>
    >>> hello = datastore.Key('hello')
    >>> ds.put(hello, 'world')
    >>> ds.contains(hello)
    True
    >>> ds.get(hello)
    'world'
    >>> ds.delete(hello)
    >>> ds.get(hello)
    None

Hello redis
-----------

    >>> import redis
    >>> import datastore
    >>> from datastore.impl.redis import RedisDatastore
    >>> r = redis.Redis()
    >>> ds = RedisDatastore(r)
    >>>
    >>> hello = datastore.Key('hello')
    >>> ds.put(hello, 'world')
    >>> ds.contains(hello)
    True
    >>> ds.get(hello)
    'world'
    >>> ds.delete(hello)
    >>> ds.get(hello)
    None

Hello filesystem
----------------

    >>> import datastore
    >>> from datastore.impl.filesystem import FileSystemDatastore
    >>>
    >>> ds = FileSystemDatastore('/tmp/.test_datastore')
    >>>
    >>> hello = datastore.Key('hello')
    >>> ds.put(hello, 'world')
    >>> ds.contains(hello)
    True
    >>> ds.get(hello)
    'world'
    >>> ds.delete(hello)
    >>> ds.get(hello)
    None

Hello git
---------

    >>> import datastore
    >>> from datastore.impl.git import GitDatastore
    >>>
    >>> ds = GitDatastore('/tmp/.test_datastore')
    >>>
    >>> hello = datastore.Key('hello')
    >>> ds.put(hello, 'world')
    >>> ds.contains(hello)
    True
    >>> ds.get(hello)
    'world'
    >>> ds.delete(hello)
    >>> ds.get(hello)
    None


Hello Serialization
-------------------

    >>> import datastore
    >>> import json
    >>>
    >>> ds_child = datastore.DictDatastore()
    >>> ds = datastore.serialize.shim(ds_child, json)
    >>>
    >>> hello = datastore.Key('hello')
    >>> ds.put(hello, 'world')
    >>> ds.contains(hello)
    True
    >>> ds.get(hello)
    'world'
    >>> ds.delete(hello)
    >>> ds.get(hello)
    None



Hello Tiered Access
-------------------

    >>> import pymongo
    >>> import datastore
    >>>
    >>> from datastore.impl.mongo import MongoDatastore
    >>> from datastore.impl.lrucache import LRUCache
    >>> from datastore.impl.filesystem import FileSystemDatastore
    >>>
    >>> conn = pymongo.Connection()
    >>> mongo = MongoDatastore(conn.test_db)
    >>>
    >>> cache = LRUCache(1000)
    >>> fs = FileSystemDatastore('/tmp/.test_db')
    >>>
    >>> ds = datastore.TieredDatastore([cache, mongo, fs])
    >>>
    >>> hello = datastore.Key('hello')
    >>> ds.put(hello, 'world')
    >>> ds.contains(hello)
    True
    >>> ds.get(hello)
    'world'
    >>> ds.delete(hello)
    >>> ds.get(hello)
    None


Hello Sharding
--------------

    >>> import datastore
    >>>
    >>> shards = [datastore.DictDatastore() for i in range(0, 10)]
    >>>
    >>> ds = datastore.ShardedDatastore(shards)
    >>>
    >>> hello = datastore.Key('hello')
    >>> ds.put(hello, 'world')
    >>> ds.contains(hello)
    True
    >>> ds.get(hello)
    'world'
    >>> ds.delete(hello)
    >>> ds.get(hello)
    None
