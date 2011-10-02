.. g-node-portal documentation master file, created by
   sphinx-quickstart on Thu Sep 22 17:35:49 2011.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to g-node-portal's documentation!
=========================================

Contents:

.. toctree::
   :maxdepth: 2

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`


************************
G-Node API Specification
************************

======================================
Terminology used in this specification
======================================

Throughout this specification, it's refered to a series of terms like section or datafile, which are intended to have specific meanings.

**Datafile**
    Any data item maintained by G-Node. It can be either a binary/ascii file or a data structure, hosted in the native G-Node format. This could include a PDF file, MP3 file, TXT file, or a raw data structure in the NEO (native G-Node) format (see http://packages.python.org/electrophysiology/classes.html). A datafile is created by uploading an ordinary file to G-Node, and possibly converting it to the native G-Node format. That is to say, when you upload something like a MultichannelSystems® file, you have an option to convert it to the native format, and then it is considered as a native (raw) data structure in this specification. If you do not convert it when you upload, then it is considered as just "file".

**Section**
    A container of other sections, properties or datafiles. It could be also named folder. These are basically used to "label" your datafiles with a variety of tags (like in GMail), with a difference that you can manage sections (tags) to organize them in a tree-like structure. The Section® is a prototype of the odML section (see http://www.g-node.org/projects/odml) and is implemented inline with odML concepts and methodology.

**Property**
    A property is a flexible way to annotate your data (implemented in line with odML). Within these could be a model of your recording device, duration of the stimulus, a layer of the cell you record from. Some properties are provided by default when you create a section of either type. You just need to set values, appropriate for your research.

**NEO Raw Data Object**
    NEO® objects are useful when working with the raw data. NEO objects include Segments, Analog Signals, Spike Trains etc. (see http://packages.python.org/electrophysiology/classes.html) It is a flexible data structure, compartible with several well-known data-formats, and useful for data analysis. You may convert your files to the NEO (native G-Node) structure using API, when the file is convertible (currently supported information extraction from neuroshare-compliant formats, http://neuroshare.sourceforge.net/index.shtml).

**Resource**
    Any item maintained by the API, including sections, datafiles, and NEO objects.

**Resource ID**
    A unique identifier for each resource. This is usually of the format <type>_<id>, e.g. section_12345. Throughout this specification, if not specified, a typed resource ID should be assumed.

**Untyped resource ID**
    A resource ID, without the type prefix. Given the previous example, an untyped resource ID would be 12345.


*Note. For all URLs, given later in this specification, you should add a G-Node prefix URL, which is https://portal.g-node.org/data*

=================
1. AUTHENTICATION
=================

To authentice at G-Node, please use the following methods:

------------------
1.1 Authentication
------------------

 ::

    Request: POST /account/authenticate/

parameters:
 * username - user account name
 * password - user password

A successful response will contain a cookie called *sessionid*. Use this cookie within every request to work with G-Node API.

*Note. If you want to TRY OUT the API from the normal web-browser (like Firefox, Chrome, Safari etc.), just go to the login page (* https://portal.g-node.org/data/account/login/ *), fill in the login form and subnit it to authenticate. After successful authentication sessionid will be stored in the browser, and you may use the address bar (or any REST-client, for example* `Advanced REST Client for Chrome`_ *) to query the API.*

.. _Advanced REST Client for Chrome: https://chrome.google.com/webstore/detail/ahdjpgllmllekelefacdedbjnjaplfjn/


========================
2. WORKING WITH RAW DATA
========================

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

You may find more information and the original description of NEO® classes here http://packages.python.org/electrophysiology/classes.html

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
Object Type             Data arrays
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
    
    Request: POST /electrophysiology/<object_type>

in order to create a new NEO object. You should specify object attributes, data fields (if exist), and relationships in the request body as a JSON object. For example, send an authorized HTTP POST request to the "/electrophysiology/" with the following body

 ::

    HTTP POST /electrophysiology/segment/

    {
        "name": "Trial 12, Saccade, Stim. 45 grad color red",
        "filedatetime": "10/05/2011",
        "index": 12
    }

to create a new segment. If the response status is 'Created' (201) a client receives a new ID of a segment from the response. Here is the response example:

 ::
    
    {
        "neo_id": "segment_213",
        "index": 1,
        "name": "Trial 12, Saccade, Stim. 45 grad color red",
        "author": "dummy",
        "analogsignalarray": [(0)],
        "filedatetime": "2011-05-12 00:00:00",
        "eventarray": [(0)],
        "irsaanalogsignal": [(0)],
        "epoch": [(0)],
        "epocharray": [(0)],
        "spiketrain": [(0)],
        "spike": [(0)],
        "analogsignal": [(0)],
        "date_created": "2011-09-29 10:42:40.004021",
        "message": "Object created successfully.",
        "logged_in_as": "andrey",
        "message_type": "object_created",
        "event": [(0)],
        "block": null,
        "size": 0
    }


A full set of examples for all supported NEO object can be found here ([wiki:APINEOExamples NEO API Examples]). 

*Note. To understand, which attributes, data fields and relationships are supported for every NEO object please consider Tables 2.1 - 2.4,  ([wiki:APINEOExamples NEO API Examples]) as well as the NEO specification (http://packages.python.org/electrophysiology/classes.html).*

To update the segment, changing some ot its parameters, you need to send an authorized HTTP POST to the same URL providing the ID of the segment at the end of the URL. Assuming the segment we've just created was assigned an ID = 213, send an HTTP POST to the "/electrophysiology/segment/213/" with the following body

 ::
    
    HTTP POST /electrophysiology/segment_213/

    {
        "name": "Trial 12, Saccade, Stim. 45 grad color green",
        "block": "block_1"
    }


to change the color in the name of the segment from "red" to "blue" and link it to the Block with ID 1. When update is required, you may specify only those parameters that you want to update. All other attributes, arrays and relationships will stay as they were.

A response should look similar to this:

 ::
    
    {
        "neo_id": "segment_213",
        "index": 1,
        "name": "Trial 12, Saccade, Stim. 45 grad color green",
        "author": "dummy",
        "analogsignalarray": [(0)],
        "filedatetime": "2011-05-12 00:00:00",
        "eventarray": [(0)],
        "irsaanalogsignal": [(0)],
        "epoch": [(0)],
        "epocharray": [(0)],
        "spiketrain": [(0)],
        "spike": [(0)],
        "analogsignal": [(0)],
        "date_created": "2011-09-29 10:42:40.004021",
        "message": "Object updated successfully. Data changes saved.",
        "logged_in_as": "andrey",
        "message_type": "object_created",
        "event": [(0)],
        "block": null,
        "size": 0
    }


The NEO® model sugest object relationships, like a segment may be connected to a block. Object relationaships help to quickly organize neurophysiological data in the consistent and easily accessible structure. The full list of supported connections can be found in Tables 2.3 - 2.4. Please provide the relationships inside the POST request to save appropriate data structure of your recordings. Consider the examples page ([wiki:APINEOExamples NEO API Examples]).

Some objects have data fields - they are similar to normal attributes, however one should also specify data units to save them (a signal data field of Analog Signal object typically has units "mV", Event object has "time" in "ms" etc). To save or update the associated object data, provide object data in the request body under a corresponding parameter, as shown in examples page ([wiki:APINEOExamples NEO API Examples]). You may find the whole list of data-related objects with corresponding parameters in the table 2.2 above.

*Note. For the moment, the following units types are supported:*
 * in time domain: "s", "ms", "mcs"
 * in signal domain: "v", "mv", "mcv"
 * sampling rate for signals: "hz", "khz", "mhz", "1/s"

---------------------------
2.2 Getting a single object
---------------------------

To get a NEO object with its attributes send a following GET request 

 ::
    
    Request: GET /electrophysiology/<obj_type>/<obj_id>/


You'll get the response, similar to:

 ::
    
    {
        "neo_id": "analogsignal_952",
        "name": "LFP FIX Signal-5",
        "author": "ray",
        "message_type": "retrieved",
        "signal": {
            "units": "mv",
            "data": [
                179.6875,
                172.85156,
                158.20312,
                151.36719,
                ...
                150.87891,
                132.32422,
                122.55859,
                125.48828]
        },
        "t_start": {
            "units": "ms",
            "data": 0
        },
        "recordingchannel": "recordingchannel_13",
        "date_created": "2011-08-22 11:43:56",
        "sampling_rate": {
            "units": "hz",
            "data": 20000
        },
        "message": "Object retrieved successfully.",
        "logged_in_as": "ray",
        "segment": "segment_213",
        "analogsignalarray": null,
        "size": 12000
    }

The response will contain full information about an object, including its attributes, data arrays, downstream and upstream relationships. In the following sections you'll find how to query only part of the information (say, data or relationships, or a slice of the signal, when the signal is very long).

-------------------------
2.3 Partial Data Requests
-------------------------

You may want to request object data or relationaships only (see list of objects and their attributes, data fields and relationaships in the Tables 2.1 - 2.4), or even just get the overall information about an object (like object size) without getting any data or attributes. We support the following parameters for all GET requests for a single object 

 * [q] - can be one of "full", "info", "data", "parents", "children". The response will contain content varying from just generic object information ("info") to the full response ("full").
 * [cascade] - when set to "True" will recursively retrieve all the children objects (not only their IDs). Please be careful with such requests, requesting a big block in a "cascade" mode may result in several Gigobytes of data to download, and significant delays.

Specifically for signal-based objects (Analog Signal, Irregularly Sampled Signal) the following GET request parameters are supported:

 * [start_time] - start time of the required range (calculated using the same time unit as the t_start of the signal)
 * [end_time] - end time of the required range (calculated using the same time unit as the t_start of the signal)
 * [duration] - duration of the required range (calculated using the same time unit as the t_start of the signal)
 * [start_index] - start index of the required datarange (an index of the starting datapoint)
 * [end_index] - end index of the required range (an index of the end datapoint)
 * [samples_count] - number of points of the required range (an index of the end datapoint)
 * [downsample] - number of datapoints. This parameter is used to indicate whether downsampling is needed. The downsampling is applied on top of the selected data range using other parameters (if specified).

*Note. Some reasonable combinations of these parameters (like ' ' or ' ' will return a correct response. Using redundant number of parameters will lead to their disregard, useless combinations may throw a 400 bad request.*

For example, send the following GET request

 ::
    
    Request: GET /electrophysiology/analogsignal/11/?start_index=20&end_index=100&downsample=10


to get the Analog Signal range with datapoints as of 30 to 100, downsampled from 71 points to 10:

 ::
    
    {
        "neo_id": "analogsignal_952",
        "name": "LFP FIX Signal-5",
        "author": "ray",
        "message_type": "retrieved",
        "signal": {
        "units": "mv",
        "data": [
            68.57667133722775,
            77.09840224197298,
            43.238698121320084,
            17.58256832247581,
            -2.4392013587548016,
            -2.2720730942678244,
            -13.626765036880082,
            40.225247238106235,
            85.47298589746826,
            52.89693899799824]
        },
        "t_start": {
            "units": "ms",
            "data": 1.5
        },
        "recordingchannel": "recordingchannel_13",
        "date_created": "2011-08-22 11:43:56",
        "sampling_rate": {
            "units": "hz",
            "data": 20000
        },
        "message": "Object retrieved successfully.",
        "logged_in_as": "ray",
        "segment": "segment_190",
        "analogsignalarray": null,
        "size": 12000
    }


Basically, for Analog Signal the range, provided in the request parameters, is just two sequential numbers, between which the requested data array will be retrieved. However, for Irregularly Sampled Analog Signal, this will work in a slightly different way. The range requested will be calculated using "times" array, attached to the object.

When a SpikeTrain is requested, that range of waveforms (if defined) will be retrieved, which fall into the requested range. The comparison is made matching each waveform to the time, provided in the time array.


'''2.4 Getting object relationships only'''

According to the NEO data model, all objects in the scheme are connected. You may get the both "parent-to-child" (like which Segments are contained in a Block) and "child-to-parent" (like from which Recording Point an Analog Signal was recorded) relations by sending request to /electrophysiology/children/<neo_id>/ or /electrophysiology/parents/<neo_id>/ accordingly. In the tables below you may find supported objects of either type:



For example, to request a list of parents for Analog Signal, send the following GET request

 ::
    
Request: GET /electrophysiology/parents/analogsignal_34


A response should look similar to this:

 ::
    
[{
    "neo_id": analogsignal_34, 
    "segment": "segment_1",
    "recordingchannel": "recordingchannel_12",
    "message": "Here is the list of object parents."
}]


Accordingly, if you want to receive a contents of the certain block, send an HTTP request to "/electrophysiology/children/block_87" (assuming block has id=87). If the block contains both segments and recording channel groups, you'll get the following response:

 ::
    
[{
    "neo_id": "block_87", 
    "segment": [
        "segment_193",
        "segment_194",
        "segment_195"
    ],
    "recordingchannelgroup": [
        "recordingchannelgroup_1",
        "recordingchannelgroup_2",
    ],
    "message": "Here are the contents of the requested object."
}]


''Note. When some of the parents / children are not set up, an empty string / list will appear in the response.''
''Note. While updating objects, please use only child-to-parent relationships (like specify to which segment (parent) particular event (child)  belongs). Parent-to-child relations will not be parsed and saved.''


'''2.5 Query data objects - get an object list'''

Use the following HTTP GET 

 ::
    
Request: GET /electrophysiology/select/<object_type>/?params


to query NEO objects of a specific type. For example, if you want to get all Analog Signals available for a specific user, send the following request 

 ::
    
Request: GET /electrophysiology/select/analogsignal/


You receive a list of Analog Signals IDs as a response:

 ::
    
[{
    "selected": [
        "analogsignal_10",
        "analogsignal_11", 
        "analogsignal_12", 
        "analogsignal_13", 
        "analogsignal_14", 
        "analogsignal_15", 
        "analogsignal_16", 
        "analogsignal_17", 
        "analogsignal_18", 
        "analogsignal_19", 
        "analogsignal_20", 
        "analogsignal_21"
    ]
    "object_total": 21,
    "object_selected": 12,
    "selected_as_of": 10,
    "message": "Here is the list of requested objects."
}]


By default the API will return the first 1000 data objects in the response. This saves bandwidth and improves performance. A response will contain "object_total" parameter, with the total number of available objects. Use the "range_start" parameter indicating the starting point of the range of the whole list of objects (for example, 0 or 1499) to retrieve the rest of the objects. Just send a "range_start" parameter in the GET request, like 

 ::
    
Request: GET /electrophysiology/select/analogsignals/?range_start=844



'''2.6 Tagging raw data objects with metadata'''

 ::
    
Request: POST /electrophysiology/assign/<obj_id>/?params


parameters:
 * obj_id - an object to assign a tag (Section, AnaogSignal etc.)
 * value_id - a value from the values of properties, defined up the hierarchy of the section tree you work in.

''Note. For example, you have an experiment with stimulus, changing its color across trials. You have several Analog Signals recorded, and you want to indicate, which of those were recorded under which stimulus condition. Assume in the experiment section tree you already defined a property, say, "StimulusColor" with values "red, green, blue". In that case, you use this function (being described) to "tag" all required Analog Signals with appropriate metadata value, assigning a "red" value to, let's say, first five hundred Analog Signals, "green" - to the second five hundred time series etc.''


 ::
    
Response:
TBD




'''2.7 Computations with specified objects'''

 ::
    
Request: POST /electrophysiology/<operation>/?params


parameters:
 * operation - an operation to perform
 * object_type - analogsignal or spiketrain
 * object_list - a tuple of object ids
 * [query_string] - if defined, a query string will be used to select object for computation. object_list will be ignored.
 * datafile_id - where to save the results


 ::
    
Response:
TBD



'''2.8 Delete an object'''

 ::
    
Request: DELETE /electrophysiology/?params


parameters:
 * obj_type - type of the object
 * obj_id - object id


 ::
    
Response:
TBD




