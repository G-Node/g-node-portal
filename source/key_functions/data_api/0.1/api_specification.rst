.. _data_api_specification:

**********************
DATA API Specification
**********************

======================================
Terminology used in this specification
======================================

Throughout this specification, it's refered to a series of terms like section or datafile, which are intended to have specific meanings.

**Datafile**
    Any data item maintained by G-Node. It can be either a binary/ascii file or a data structure, hosted in the native G-Node format. This could include a PDF file, MP3 file, TXT file, or a raw data structure in the NEO (native G-Node) format (see http://packages.python.org/neo/api_reference.html). A datafile is created by uploading an ordinary file to G-Node, and possibly converting it to the native G-Node format. That is to say, when you upload something like a MultichannelSystems® file, you have an option to convert it to the native format, and then it is considered as a native (raw) data structure in this specification. If you do not convert it when you upload, then it is considered as just "file".

**Sections**
    A container of other sections, properties or datafiles. Intuitively it's like a folder in a usual file system. The Section® is a prototype of the odML section (see http://www.g-node.org/projects/odml) and is implemented inline with odML concepts and methodology.

**Properties and Values**
    A property is a flexible way to annotate your data (implemented in line with odML). Within these could be a model of your recording device, duration of the stimulus, a layer of the cell you record from. Some properties are provided by default when you create a section of either type. You just need to set values, appropriate for your research. Importantly, properties and values are also used to "label" your data objects (segments, analog signals, spiketrains etc.), so you can access data from different perspectives using metadata. 

**NEO Raw Data Object**
    NEO® objects are useful when working with the raw data. NEO objects include Segments, Analog Signals, Spike Trains etc. (see http://packages.python.org/neo/api_reference.html) It is a flexible data structure, compartible with several well-known data-formats, and useful for data analysis. You may convert your files to the NEO (native G-Node) structure using API, when the file is convertible (currently supported information extraction from neuroshare-compliant formats, http://neuroshare.sourceforge.net/index.shtml).

*Note. For all URLs, given later in this specification, you should add a G-Node prefix URL, which is* https://portal.g-node.org/data/

==============
AUTHENTICATION
==============

To authentice at G-Node, please use the following methods:

--------------
Authentication
--------------

 ::

    Request: POST /account/authenticate/

parameters:
 * username - user account name
 * password - user password

A successful response will contain a cookie called *sessionid*. Use this cookie within every request to work with G-Node API.

*Note. If you want to just TRY the API from the usual web-browser (like Firefox, Chrome, Safari etc.), just go to the login page (* /account/login/ *), fill in the login form and subnit it to authenticate. After successful authentication sessionid will be stored in the browser, and you may use the address bar (or any REST-client, for example* `Advanced REST Client for Chrome`_ *) to query the API.*

.. _Advanced REST Client for Chrome: https://chrome.google.com/webstore/detail/ahdjpgllmllekelefacdedbjnjaplfjn/


==================
KEY API PRINCIPLES
==================

The DATA API is designed in a RESTful way, e.g. to have similar routines for creating, updating and selecting different objects that API supports. To get the list of available objects of a specific type (e.g. Analog signal, or a Section, or a Datafile) you need to send a GET request to the URL, ending with the name of this type, for example:

 ::

    Request: GET /electrophysiology/analogsignal/

or

 ::

    Request: GET /metadata/sections/

You may filter the list of objects by owner, permissions or specific conditions on the attributes, specifying criterias directly in the GET as parameters. To filter by owner, include owner=<user_name> or owner=<user_ID> parameters. Specify safety_level=3 or safety_level=1 to get only private or public objects respectively. That is basically applicable to any attribute: to filter by attribute (field lookups) you need to provide attribute name followed by a double underscore and a lookup type. For example 

 ::

    Request: GET /metadata/sections/?owner=alex&safety_level=1&name__icontains=experiment

filters alex's publicly available metadata sections containing 'experiment' in the name, or

 ::

    Request: GET /metadata/sections/?date_created__gt=2012-02-23 13:20:11

- filters out all objects created before February, 23 2012

The following lookups are supported:
 * exact
 * iexact
 * contains
 * icontains
 * gt
 * gte
 * lt
 * lte
 * startswith
 * istartswith
 * endswith
 * iendswith
 * year
 * month  
 * day  
 * week_day
 * isnull

The following lookups are coming soon:
 * in
 * search
 * regex
 * iregex

DATA API limits the number objects to be retrieved in one request by 1000. If there are more than 1000 objects you should request them using offset=1000 (offset=2000 etc.). You may also limit the number of objects by max_results=<some_number> parameter. The start / end indexes for the selected objects are usually contained in the response as "selected_range". For example:

 ::

    Request: GET /metadata/sections/?offset=120&max_results=300

To get a single object you should specify its ID at the end of the URL:

 ::

    Request: GET /metadata/sections/10/



You may also control the amount of the information about avery object you receive using the q parameter. Supported values are: 
    - 'link' - just permalink
    - 'info' - object with local attributes
    - 'beard' - object with local attributes AND foreign keys resolved
    - 'data' - data-arrays or any high-volume data associated
    - 'full' - everything mentioned above
This works with both list of objects and single objects. For example

 ::

    Request: GET /metadata/sections/2/?q=full

will retrieve the full information about the section:

 ::

    HTTP SUCCESS (200)

    {
        "logged_in_as": "bob",
        "objects_selected": 1,
        "selected": [{
            "fields": {
                "parent_section": null,
                "tree_position": 1,
                "odml_type": 20,
                "name": "stimulus",
                "datafile_set": [],
                "property_set": [
                    "metadata/properties/10",
                    "metadata/properties/11",
                    "metadata/properties/15"
                ],
                "current_state": 10,
                "is_template": false,
                "last_modified": "2012-03-05 16:45:26",
                "safety_level": 2,
                "block_set": [],
                "owner": 2,
                "date_created": "2012-02-23 18:19:53",
                "section_set": [
                    "metadata/sections/4",
                    "metadata/sections/6"
                ],
                "user_custom": null,
                "description": ""
            },
            "model": "metadata.section",
            "permalink": "metadata/sections/2"
        }],
        "message": "Here is the list of requested objects.",
        "selected_range": [0, 0],
        "message_type": "object_selected"
    }

To update one or several attributes of an object send POST to the object permalink, providing new parameters / values in the POST body. For example, this request updates the name and comment for the property with ID 2:

 ::

    Request: POST /metadata/properties/2/

    {
        "name": "15 - here is new name",
        "comment": "We just changed the property name"
    }


Bulk object update is also possible. To make changes to several objects at once, you need to provide bulk_update=1 parameter. Changes will be applied to all objects in the selection; use filters so select only objects, that have to be changed. The following resuest moves all properties with name having "sampling" to the section with ID 146:

 ::

    Request: POST /metadata/properties/2/?name__icontains=sampling&bulk_update=1

    {
        "section": 146
    }


Send the DELETE request to an object permalink to permanently archive it.


===============
DATA MANAGEMENT
===============

G-Node Data API provides a common set of objects for dealing with electro-physiological (in vivo and/or simulated) data. It is based on the NEO® data object model. NEO® approach provides common names and concepts to deal with electrophysiological data in an easy and well-structured way. It accounts for the trade-off between minimizing the data validation routine and at the same time keeping sufficient consistency. The NEO® data model concept is already used in several neuroscientific projects (OpenElectrophy, NeuroTools) and thus provides a promising opportunity to cooperate with other initiatives while not increasing the number of existing standards. On top of this data model we provide a set of core services (Data API). You may work with raw data objects - Spike Trains, Analog Signals, combining them in Segments, performing computations with analog signals and spike trains (sum, mean, fft etc.), and store new results back to the G-Node. The following types of raw data objects are supported:

 * block
 * segment
 * event
 * eventarray
 * epoch
 * epocharray
 * unit
 * spiketrain
 * analogsignal
 * analogsignalarray
 * irsaanalogsignal
 * spike
 * recordingchannelgroup
 * recordingchannel  

Every object serves a specific purpose to organize your electrophysiological data. 'Block's mainly represent one experiment (or the whole data, recorded within one experiment). 'Segment' represents an experimental trial, a "time frame" within an experiment with unique experimental conditions. Blocks may contain segments, like experiment consists of several trials. A 'Segment' may contain 'AnalogSignal's, 'Event's or 'Epoch's, which are being used to organize recorded signals, single time events, or events with duration inside one experimental time-frame. Segment may also contain 'SpikeTrain' objects to accommodate the spike data, sorted online. For every 'Block' one may define a set of 'RecordingChannel's according to the experimental setup. These channels can be organized under a 'RecordingChannelGroup', to keep track of tethrodes or anything else. 'AnalogSignals' can be linked to the appropriate 'RecordingChannel's to make the dataset consistent.

You may find more information and the original description of NEO® classes here `NEO classes`_.

.. _NEO classes: http://packages.python.org/neo/api_reference.html

Every raw data object has a set of *attributes*, *data fields*, it may also have relationships, like *parents* and *children*. For example, a segment has to have an attribute 'name'. 'AnalogSignal' should have a 'sampling_rate' data field, which consists of the unit (say, Hz) and a value (say, 20000). A 'Block' consists of 'Segments', which means the 'Block' has a child 'Segment', and a 'Segment' has a parent 'Block'. In the following tables you may find object descriptions:

**Table 2.1 Object Simple Attributes**

=====================   ===============================
Object Type             Attributes
=====================   ===============================
block                   'name'\*, 'filedatetime', 'index'
segment                 'name'\*, 'filedatetime', 'index'
event                   'label'\*
eventarray              No attributes
epoch                   'label'\*
epocharray              No attributes
unit                    'name'\*
spiketrain              No attributes
analogsignal            'name'\*
analogsignalarray       No attributes
irsaanalogsignal        'name'\*
spike                   No attributes
recordingchannelgroup   'name'\*
recordingchannel        'name'\*, 'index'
=====================   ===============================

*Note. Attributes with \* asterisk are mandatory.*

**Table 2.2 Object Data Fields (attributes with units)**

=================   =========================================
Object Type         Data fields
=================   =========================================
event               'time'
epoch               'time', 'duration'
spiketrain          't_start', 't_stop', 'times', 'waveforms'
analogsignal        'sampling_rate', 't_start', 'signal'
irsaanalogsignal    't_start', 'signal', 'times'
spike               'left_sweep', 'time', 'sampling_rate', 'waveforms'
=================   =========================================


**Table 2.3 Object Children**

=====================   =========================================
Object Type             Children
=====================   =========================================
block                   'segment', 'recordingchannelgroup'
segment                 'analogsignal', 'irsaanalogsignal', 'analogsignalarray', 'spiketrain', 'spike', 'event', 'eventarray', 'epoch', 'epocharray'
eventarray              'event'
epocharray              'epoch'
unit                    'spiketrain','spike'
analogsignalarray       'analogsignal'
recordingchannelgroup   'recordingchannel', 'analogsignalarray'
recordingchannel        'unit', 'analogsignal', 'irsaanalogsignal'
=====================   =========================================


**Table 2.4 Object Parents**

=====================   =========================================
Object Type             Parents
=====================   =========================================
segment                 'block'
event                   'segment', 'eventarray'
eventarray              'segment'
epoch                   'segment', 'epocharray'
epocharray              'segment'
unit                    'recordingchannel'
spiketrain              'segment', 'unit'
analogsignal            'segment', 'analogsignalarray', 'recordingchannel'
analogsignalarray       'segment'
irsaanalogsignal        'segment', 'recordingchannel'
spike                   'segment', 'unit'
recordingchannelgroup   'block'
recordingchannel        'recordingchannelgroup'
=====================   =========================================

G-Node Data API supports a number of functions to manage objects, listed above. The following sections 2.1 - 2.8 give an overview .

--------------------------------
2.1 Create or update data object
--------------------------------

Send an authorized HTTP request with the body (in JSON format), providing object data (attributes, arrays and relationships), to

 ::
    
    Request: POST /electrophysiology/<object_type>/

in order to create a new NEO object. You should specify object attributes, data fields (if exist), and relationships in the request body as a JSON object. For example, send an authorized HTTP POST request to the "/electrophysiology/segment" with the following body

 ::

    HTTP POST /electrophysiology/segment/

    {
        "name": "Trial 12, Saccade, Stim. 45 grad color red",
        "filedatetime": "10/05/2011",
        "index": 12
    }

to create a new segment. If the response status is 'Created' (201) a client receives a new ID of a segment from the response. Here is the response example:

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
            "permalink": "/electrophysiology/segment/1"
        }],
        "message": "Object created successfully.",
        "selected_range": [0, 0],
        "message_type": "object_created"
    }


