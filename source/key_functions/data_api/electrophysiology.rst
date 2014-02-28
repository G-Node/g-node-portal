=================
Electrophysiology
=================

The Data API provides a :ref:`common set of objects <ePhys Objects>` for dealing with electro-physiological (in vivo and/or simulated) data. It supports a number of functions to manage these objects, and the following sections will explain them in detail. To understand the concept of our model for electrophysiology please consider a nice example in the :ref:`overview <overview_ephys>` and :ref:`object specification <ePhys Objects>`.

----------------------------
Create or update data object
----------------------------

Send an authorized HTTP request with the body (in JSON format), providing object data (attributes, arrays and relationships), to

::
    
    Request: POST /electrophysiology/<object_type>/

in order to create a new object (except data-containing objects, like :ref:`AnalogSignal <AnalogSignal>` or :ref:`SpikeTrain <SpikeTrain>`, for which :ref:`you have to upload data first <array_upload_note>`), you should specify object attributes, data fields (if exist), and relationships in the request body as a JSON object. For example, send an authorized HTTP POST request to the "/electrophysiology/segment/" with the following body

::

    HTTP POST /electrophysiology/segment/

    {
        "name": "Trial 12, Saccade, Stim. 45 grad color red",
        "filedatetime": "2012-04-10 10:00:00",
        "index": 12
    }

to create a new :ref:`Segment <Segment>`. If the response status is 'Created' (201) a client receives a new ID of a segment from the response. Here is the response example:

::

    HTTP CREATED (201)
    
    {
        "logged_in_as": "bob",
        "objects_selected": 1,
        "selected": [{
                    "fields": {
                    "index": 12,
                    "name": "Trial 12, Saccade, Stim. 45 grad color red",
                    "current_state": 10,
                    "filedatetime": null,
                    "last_modified": "2012-04-10 13:38:23",
                    "safety_level": 3,
                    "date_created": "2012-04-10 13:38:23"
                },
            "model": "neo_api.segment",
            "permalink": "/electrophysiology/segment/213"
        }],
        "message": "Object created successfully.",
        "selected_range": [0, 0],
        "message_type": "object_created"
    }


A full set of examples for all supported ePhys objects can be found in :doc:`object examples <object_examples>`.

*Note. To understand, which attributes, data fields and relationships are supported for every NEO object please consider the* :ref:`ePhys specification <ePhys Objects>`.

To update the :ref:`Segment <Segment>`, changing some ot its parameters, you need to send an authorized HTTP POST to the same URL providing the ID of the segment at the end of the URL. Assuming the segment we've just created was assigned an ID = 213, send an HTTP POST to the "/electrophysiology/segment/213/" with the following body

::
    
    HTTP POST /electrophysiology/segment/213/

    {
        "name": "Trial 12, Saccade, Stim. 45 grad color green",
        "block": 1
    }


to change the name of the segment and link it to the :ref:`Block <Block>` with ID 1. When update is required, you may specify only those parameters that you want to update. All other attributes, arrays and relationships will stay as they were.

A response should look similar to this:

::

    HTTP SUCCESS (200)
    
    {
    "logged_in_as": "bob",
    "objects_selected": 1,
    "selected": [{
        "fields": {
            "index": 12,
            "block": "/electrophysiology/block/1",
            "name": "Trial 12, Saccade, Stim. 45 grad color green",
            "current_state": 10,
            "filedatetime": null,
            "last_modified": "2012-04-10 13:38:23",
            "safety_level": 3,
            "date_created": "2012-04-10 13:38:23"
        },
        "model": "neo_api.segment",
        "permalink": "/electrophysiology/segment/213"
    }],
    "message": "Here is the list of requested objects.",
    "selected_range": [0, 0],
    "message_type": "object_selected"
    }


Our model for electrophysiology, based on the `NEO® model <http://neo.readthedocs.org/en/latest/index.html>`_, suggests particular object relationships: a segment (timeframe) is a part of a block (experiment), events happen within a segment etc. Object relationaships help to quickly organize neurophysiological data in the consistent and easily accessible structure. Relationships are updated in a similar way as normal attributes: provide relationships inside the POST request to save appropriate data structure of your recordings. The full list of supported connections can be found in the :ref:`key concepts <ePhys Objects>` in attribute/relationaship tables, given for every object. Also consider the :doc:`object examples page <object_examples>`.

Some objects have data fields - they are similar to normal attributes, however one should also specify data units to save them (the 'sampling_rate' data field of :ref:`AnalogSignal <AnalogSignal>` object typically has units "Hz" or "KHz", :ref:`Event <Event>` object has "time" in "ms" etc). Use a dict with "data" and "units" keys while updating these fields:

::

    "time": {
        "units": "ms",
        "data": 65
    }

