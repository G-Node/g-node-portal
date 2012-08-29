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

**Section**
    A container of other sections, properties or datafiles. It could be also named folder. These are basically used to "label" your datafiles with a variety of tags (like in GMail), with a difference that you can manage sections (tags) to organize them in a tree-like structure. The Section® is a prototype of the odML section (see http://www.g-node.org/projects/odml) and is implemented inline with odML concepts and methodology.

**Property**
    A property is a flexible way to annotate your data (implemented in line with odML). Within these could be a model of your recording device, duration of the stimulus, a layer of the cell you record from. Some properties are provided by default when you create a section of either type. You just need to set values, appropriate for your research.

**NEO Raw Data Object**
    NEO® objects are useful when working with the raw data. NEO objects include Segments, Analog Signals, Spike Trains etc. (see http://packages.python.org/neo/api_reference.html) It is a flexible data structure, compartible with several well-known data-formats, and useful for data analysis. You may convert your files to the NEO (native G-Node) structure using API, when the file is convertible (currently supported information extraction from neuroshare-compliant formats, http://neuroshare.sourceforge.net/index.shtml).

**Label**
    One of the most important concepts in this context. A Label links Raw Data Objects with Property values. For example, when I want to indicate that a specific set of Analog Signals was recorded when the stimuli, shown to the subject, had a specific color (e.g. red), I can create a Property called "Stimuli Colors" with color values ("red" and maybe some more) and then for every Analog Signal I create a label, which links the required value ("red") to the signal. Thus all selected signals become "labeled" with a specific stimuli color ("red"). This labeling can be done in bulk mode, please consider section 2.5 of this document.

**Resource**
    Any item maintained by the API, including sections, datafiles, and NEO objects.

**Resource ID**
    A unique identifier for each resource. This is usually of the format <type>_<id>, e.g. section_12345. Throughout this specification, if not specified, a typed resource ID should be assumed.

**Untyped resource ID**
    A resource ID, without the type prefix. Given the previous example, an untyped resource ID would be 12345.


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

*Note. If you want to just TRY the API from the usual web-browser (like Firefox, Chrome, Safari etc.), just go to the login page (* https://portal.g-node.org/data/account/login/ *), fill in the login form and subnit it to authenticate. After successful authentication sessionid will be stored in the browser, and you may use the address bar (or any REST-client, for example* `Advanced REST Client for Chrome`_ *) to query the API.*

.. _Advanced REST Client for Chrome: https://chrome.google.com/webstore/detail/ahdjpgllmllekelefacdedbjnjaplfjn/


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

    HTTP CREATED (201)
    
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


A full set of examples for all supported NEO object can be found here (:ref:`api_object_examples`). 

*Note. To understand, which attributes, data fields and relationships are supported for every NEO object please consider Tables 2.1 - 2.4, as well as the NEO specification* (http://packages.python.org/neo/api_reference.html).

To update the segment, changing some ot its parameters, you need to send an authorized HTTP POST to the same URL providing the ID of the segment at the end of the URL. Assuming the segment we've just created was assigned an ID = 213, send an HTTP POST to the "/electrophysiology/segment/213/" with the following body

 ::
    
    HTTP POST /electrophysiology/segment/213/

    {
        "name": "Trial 12, Saccade, Stim. 45 grad color green",
        "block": "block_1"
    }


to change the name of the segment and link it to the Block with ID 1. When update is required, you may specify only those parameters that you want to update. All other attributes, arrays and relationships will stay as they were.

A response should look similar to this:

 ::

    HTTP SUCCESS (200)
    
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
        "message_type": "object updated successfully",
        "event": [(0)],
        "block": null,
        "size": 0
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

---------------------
Partial Data Requests
---------------------

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

*Note. Some reasonable combinations of these parameters (like 'start_time' and 'duration' or 'start_index' and 'end_time' will return a correct response. Using redundant number of parameters will lead to their disregard, useless combinations may throw a 400 bad request.*

For example, send the following GET request

 ::
    
    Request: GET /electrophysiology/analogsignal/11/?start_index=30&end_index=100&downsample=10


to get the Analog Signal range with datapoints as of 30 to 100, downsampled from 71 points to 10:

 ::

    HTTP SUCCESS (200)
    
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

Notice that the "t_start" data field in the response has a data value of 1.5, indicating the start of the retrieved signal.

-------------------------
Getting a List of Objects
-------------------------

Use the following HTTP GET 

 ::
    
    Request: GET /electrophysiology/<object_type>/?params


to query NEO objects of a specific type. For example, if you want to get all Analog Signals available for a specific user, send the following request 

 ::
    
    GET /electrophysiology/analogsignal/


You receive a list of Analog Signals IDs as a response:

 ::
    
    {
        "object_selected": 10,
        "message_type": "object_selected",
        "object_total": 10,
        "selected": [
            "analogsignal_12", 
            "analogsignal_13", 
            "analogsignal_14", 
            "analogsignal_15", 
            "analogsignal_16", 
            "analogsignal_17", 
            "analogsignal_18", 
            "analogsignal_19", 
            "analogsignal_20", 
            "analogsignal_21"]
        "message": "Here is the list of requested objects.",
        "logged_in_as": "ray",
        "selected_as_of": 0
    }


By default the API will return the first 1000 data objects in the response. This saves bandwidth and improves performance. A response will contain "object_total" parameter, with the total number of available objects. The feature to request more than a 1000 objects is coming soon! You'll be able to use the "range_start" parameter indicating the starting point of the range of the whole list of objects (for example, 0 or 1499) to retrieve the rest of the objects. Just send a "range_start" parameter in the GET request, like 

 ::
    
    GET /electrophysiology/analogsignals/?range_start=844

to get the rest of the objects.


-----------------------------------------------------
Labeling raw data objects with metadata (coming soon)
-----------------------------------------------------

You may label several Raw Data objects with a particular value by using the following request:

 ::
    
    POST /labels/

    {
        "value_id": "value_1345", # some value, from properties
        "selection_id": "selection_id", # you may apply a label to the selection
        "apply_to": [
            "segment_124",
            "segment_125"
        ]
    }

*Note. For example, you have an experiment with stimulus, changing its color across trials. You have several Analog Signals recorded, and you want to indicate (or 'group' them), which of those were recorded under which stimulus condition. Assume in the experiment section tree you already defined a property, say, "StimulusColor" with values "red, green, blue". In that case, you use this function to "label" all required Analog Signals with appropriate metadata value, assigning a "red" value to, let's say, first five hundred Analog Signals, "green" - to the second five hundred time series etc.*

---------------------------
Remove labels (coming soon)
---------------------------

You may completely remove a particular label:

 ::

    DELETE /labels/<label_id>/

You may remove a particular label from one or several objects (providing a selection or a list of object IDs):

 ::
    
    DELETE /labels/<label_id>/

    {
        "selection_id": "selection_id", # you may apply a label to the selection
        "apply_to": [
            "segment_124",
            "segment_125"
        ]
    }


===================
METADATA MANAGEMENT
===================

You may organize your experimental data using odML Sections and Properties. They are useful elements to created flexible nested structures, containing key-value pairs, aimed to help scientists to describe their experiments and annotate recorded data. Here are the actions available for you to manage your experimental metadata.

------------------------
Getting list of sections
------------------------

 ::
    
    GET /metadata/sections/

parameters:
 * [recursive] - true, false (default) - do a recursive export
 * [section_id] - sections in a specific section (root by default)
 * [visibility] - private, public, shared, all (default) - which types of sections to return
 * [owner] - filter by an owner of the file
 * [created_min] - filters files older than created_min
 * [created_max] - filters files younger than created_max
 * [q] - full-text query string (also searches in parent section?)
 * [max-results] - maximum number of results to be retrieved (default is 1000, provide this parameter if you need to query more).

 ::
    
    Response:
    TBD

-------------------------------------
Section: create, update, copy or move
-------------------------------------

 ::
    
    Request: POST /metadata/sections/?params

    { 
        "title": "Stimuli",
        "description": "This section describes the stimulus",
        "type": "stimuli",
        "link": "",
        "repository": "",
        "mapping": "",
        "reference": "",
        "parent_section": "section_1234",
    }

if you need to create a section from the template (or from other section), or you want to move the section to another place in the metadata tree - provide the parameters below:

 * source_section_id - section to copy
 * target_section_id - a section to copy into
 * [mode] - move (default), copy - whether to move or copy
 * [with_properties] - whether or not to copy properties, which belong to the section (in copy mode)
 * [with_datafiles] - whether or not to copy links (!!) to datafiles, which belong to the section (in copy mode).
 * [recursive] - true, false - copy all subsections recursively (in copy mode)

*Note. Attributes of the section, provided in the request body, will be used as values for the newly created section. In case of copy/move, the request body can be empty or contain only required parameters.*

*Note. Files, contained in the section, are never duplicated while using copy/move function. However, in the newly created section you will see the same files: these are only links to the files, which were successfully copied.*


 ::
    
    Response:
    TBD



*Note. ACL for the file is by default assigned as for the parent section.*

 ::
    
    Response:
    TBD


-----------------------
Getting section details
-----------------------

 ::
    
    GET /metadata/sections/<section_id>


 ::
    
    Response:
    TBD


--------------
Delete section
--------------

 ::

    DELETE /metadata/sections/<section_id>/?params


parameters:
 * [force] - true, false (default) - use "true" to delete the file even if there are other users with collaboration role (able to write). If "false" or omitted, the file will not be deleted being in the state having collaborators.


 ::
    
    Response:
    TBD


--------------------------
Getting list of properties
--------------------------

 ::
    
    GET /metadata/properties/

parameters:
 * [section_id] - properties in a specific section (all by default)
 * [q] - full-text query string (searches both properties and values)
 * [max-results] - maximum number of results to be retrieved (default is 1000, provide this parameter if you need to query more).


 ::
    
    Response:
    TBD


---------------------------
Create or update a property
---------------------------

 ::
    
    Request: POST /metadata/properties/<property_id>/

    { 
        "section_id": "section_1234",
        "description": "This section describes the stimulus",
        "name": "stimuli colors",
        "type": "experimental conditions",
        "unit": "",
        "definition": "",
        "mapping": "",
        "dependency": "",
    }


 ::
    
    Response:
    TBD


------------------
Getting a property
------------------

 ::
    
    GET /metadata/properties/<property_id>


 ::
    
    Response:
    TBD

---------------
Delete property
---------------

 ::
    
    DELETE /metadata/properties/<property_id>


 ::
    
    Response:
    TBD

===============
FILE MANAGEMENT
===============

-------------------------
Getting list of datafiles
-------------------------

 ::

    Request: GET /datafiles/?params

parameters:
 * [section_id] - return files only in a specific section (all files if not provided)
 * [visibility] - private, public, shared, all (default) - which types of files to return
 * [owner] - filter by an owner of the file
 * [created_min] - filters files older than created_min
 * [created_max] - filters files younger than created_max
 * [q] - full-text query string (also searches in parent section?)
 * [max-results] - maximum number of results to be retrieved (default is 1000, provide this parameter if you need to query more).

Typically you should get the following response:

 ::

    Response:

    TBD

---------------------------
Getting single file details
---------------------------
 ::
    
    Request: GET /datafiles/<datafile_id>


 ::
    
    Response:
    TBD


(Conversion status (not able to convert, not converted, NEO))
(if convertable, provide additional statistics)


----------------------------------------------
Upload a datafile (with or without conversion)
----------------------------------------------

 ::
    
    Request: POST /datafiles/?params

    {
        "name": "In-vivo single-channel recordings, V1",
        "description": "Some description here",
        "keywords": "monkey V1 single-channel"
    }

parameters:
 * [section_id] - provide an ID of the section in which to store the file (recommended).
 * [convert] - true (default), false - whether try to convert the file into native format, if possible. For the moment the following types are supported: TBD.

*Note. If the file is uploaded into a specific section, the security settings for the new file will be assigned as for the parent section. When no section is specified, the file is private by default.*

 ::
    
    Response:

    HTTP CREATED (201)

    TBD

--------------------------
Modify datafile attributes
--------------------------

 ::
    
    Request: POST /datafiles/<datafile_id>/?params

    {
        "description": "Some REAL description goes here..",
    }

parameters:
 * [convert] - provide a convert=true parameter to initiate file conversion. The file should be convertible and never converted previously.

*Note. The conversion operation is asynchronous, which means you'll not see the status of the conversion immediately in the response. You may use a details request (2.2) to check whether the file was converted successfully or not.*

 ::
    
    Response:
    TBD

---------------
Export datafile
---------------

When the file is not converted, it simply returns the originally uploaded file for download. When file is in native format, it can convert all underlying data into a file with the format specified and return it for download.

 ::
    
    Request: GET /datafiles/<datafile_id>/binary/?params


parameters:
 * [format] - required file format. The following formats are supported: HDF5. Leave this empty to download an original file.

 ::
    
    Response:
    TBD

---------------
Delete datafile
---------------

 ::
    
    Request: DELETE /datafiles/<datafile_id>/?params


parameters:
 * [force] - true, false (default) - use "true" to delete the file even if there are other users with access to the file. If "false" or omitted, the file will not be deleted being in the state having collaborators.

 ::
    
    Response:
    TBD


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
    
    POST /acl/?params

    {
        "resources": [
            "section_1345",
            "section_1346",
            "datafile_1345"
        ]
        "state": "<access level>", # can be public, friendly, private - access level
        "shared_with": {
            "<iser_id>": "<user_role>", # a role can be "reader" or "collaborator"
            "<iser_id>": "<user_role>"
        }
    }

parameters:
 * [recursive] - true, false (default) - apply to all resources recursivery. 

*Note. The requested ACL changes will be applied to the resources in a sequence, as they were provided in the request.*

 ::
    
    Response:
    TBD

-----------------------------
Remove a user from ALL shares
-----------------------------

Not implemented yet.


===========================
QUERY DATA USING G-NODE API
===========================

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
