===================
Working with Arrays
===================

This section is about objects that have any data associated with them. It provides details on how to handle the data - how to create a new object, how to access related data, what are the possibilities when making data queries.

-----------------------
Data is stored in Files
-----------------------

We use `HDF5 file format <http://www.hdfgroup.org/HDF5/>`_ to store any array data. We made several performance tests which showed this is the optimal way of managing large data arrays. Practically that means that for every object, that has an array data associated with it (like :ref:`AnalogSignal <AnalogSignal>` or :ref:`SpikeTrain <SpikeTrain>`) we store the data in related HDF5 file. In order to access the data using the API you basically have to access the associated file. There are several format options, explained in the :ref:`sections below <data_format>`.

.. _create_array_routine:

^^^^^^^^^^^^^^^^^^^^^^^^^^
Create data-related object
^^^^^^^^^^^^^^^^^^^^^^^^^^

So the routine to create a new data-associated object has actually two steps:
 * you upload the file with the data, the file is being validated on the server
 * you create your object using :ref:`standard POST request <common_create>` where, inside the body, you mention the link to the created datafile
In between it's recommended to wait until the file is completely uploaded and validated. The requirements to the uploaded file in order it can be later used with :ref:`data-related objects <array_upload_note>` is provided in the :ref:`HDF5 section <data_format>`.

To check the file is correct and can be assigned to a :ref:`data-related object <array_upload_note>`, you may request the uploaded file information (:ref:`usual GET request <get_single_object>`) and make sure it has appropriate type (type = 5 in particular for files with arrays, see :ref:`file types <file_types>`).

^^^^^^^^^^^
Access data
^^^^^^^^^^^

Accessing the data basically also suggests you send a couple of HTTP requests: 
 * at first you get the object in JSON with reference to the datafile, 
 * at second you access the datafile directly using the reference from previous response (a detailed example is in the next section). 
This routine is needed to mitigate the unefficiency of transferring data, for instance, directly inside one full JSON response body assembled for several objects, which increases the amount of data transferred and leads to a high server and client load. In cases with large data arrays, which are typical for neuroscience, efficient data access is crucial.

---------------------
Partial data requests
---------------------

All :ref:`data-related objects <array_upload_note>`, which represent electrophysiological signals, support partial data requests. In fact, that means you may request only a slice of the data array if needed and avoid downloading of the whole signal, which could be very practical when accessing large data arrays. This works for both single and multiple object requests. Use the following GET request parameters to configure partial data response:

 * [start_time] - start time of the required range (calculated using the same time unit as the t_start of the signal)
 * [end_time] - end time of the required range (calculated using the same time unit as the t_start of the signal)
 * [duration] - duration of the required range (calculated using the same time unit as the t_start of the signal)
 * [start_index] - start index of the required datarange (an index of the starting datapoint)
 * [end_index] - end index of the required range (an index of the end datapoint)
 * [samples_count] - number of points of the required range (an index of the end datapoint)
 * [downsample] - number of datapoints. This parameter is used to indicate whether downsampling is needed. The downsampling is applied on top of the selected data range using other parameters (if specified).

*Note. Some reasonable combinations of these parameters (like 'start_time' and 'duration' or 'start_index' and 'end_time' will return a correct response. Using redundant number of parameters will lead to their disregard, useless combinations may throw a 400 bad request.*

For example, send the following GET request

 ::
    
    Request: GET /electrophysiology/analogsignal/11/?start_time=50&duration=100&downsample=20


to get the 100ms time window of the :ref:`AnalogSignal <AnalogSignal>` with ID = 11 starting from 50ms, downsampled from to 20 datapoints, first request the object:

 ::

    HTTP SUCCESS (200)
    
    {
    ...
    "selected": [
        {
        "fields": {
            "id": 11,
            "name": "LFP FIX Signal-10, size: 2000",
            "signal": {
                "units": "mv",
                "data": "/datafiles/2177?start_index=1000&end_index=3001&downsample=20"
            },
            "t_start": {
                "units": "ms",
                "data": 50
            },
            "sampling_rate": {
                "units": "hz",
                "data": 20000
            },
            "guid": "00ec575f2f20eae37907d1927afaabe09bab2b39",
            ...
        },
        "model": "neo_api.analogsignal",
        "permalink": "/electrophysiology/analogsignal/11"
        }
    ],
    ...
    }

and then request an appropriate data slice using the link in the response (in this case "/datafiles/2177?start_index=1000&end_index=3001&downsample=20").

Notice that the "t_start" data field in the response has a data value of 3, indicating the start of the requested part of the array, but not the full original array.

.. _data_format:

-------------------------
Data in different formats
-------------------------

You may request data in different format, depending on what better is suitable for your client application. Up to the moment we support the following formats:
 * HDF5
 * JSON

^^^^
HDF5
^^^^

By default you get back data as an HDF5 file. Inside the file, an appropriate array is stored in the root of the HDF5 hierarchy (normally there shouldn't be any other object in the file, so just accessing the first object should return a correct array); an array should have a name equal to its :ref:`global ID <common_terms>`. To summarize:
 * it is an HDF5 file
 * an array is in the root ("/")
 * the dimensions of the array are compatible with the :ref:`object specification <ePhys Objects>`


^^^^
JSON
^^^^

In order to request different format, provide "format" parameter in the GET request. For the moment ther is only one alternative format - JSON, so just provide "format=json" in the request GET to get back data in the response body as a list of values (be aware the response is a simple string).





