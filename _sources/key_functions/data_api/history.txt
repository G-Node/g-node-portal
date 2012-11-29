==============================
Versioning and history support
==============================

You may know that our system supports :ref:`object versioning <track_changes>`. That means, for every object we keep all changes made. This document describes how you can access previous versions.

^^^^^^^^^^^^^^^^^^
Going back in time
^^^^^^^^^^^^^^^^^^

If any change was made to an object, you may request any of it's previous states by specifying the time of the state in the request. Use the **at_time** parameter for that:

::

    Request: GET /<namespace>/<object_type>/[<object_id>]/?at_time=<YYYY-MM-DD HH:MM:SS>


this works with lists of objects too:

::

    Request: GET /<namespace>/<object_type>/?at_time=<YYYY-MM-DD HH:MM:SS>


For example, if you set **at_time** to any point in time in the previous century:

::

    Request: GET /<namespace>/<object_type>/?at_time=1998-07-26 17:16:07


you'll obviously get no results.


^^^^^^^^^^^^^^^^^^^^^^
Object changes history
^^^^^^^^^^^^^^^^^^^^^^

Soon you'll be able to request object changes history in one GET request, however, at the moment this feature is in development.