A full set of examples for all supported NEO object can be found here (:ref:`api_object_examples`). 

*Note. To understand, which attributes, data fields and relationships are supported for every NEO object please consider Tables 2.1 - 2.4, as well as the NEO specification* (http://packages.python.org/neo/api_reference.html).

To update the segment, changing some ot its parameters, you need to send an authorized HTTP POST to the same URL providing the ID of the segment at the end of the URL. Assuming the segment we've just created was assigned an ID = 213, send an HTTP POST to the "/electrophysiology/segment/213/" with the following body

 ::
    
    HTTP POST /electrophysiology/segment/213/

    {
        "name": "Trial 12, Saccade, Stim. 45 grad color green",
        "block": 1
    }


to change the name of the segment and link it to the Block with ID 1. When update is required, you may specify only those parameters that you want to update. All other attributes, arrays and relationships will stay as they were.

A response should look similar to this:

 ::

    HTTP SUCCESS (200)
    
    {
    "logged_in_as": "bob",
    "objects_selected": 1,
    "selected": [{
        "fields": {
            "index": 12,
            "name": "Trial 12, Saccade, Stim. 45 grad color green",
            "current_state": 10,
            "filedatetime": null,
            "last_modified": "2012-04-10 13:38:23",
            "safety_level": 3,
            "date_created": "2012-04-10 13:38:23"
        },
        "model": "neo_api.segment",
        "permalink": "/electrophysiology/segment/1"
    }],
    "message": "Here is the list of requested objects.",
    "selected_range": [0, 0],
    "message_type": "object_selected"
    }


The NEO® model sugest object relationships, like a segment may be connected to a block. Object relationaships help to quickly organize neurophysiological data in the consistent and easily accessible structure. The full list of supported connections can be found in Tables 2.3 - 2.4. Please provide the relationships inside the POST request to save appropriate data structure of your recordings. Consider the examples page (:ref:`api_object_examples`).

Some objects have data fields - they are similar to normal attributes, however one should also specify data units to save them (a signal data field of Analog Signal object typically has units "mV", Event object has "time" in "ms" etc). To save or update the associated object data, provide object data in the request body under a corresponding parameter, as shown in examples page (:ref:`api_object_examples`). You may find the whole list of data-related objects with corresponding parameters in the table 2.2 above.

*Note. For the moment, the following unit types are supported:*
 * in time domain: "s", "ms", "mcs"
 * in signal domain: "v", "mv", "mcv"
 * sampling rate for signals: "hz", "khz", "mhz", "1/s"

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


Play with the q request GET parameter to get different information about an object, including its attributes, data arrays, downstream and upstream relationships. In the following sections you'll also find how to request a slice of the signal(s), when the signal is very long.

---------------------
Partial Data Requests
---------------------

You may want to request object data or relationaships only (see list of objects and their attributes, data fields and relationaships in the Tables 2.1 - 2.4), or even just get the overall information about an object (like object size) without getting any data or attributes. We support the following parameters for all GET requests for a single object 

 * [q] - can be one of "full", "info", "data", "beard", "link". The response will contain content varying from just generic object information ("link", "info") to the full response ("full").
 * [cascade] - when set to "True" will recursively retrieve all the children objects (not only their permalinks). Please be careful with such requests, requesting a big block in a "cascade" mode may result in several Gigobytes of data to download, and significant delays.

Specifically for signal-based objects (Analog Signal, Irregularly Sampled Signal) the following GET request parameters are supported:

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
    
    Request: GET /electrophysiology/analogsignal/11/?start_index=30&end_index=100&downsample=10&q=data


to get the Analog Signal range with datapoints as of 30 to 100, downsampled from 71 points to 10:

 ::

    HTTP SUCCESS (200)
    
    {
    "logged_in_as": "bob",
    "objects_selected": 1,
    "selected": [{
        "fields": {
            "sampling_rate": {
                "units": "hz",
                "data": 10000
            },
            "signal": {
                "units": "mv",
                "data": [
                    -109.15087908065715,
                    -143.56599349341815,
                    -123.28235629296393,
                    -116.57424590237954,
                    -85.95255136469267,
                    -86.48956135366747,
                    -45.100152757898336,
                    -29.496947080422245,
                    -52.86667933701428,
                    -30.721555308717267
                ]},
            "t_start": {
                "units": "ms",
                "data": 3
            }
        },
        "model": "neo_api.analogsignal",
        "permalink": "/electrophysiology/analogsignal/11"
        }
    }],
    "message": "Here is the list of requested objects.",
    "selected_range": [0, 0],
    "message_type": "object_selected"
    }



