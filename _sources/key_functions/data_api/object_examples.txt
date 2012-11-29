================================
Object specs and examples (JSON)
================================

--------------------
JSON attribute rules
--------------------

For every object, available in :doc:`our object model <../terminology>`, we use the following rules to build its JSON representation:

=============================   ==========================
Model type                      JSON representation
=============================   ==========================
int                             <integer>
string                          "<string>"
datetime                        "YYYY-MM-DD HH:MM:SS"
float + units                   { "data": <float>, "units": "<string>" }
array of strings 1D             { "data": "<file_permalink>", "units": "dt" }
array of floats 1D + units      { "data": "<file_permalink>", "units": "<string>" }
array of floats 2D + units      { "data": "<file_permalink>", "units": "<string>" }
array of floats 3D + units      { "data": "<file_permalink>", "units": "<string>" }
foreign key                     "<parent_obj_permalink>"
=============================   ==========================

*Note. Available units values are* :ref:`listed here <available_units>`.

------------------
POST body examples
------------------

Here you may find a list of examples of HTTP request bodies for all supported objects. All examples provide an example POST request body to create a new object. 

^^^^^^^^^^^^^^^^^
Electrophysiology
^^^^^^^^^^^^^^^^^

This is an example request to create a new :ref:`Block <Block>`:

 ::

    Request: POST /electrophysiology/block/

    {
        "name": "Block of recordings from May, 10",
        "filedatetime": "2012-09-17 18:08:15",
        "index": 1
    }

Send the following HTTP POST request to create a new :ref:`Segment <Segment>` and connect it to the :ref:`Block <Block>` with an ID = 1234:

 ::

    Request: POST /electrophysiology/segment/

    {
        "name": "Trial 1",
        "filedatetime": "2012-09-17 18:08:15",
        "index": 1,
        "block": 1234
    }

You may create a new :ref:`Event <Event>` by sending the following HTTP POST:

 ::

    Request: POST /electrophysiology/event/

    {
        "time": {
            "data": 7.22,
            "units": "ms"
        },
        "label": "Stimuli end",
        "segment": 1,
        "eventarray": 1
    }

In case you want to create an array of Events, first :ref:`upload one file with labels and another file with times <create_array_routine>` and then create an :ref:`EventArray <Event>` object (in this example it will be linked to the :ref:`Segment <Segment>` 3847):

 ::

    Request: POST /electrophysiology/eventarray/

    {
        "segment": 3847,
        "times": {
            "data": "/datafiles/158867/",
            "units": "ms"
        },
        "labels": {
            "data": "/datafiles/158868/",
            "units": "ms"
        }
    }


In some cases, instead of creating Events, you may find useful :ref:`Epoch <Epoch>` objects. Use HTTP POST request, similar to the following, to create new :ref:`Epoch <Epoch>`:

 ::

    Request: POST /electrophysiology/epoch/

    {
        "time": {
            "data": 78.22,
            "units": "ms"
        },
        "duration": {
            "data": 0.35,
            "units": "ms"
        },
        "label": "Displaying blue screen",
        "segment": 3847,
    }

As with :ref:`EventArray <Event>`, you may want to organize several Epochs in an :ref:`EpochArray <Epoch>`. In this case you should create 3 arrays (for times, durations and labels) first, and then create the :ref:`EpochArray <Epoch>` object with the request, similar to

 ::

    Request: POST /electrophysiology/epocharray/

    {
        "segment": 1234,
        "times": {
            "data": "/datafiles/159690/",
            "units": "ms"
        },
        "durations": {
            "data": "/datafiles/159691/",
            "units": "ms"
        },
        "labels": {
            "data": "/datafiles/159692/",
            "units": "ms"
        }
    }


If you use Tethrodes or any other type of "groups" of electrodes within your electrophysiological recording, you may want to consider creating a :ref:`RecordingChannelGroup <RecordingChannelGroup>`:

 ::

    Request: POST /electrophysiology/recordingchannelgroup/

    {
        "name": "Tethrode #5",
        "block": 12
    }


To create a :ref:`RecordingChannel <RecordingChannel>`, please send a request similar to (if you want to link it to the Tethrode (:ref:`RecordingChannelGroup <RecordingChannelGroup>`), indicate it with the "recordingchannelgroup" parameter):

 ::

    Request: POST /electrophysiology/recordingchannel/

    {
        "name": "Electrode #1",
        "index": 1,
        "recordingchannelgroup": 18
    }

To create a :ref:`Unit <Unit>` (usually representing a Neuron within the data model) please send an HTTP request, similar to:

 ::

    Request: POST /electrophysiology/unit/

    {
        "name": "Neuron 34.56 x 28.8 x 245.69",
        "recordingchannel": [
            67,
            68,
            69
        ]
    }

