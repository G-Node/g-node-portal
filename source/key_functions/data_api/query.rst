================
Search and Query
================

API supports simple full-text search across all objects, available for a logged-in user, as well as fine-grained queries with different filters and criterias.

-----
Query
-----

^^^^^^^^^^^^^
Field lookups
^^^^^^^^^^^^^

You may filter results of the request that returns a list of a particular object type (like :ref:`AnalogSignal <AnalogSignal>` or :ref:`SpikeTrain <SpikeTrain>`) by providing several parameters in the GET request, for example:

::

    Request: GET /electrophysiology/event/?owner=alex&label__icontains=stimulus

In this example we filter all available :ref:`events <Event>` to get only those belonging to 'alex' and having 'stimulus' in the label. You may use filters with any object attribute, in addition, for every filter you may specify special lookup, like __icontains in this example. The following lookups are supported (The back-end of the DATA API is based on `django <https://www.djangoproject.com/>`_ thus the whole query mechanism was somehow inspired by `django queryset machinery <https://docs.djangoproject.com/en/dev/ref/models/querysets/>`_):
 * **exact** - exact match
 * **iexact** - case-insensitive exact match
 * **contains** - case-sensitive containment test
 * **icontains** - case-insensitive containment test
 * **in** - in a given list. example:

::

    Request: GET /electrophysiology/segment/?index__in=[7,9,14]

further:
 * **gt** - greater than. example:


::

    Request: GET /electrophysiology/analogsignal/?t_start__gt=15.55


and
 * **gte** - greater than or equal to
 * **lt** - less than
 * **lte** - less than or equal to
 * **isnull** - is equal to null (provide 0 for False, any other value means True)
 * **startswith** - case-sensitive starts-with
 * **istartswith** - case-insensitive starts-with
 * **endswith** - case-sensitive ends-with
 * **iendswith** - case-insensitive ends-with
 * **year** - for date/datetime fields, exact year match. example:

::

    Request: GET /electrophysiology/analogsignal/?date_created__year=2010

as well as
 * **month** - for date/datetime fields, exact month match
 * **day** - for date/datetime fields, exact day match
 * **week_day** - for datetime fields, a 'day of the week' match. Takes an integer value representing the day of week from 1 (Sunday) to 7 (Saturday)

At the moment the query works only in "filter" mode, including in the response only those values, that MATCH the criteria provided in the GET request. We are extending the API to support the "exclude" mode, which will work the opposite way and return the results that do NOT match the provided criteria. This will provide more flexibility in using the query mechanism. This function is coming soon.


^^^^^^^^^^^^^^^^^^^^^
Filter by permissions
^^^^^^^^^^^^^^^^^^^^^

A useful case for filtering could be filtering objects by their authorship or permissions. For example, if you need to access only public objects (having safety_level = 1, see :ref:`useful mappings <useful_mappings>`), use the request like:

::

    Request: GET /metadata/section/?safety_level=1


To quickly get a list of objects, shared by a particular user (who's account name should be like 'alex'), try:

::

    Request: GET /electrophysiology/analogsignal/?owner__icontains=alex


^^^^^^^^^^^^^^^^^
Splitting results
^^^^^^^^^^^^^^^^^

DATA API limits the number objects to be retrieved in one request by 100. If there are more than a 100 objects you should request them using offset=<some_number> (offset=200 etc.). You may also limit the number of objects by max_results=<some_number> parameter. The start / end indexes for the selected objects are usually contained in the response as "selected_range". For example, in case there are more than 500 objects, the following request:

::

    Request: GET /metadata/section/?offset=120&max_results=30

will retrieve 30 objects, indexed from 120 to 149 respectively.


^^^^^^^^^^^^^^^^^^^^^^^^^
Querying data by metadata
^^^^^^^^^^^^^^^^^^^^^^^^^

Bugs here!

^^^^^^^^^^^^^^^^
Ordering results
^^^^^^^^^^^^^^^^

This feature is being developed.


----------------
Full-text search
----------------

A full-text search feature is being developed.