Notice that the "t_start" data field in the response has a data value of 3, indicating the start of the retrieved signal.

-------------------------
Getting a List of Objects
-------------------------

Use the following HTTP GET 

 ::
    
    Request: GET /electrophysiology/<object_type>/?params


to query NEO objects of a specific type. For example, if you want to get all Analog Signals available for a specific user, send the following request 

 ::
    
    GET /electrophysiology/analogsignal/?max_results=3&q=link


You receive a list of Analog Signal permalinks as a response:

 ::

    HTTP SUCCESS (200)
    
    {
    "logged_in_as": "bob",
    "objects_selected": 1,
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


By default the API will return the first 1000 data objects in the response. Use standard filters to refine the selection.


---------------
Data Annotation
---------------

You may annotate (or "tag" or "label") one or several Raw Data objects with particular metadata values by using the following request:

 ::
    
    POST /electrophysiology/analogsignal/11/

    {
        "metadata": [
            "/metadata/values/2",
            "/metadata/values/15"
        ]
    }

If the POST request is sent to the selection (several objects), all objects in the selection will be updated. Importantly, by default metadata values are added to the existing metadata values. If you need to completely overwrite object's metadata, provide m2m_append=0 parameter in the request. This is the way to remove object metadata, providing empty list together with m2m_append=0.

*Note. For example, you have an experiment with stimulus which changes its color across trials. You have several Analog Signals recorded, and you want to indicate, which of those were recorded under which stimulus condition. Assume in the experiment section tree you already defined a property, say, "StimulusColor" with values "red, green, blue". In that case, annotate all required Analog Signals with appropriate metadata values, assigning a "red" value to the signals recorded when the Stimulius was red etc.*


===================
METADATA MANAGEMENT
===================

You may organize your experimental data using odML Sections and Properties. They are useful elements to created flexible nested structures, containing key-value pairs, aimed to help scientists to describe their experiments and annotate recorded data. Here are the actions available for you to manage your experimental metadata.

------------------------
Getting list of sections
------------------------

 ::
    
    GET /metadata/sections/?params

in addition to the usual filters, use the following parameters:

 * [top] - owned (default), shared - return only top (no parent) sections, owned by the user or shared with the user

Example response:

 ::
    
    HTTP SUCCESS (200)
    
    {
    "logged_in_as": "bob",
    "objects_selected": 1,
    "selected": [
        {
        "fields": {},
        "model": "metadata.section",
        "permalink": "/metadata/sections/3"
        },
        {
        "fields": {},
        "model": "metadata.section",
        "permalink": "/metadata/sections/1"
        },
        {
        "fields": {},
        "model": "metadata.section",
        "permalink": "/metadata/sections/2"
        }
    ],
    "message": "Here is the list of requested objects.",
    "selected_range": [0, 0],
    "message_type": "object_selected"
    }


---------------
Section: create
---------------

 ::
    
    Request: POST /metadata/sections/?params

    {
        "parent_section": 2,
        "name": "Electrode",
        "tree_position": 1,
        "odml_type": 0,
        "safety_level": 3,
        "description": "Settings and other experimental setup"
    }

New in the development version:
if you need to create a section from the template (or from other section) - like a normal copy operations, provide the parameters below:

 * source_template - section to copy
 * [with_properties] - whether or not to copy properties, which belong to the section ()
 * [with_datafiles] - whether or not to copy links (!!) to datafiles, which belong to the section (in copy mode).
 * [recursive] - true, false - copy all subsections recursively (in copy mode)

*Note. Files and blocks, contained in the template section, are never duplicated while making a copy: newly created section(s) will not contain any files or other data.*

Response:
    
 ::
    
    HTTP CREATED (201)
    
    {
    "logged_in_as": "bob",
    "objects_selected": 1,
    "selected": [{
            "fields": {
            "tree_position": 1,
            "odml_type": 0,
            "description": "Settings and other experimental setup",
            "current_state": 10,
            "is_template": false,
            "last_modified": "2012-04-10 15:59:00",
            "safety_level": 3,
            "date_created": "2012-04-10 15:59:00",
            "name": "Electrode"
        },
        "model": "metadata.section",
        "permalink": "/metadata/sections/18"
    }],
    "message": "Here is the list of requested objects.",
    "selected_range": [0, 0],
    "message_type": "object_selected"
    }


-----------------------
Getting section details
-----------------------

 ::
    
    GET /metadata/sections/<section_id>

Response:

 ::
    
    HTTP SUCCESS (200)
    
    {
    "logged_in_as": "bob",
    "objects_selected": 1,
    "selected": [{
        "fields": {
            "tree_position": 1,
            "odml_type": 0,
            "description": "Settings and other experimental setup",
            "current_state": 10,
            "is_template": false,
            "last_modified": "2012-04-10 15:59:00",
            "safety_level": 3,
            "date_created": "2012-04-10 15:59:00",
            "name": "Electrode"
            },
        "model": "metadata.section",
        "permalink": "/metadata/sections/18"
    }],
    "message": "Here is the list of requested objects.",
    "selected_range": [0, 0],
    "message_type": "object_selected"
    }


--------------
Delete section
--------------

 ::

    DELETE /metadata/sections/<section_id>/?params


parameters:
 * [force] - true, false (default) - use force=1 to delete the section even if there are other users with collaboration role (able to write). If omitted, the section will not be deleted being in the state having collaborators.

Response:

 ::
    
    {
        "message": "Object was deleted.",
        "logged_in_as": "bob",
        "message_type": "deleted"
    }


--------------------------
Getting list of properties
--------------------------

 ::
    
    GET /metadata/properties/?params


Response:

 ::
    
    HTTP SUCCESS (200)
    
    {
    "logged_in_as": "bob",
    "objects_selected": 1,
    "selected": [
        {
            "fields": {},
            "model": "metadata.property",
            "permalink": "/metadata/properties/1"
        },
        {
            "fields": {},
            "model": "metadata.property",
            "permalink": "/metadata/properties/2"
        }
    ],
    "message": "Here is the list of requested objects.",
    "selected_range": [0, 0],
    "message_type": "object_selected"
    }


---------------------------
Create or update a property
---------------------------

 ::
    
    Request: POST /metadata/properties/<property_id>/

    {
        "comment": "insert some comments here..",
        "definition": "the type of electrode.",
        "name": "type",
        "dependency_value": "",
        "dtype": "",
        "section": 1,
        "uncertainty": "",
        "mapping": "",
        "dependency": "",
        "safety_level": 3,
        "unit": ""
    }

Response:

 ::
    
    HTTP SUCCESS (200) or CREATED (201)
    
    {
    "logged_in_as": "bob",
    "objects_selected": 1,
    "selected": [{
        "fields": {
            "comment": "insert some comments here..",
            "definition": "the type of electrode.",
            "name": "type",
            "dependency_value": "",
            "dtype": "",
            "uncertainty": "",
            "current_state": 10,
            "mapping": "",
            "dependency": "",
            "last_modified": "2012-04-10 16:18:26",
            "safety_level": 3,
            "date_created": "2012-04-10 16:18:26",
            "unit": ""
        },
        "model": "metadata.property",
        "permalink": "/metadata/properties/23"
    }],
    "message": "Here is the list of requested objects.",
    "selected_range": [0, 0],
    "message_type": "object_selected"
    }


------------------
Getting a property
------------------

 ::
    
    GET /metadata/properties/<property_id>


Response is similar to the above.

---------------
Delete property
---------------

 ::
    
    DELETE /metadata/properties/<property_id>


===============
FILE MANAGEMENT
===============

-------------------------
Getting list of datafiles
-------------------------

 ::

    Request: GET /datafiles/?params

Typically you should get the following response:

Response:

 ::
    
    HTTP SUCCESS (200)
    
    {
    "logged_in_as": "bob",
    "objects_selected": 2,
    "selected": [
        {
        "fields": {
            "last_task_id": "2a3c89f4-9328-46aa-801e-b7bf88740f8f",
            "title": "ascii_csv.txt",
            "extracted_info": null,
            "tags": "",
            "current_state": 10,
            "operations_log": "python-neuroshare: failure ascii-csv: success ",
            "conversion_type": 3,
            "caption": "",
            "last_modified": "2012-03-14 17:27:08",
            "safety_level": 3,
            "date_created": "2012-03-14 17:27:08"
        },
        "model": "datafiles.datafile",
        "permalink": "/datafiles/9"
        },
        {
        "fields": {
            "last_task_id": "7c260cfa-2395-40b7-907e-065088f41498",
            "title": "ascii_csv.txt",
            "extracted_info": null,
            "tags": "",
            "current_state": 10,
            "operations_log": "python-neuroshare: failure ascii-csv: success ascii-csv: parsing successul ascii-csv: parsing successul objects parsed: 3ascii-csv: parsing successul objects parsed: 3",
            "conversion_type": 3,
            "caption": "",
            "last_modified": "2012-03-15 10:27:45",
            "safety_level": 3,
            "date_created": "2012-03-14 17:29:21"
        },
        "model": "datafiles.datafile",
        "permalink": "/datafiles/10"
        }
    ],
    "message": "Here is the list of requested objects.",
    "selected_range": [0, 1],
    "message_type": "object_selected"
    }

---------------------------
Getting single file details
---------------------------
 ::
    
    Request: GET /datafiles/<datafile_id>


----------------------------------------------
Upload a datafile (with or without conversion)
----------------------------------------------

 ::
    
    Request: POST /datafiles/?params

    the file should be sent with "raw_file" key.

parameters:
 * [section_id] - provide an ID of the section in which to store the file (recommended).
 * [convert] - 1 (default), 0 - whether try to convert the file into native format, if possible. For the moment the following types are supported: neuroshare, ascii-csv (a csv file where every line is a signal).

*Note. If the file is uploaded into a specific section, the security settings for the new file will be assigned as for the parent section. When no section is specified, the file is private by default.*


------------------------------------------------
Modify datafile attributes, move file to Section
------------------------------------------------

 ::
    
    Request: POST /datafiles/<datafile_id>/?params

    {
        "caption": "Some REAL description goes here..",
        "section": 1236,
    }

*Note. The conversion operation is asynchronous, which means you'll not see the status of the conversion immediately in the response. You may use a details request (2.2) to check whether the file was converted successfully or not.*

*Note. ACL for the file is not changed when moved to a different section.*


-------------------
Datafile conversion
-------------------

The following request

 ::
    
    Request: GET /datafiles/<datafile_id>/convert/


initiates file conversion.


-----------------
Download datafile
-----------------

When the file is not converted, you may get the originally uploaded file. When file is in native format, it can convert all underlying data into a file with the format specified and return it for download.

 ::
    
    Request: GET /datafiles/<datafile_id>/download/?params

New in development version.
in addition to the usual filters, use can use the following parameters:
 * [format] - required file format. The following formats are supported: HDF5. Leave this empty to download an original file.


---------------
Delete datafile
---------------

 ::
    
    Request: DELETE /datafiles/<datafile_id>/?params


in addition to the usual filters, use can use the following parameters:
 * [force] - true, false (default) - use "true" to delete the file even if there are other users with access to the file. If "false" or omitted, the file will not be deleted being in the state having collaborators.



==================
MANAGE PERMISSIONS
==================

There is a possibility to share your data with other users in the G-Node Portal. You may share a particular section, which means all resources inside the section become shared. You may share the section recursively, which implies all subsections in the selected section with all their contents will become shared. You may also share a single or multiple datafiles. Datafiles and sections sharing is controlled via the access control lists. Access control lists are just basic lists that show who has access to a given resource. In the ACLs, the following roles are available for a given document or folder:
 * owner — the owner of the resource (section or file). As an owner you have the ability to modify the ACL, delete the resource, etc.
 * writer — a collaborator.
 * reader — a viewer (equivalent to read-only access).

A resource (datafile or section) by itself has also a sharing state, which can be
 * public — all users may see the contents of the resource.
 * friendly — all friends have a reader role, with no need to assign them for every resource.
 * private — noone, except users, specified explicitly, may see the resource.

With no dependence on the state, people, assigned explicitly by owner of the resource as readers or writers, have corresponding access to the resource.

By <resource_type> in this paragraph we assume either "sections" or "datafiles".

----------------------------
Getting resource permissions
----------------------------

 ::
    
    GET /electrophysiology/<resource_type>/<resource_id>/acl/

Response:

 ::
    
    Response:
    {
        "message": "Here is the list of requested objects.",
        "logged_in_as": "<user_id>",
        "message_type": "object_selected",
        "safety_level": <access_level>, # 1 = public, 2 = friendly, 3 = private
        "shared_with": {
            "<user_id>": <user_permissions>, # 1 = read, 2 = read and write
            "<user_id>": <user_permissions>
        }
    }


-----------------------------------
Update resource ACL (share/unshare)
-----------------------------------

 ::
    
    POST /electrophysiology/<resource_type>/<resource_id>/acl/?params

    {
        "safety_level": "<access level>", # integer specifying the access level: 1: public, 2: friendly, 3: private
        "shared_with": {
            "<user_id>": "<user_permissions>", # integer specifying granted rights: 1: read, 2:read and write
            "<user_id>": "<user_permissions>"
        }
    }

parameters:
 * [recursive] - true, false (default) - apply to all resources recursivery (when <resource_type> = "sections"). Datafiles, found in subsections, will be also updated.
 * [notify] - true, false (default?) - users will be notified per e-mail
 ::
    
    Response:
    {
        "message": "Here is the list of requested objects.",
        "logged_in_as": "<user_id>",
        "message_type": "object_selected",
        "safety_level": <access_level>, # 1 = public, 2 = friendly, 3 = private
        "shared_with": {
            "<user_id>": <user_permissions>, # 1 = read, 2 = read and write
            "<user_id>": <user_permissions>
        }
    }

-------------------------------
Bulk ACL update (share/unshare)
-------------------------------

 ::
    
    POST /<resource_type>/<resource_id>/acl/?params

    {
        "state": "<access level>", # can be public, friendly, private - access level
        "shared_with": {
            "<user_name>": "<user_role>", # a role can be "reader" or "collaborator"
            "<user_id>": "<user_role_id>" # could be IDs as well
        }
    }

parameters:
 * [cascade] - true, false (default) - apply to all resources recursivery (when <resource_type> = "sections"). Datafiles, found in subsections, will be also updated.

New in development version:
 * [notify]: 1 (default), 0 - whether to notify users by mail that an object was shared.


===========================
QUERY DATA USING G-NODE API
===========================

**New in development version.**

You may select Raw Data objects (Segments, Analog Signals, Spike Trains) based on their attributes and labels and those of their parents and children. You may do that by creating Selections. Selections can be saved for future use.

----------------
Create Selection
----------------

 ::
    
    POST /selections/

    {
        "obj_type": "analogsignal", # <obj_type> should be of type NEO.
        "querysets": [
            {
                "filter": {
                    "name__contains": "V1",
                    "date_created__gte": "2006-01-01"
                },
                "filter": {
                    "segment__name__contains": "mice"
                },
                "exclude": {
                    "label": "red",
                    "date_created__gte": "2006-01-01"
                }
            },
            {
                "filter": {
                    "sampling_rate": 20000,
            }],
        "save_as": "provide some name here.."
    }

The way to construct your query is similar to the way described here https://docs.djangoproject.com/en/dev/topics/db/queries/
In the response you'll get selected objects:

 ::
    
    Response:
    TBD

If the "save_as" parameter was provided, the response will contain "selection_id" and "selection_name". You may use this ID to get the contents of a particular selection later.

----------------------------
Get list of saved Selections
----------------------------

 ::
    
    GET /selections/

In the response you'll get all saved selections:

 ::
    
    Response:
    TBD


-------------------
Get saved Selection
-------------------

 ::
    
    GET /selections/<selection_id>/


In the response you'll get selected objects:

 ::
    
    Response:
    TBD



===============================
RESOURCE ACTION HISTORY (draft)
===============================

**New in development version.**

In order not to forget, how certain scientific computations were achieved, or what is the source of a certain analog signal, you may request a history of an action with objects. An action history is a table indicating a source for an operation, description of an operation made and a result.

-------------------------
Get object action history
-------------------------

 ::
    
    GET /<resource_type>/<resource_id>/history/?params


parameters:
 * [start_date] - start date of the history
 * [end_date] - start date of the history


 ::
    
    Response:
    TBD


-----------------------
Get user action history
-----------------------

 ::
    
    GET /profiles/profile/<user_id>/history/?params


parameters:
 * [start_date] - start date of the history
 * [end_date] - start date of the history


 ::
    
    Response:
    TBD

*Note. Usually you will deal with the following HTTP status codes:*

 * 201 - "Created" - new object was successfully created.
 * 200 - "OK" - the object was successfully updated or GET operation performed successfully.
 * 404 - "Not Found" - you have provided an [obj_id], however, such an object does not exist. Or URL is wrond and not supported.
 * 403 - "Forbidden" - you don't have access to create, modify or view an object.
 * 400 - "Bad request" - some of the request parameters are not provided correctly. Consider the "message" contents.
 * 401 - "Unauthorized" - authorization key not provided.


----


FUTURE:
 * bulk sections/datafiles update. just add list of objects to update provided params.
 * bulk NEO update. using selections / list of IDs
 * archiving mechanisms
 * include links in respresentations (http://www.infoq.com/articles/rest-anti-patterns)
 * (SOLVED) caching!! see google + http://www.mnot.net/cache_docs/
 * (SOLVED) etags???
 * version management!!!
 * computations with objects
 * import / conversion using Google Refine technologies
 * (SOLVED) NEO: /electrophysiology/info/<neo_id>/ - short info: think about what kind of info could there be, like number of segments/signals in a block, total data in MB etc.
 * NEO: as above, in /electrophysiology/select/<obj_type>/ - provide short info about every object + a summary about all (total volume, for example)
 * NEO: /electrophysiology/select/<obj_type>/ add query mechanism
 * (SOLVED) NEO: if an array data is missing for data-related objects, should we throw an error? or just allow a user to create it? Throw an error
 * (SOLVED) NEO: should we assign units by default, if not provided? NO
 * NEO security: based on files? or not?