Please notice, that in the case with :ref:`Unit <Unit>`, you may specify several Recording Channels. Even if you need to relate :ref:`Unit <Unit>` to only one channel, please use a list "[ ... ]" with only one value inside. You may use :ref:`permalinks or just channel IDs <manage_relations>`, as per your convenience.

Please use the following HTTP request example to create :ref:`AnalogSignal <AnalogSignal>`:

::

    Request: POST /electrophysiology/analogsignal/

    {
        "name": "AS-1",
        "sampling_rate": {
            "data": 20000,
            "units": "Hz"
        },
        "t_start": {
            "data": 0.0,
            "units": "ms"
        },
        "signal": {
            "units": "mV", 
            "data": "/datafiles/167890/"
        },
        "segment": 1234,
        "recordingchannel": 1
    }

Please note, the array with data ("/datafiles/167890/") should be uploaded and validated :ref:`prior to the object creation <create_array_routine>` (this request).

If you have irregularly sampled signals, use the following request example to create :ref:`IrSaAnalogSignal <IrSaAnalogSignal>` objects:

 ::

    Request: POST /electrophysiology/irsaanalogsignal/

    {
        "name": "ISAS-1",
        "t_start": {
            "data": -200.0,
            "units": "ms"
        },
        "signal": {
            "units": "mV", 
            "data": "/datafiles/169890/"
        },
        "times": {
            "units": "ms", 
            "data": "/datafiles/169891/"
        },
        "segment": 1234,
        "recordingchannel": 1
    }

As with all other data-related objects, arrays with data ("/datafiles/167890/") and times ("/datafiles/169891/") should be uploaded and validated :ref:`prior to the object creation <create_array_routine>` (this request).

By analogy with :ref:`AnalogSignal <AnalogSignal>`, use the following request to create :ref:`AnalogSignalArray <AnalogSignalArray>` (:ref:`upload array data first <create_array_routine>`):

 ::

    Request: POST /electrophysiology/analogsignalarray/

    {
        "sampling_rate": {
            "data": 20000,
            "units": "Hz"
        },
        "t_start": {
            "data": 0.0,
            "units": "ms"
        },
        "signal": {
            "units": "mV", 
            "data": "/datafiles/180138/"
        },
        "segment": 1234,
        "recordingchannel": 1
    }


This is an example request to create a single :ref:`Spike <Spike>` (:ref:`upload waveform data first <create_array_routine>`):

 ::

    Request: POST /electrophysiology/spike/

    {
        "time": {
            "data": 300.0,
            "units": "ms"
        },
        "sampling_rate": {
            "data": 20.0,
            "units": "kHz"
        },
        "left_sweep": {
            "data": 15.0,
            "units": "ms"
        },
        "waveform": {
            "data": "/datafiles/180148/",
            "units": "ms"
        },
        "segment": 1234,
        "unit": 56
    }

And this is an example for a :ref:`SpikeTrain <SpikeTrain>`:

 ::

    Request: POST /electrophysiology/spiketrain/

    {
        "t_start": {
            "data": -400.0,
            "units": "ms"
        },
        "t_stop": {
            "data": 800.0,
            "units": "ms"
        },
        "times": {
            "units": "ms", 
            "data": "/datafiles/190388/",
        },
        "segment": 1234,
        "unit": 56
    }


^^^^^^^^
Metadata
^^^^^^^^

Send the following HTTP POST request to create a new :ref:`Section <Section>`:

 ::

    Request: POST /metadata/section/

    {
        "name": "Electrode",
        "tree_position": 1,
        "odml_type": 0,
        "safety_level": 3,
        "description": "Settings and other experimental setup"
    }


Create new :ref:`Property <Property>` inside the :ref:`Section <Section>` with ID 1456 with the following request:

 ::

    Request: POST /metadata/property/

    {
        "comment": "insert some comments here..",
        "definition": "the type of the recording",
        "name": "recording type",
        "dependency_value": "",
        "dtype": "",
        "uncertainty": "",
        "mapping": "",
        "dependency": "",
        "safety_level": 3,
        "unit": ""
        "section": 1456,
    }

This request is used to create new :ref:`Value <Value>`:

 ::

    Request: POST /metadata/value/

    {
        "parent_property": 3,
        "data": "extracellular"
    }


^^^^^^^^^
Datafiles
^^^^^^^^^

To upload a file you should send an HTTP POST request with the file attached with the key "raw_file" in the request body. Read more about :doc:`working with files here <files>`.