*Note. For the moment, the following unit types are supported:*
 * in time domain: "s", "ms", "mcs"
 * in signal domain: "v", "mv", "mcv"
 * sampling rate for signals: "hz", "khz", "mhz", "1/s"


.. _array_upload_note:

Objects, containing data arrays (data-containing objects), require special processing. These objects include:
 * :ref:`AnalogSignal <AnalogSignal>`
 * :ref:`IrSaAnalogSignal <IrSaAnalogSignal>`
 * :ref:`AnalogSignalArray <AnalogSignalArray>`
 * :ref:`SpikeTrain <SpikeTrain>`
 * :ref:`Spike <Spike>`
 * :ref:`EventArray <Event>`
 * :ref:`EpochArray <Epoch>`

To save or update a data-containing object you have to upload a file containing corresponding data first, and then create (update) an object with the usual HTTP POST request as shown above. This is done for performance reasons, huge amounts of data are poorly handled in JSON format together with the rest of the request body. In order to know how to upload the data please consider our description of the :doc:`array management <array_data>`. After you successfully uploaded a file with the data, you should use it's permalink in the POST request to connect a data-containing object with the actual data.


-----------------------
Getting a single object
-----------------------

To get a NEO object with its attributes send a following GET request 

::
    
    Request: GET /electrophysiology/<obj_type>/<obj_id>/


You'll get the response, similar to:

::

    HTTP SUCCESS (200)
    
    {
    "logged_in_as": "bob",
    "objects_selected": 1,
    "selected": [{
        "fields": {
            "name": "From File: data_in_csv.txt, #952",
            "signal_size": 12000,
            "current_state": 10,
            "last_modified": "2012-03-22 17:19:41",
            "safety_level": 3,
            "date_created": "2012-03-22 17:19:41"
        },
        "model": "neo_api.analogsignal",
        "permalink": "/electrophysiology/analogsignal/952"
    }],
    "message": "Here is the list of requested objects.",
    "selected_range": [0, 0],
    "message_type": "object_selected"
    }


Play with the :ref:`q request GET parameter <q_parameter>` to get different information about an object, including its attributes, data arrays, downstream and upstream relationships. In :doc:`array management <array_data>` section you'll also find how to request a slice of the signal(s) or request it's downsampling, which is useful when signal is very long.


-------------------------
Getting a List of Objects
-------------------------

Use the following HTTP GET 

::
    
    Request: GET /electrophysiology/<object_type>/?params


to query multiple :ref:`ePhys objects <ePhys Objects>` of a specific type. For example, if you want to get all Analog Signals available for a specific user, send the following request 

::
    
    GET /electrophysiology/analogsignal/?q=link


You receive a list of Analog Signal permalinks as a response:

::

    HTTP SUCCESS (200)
    
    {
    "logged_in_as": "bob",
    "objects_selected": 3,
    "selected": [
        {
        "fields": {},
        "model": "neo_api.analogsignal",
        "permalink": "/electrophysiology/analogsignal/1"
        },
        {
        "fields": {},
        "model": "neo_api.analogsignal",
        "permalink": "/electrophysiology/analogsignal/2"
        },
        {
        "fields": {},
        "model": "neo_api.analogsignal",
        "permalink": "/electrophysiology/analogsignal/3"
        }
    ],
    "message": "Here is the list of requested objects.",
    "selected_range": [0, 0],
    "message_type": "object_selected"
    }


By default the API will return the first 100 data objects in the response. Use :ref:`offset parameter <offset_parameter>` and/or :doc:`standard filters <query>` to refine the selection.

All :ref:`ePhys objects <ePhys Objects>` support sharing with other users by managing permissions. To learn more about sharing objects please refer to :doc:`permissions <permissions>`.


-------------------------
Accessing object metadata
-------------------------

You may request object metadata directly by sending a GET to 

::
    
    GET /electrophysiology/analogsignal/<obj_id>/metadata/


You will get a list of [<property>, <value>] pairs in the response:


::

    HTTP SUCCESS (200)
    
    {
        ...
        "metadata": [
            [
                {
                    "fields": {
                        "comment": "",
                        "definition": "",
                        "name": "bla22!",
                        "section": "/metadata/section/2",
                        "id": 1,
                        "safety_level": 3,
                        ...
                    },
                    "model": "metadata.property",
                    "permalink": "/metadata/property/1"
                },
                {
                    "fields": {
                        "id": 1,
                        "safety_level": 3,
                        "owner": "/profiles/profile/5",
                        "data": "1584",
                        "parent_property": "/metadata/property/1"
                        ...
                    },
                    "model": "metadata.value",
                    "permalink": "/metadata/value/1"
                }
            ],
            ...
        ]
    }

or an empty list if no metadata is assigned.

For the moment we do not support accessing metadata directly for several objects.


