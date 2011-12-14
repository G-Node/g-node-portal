.. _examples:

==========================
G-Node API Object Examples
==========================

Here you may find a list of examples of HTTP request bodies for all supported NEO objects. All examples assume creating a new object. If some of the required parameters to create an object are missing, the system will respond with an error. You may find a list of required parameters (attributes, data parameters and relationships) in the section 6 of the specification in tables 6.1 - 6.4.

To perform an update, you need to provide an ID of an object (e.g. 12345 for "segment_12345") in the end of the URL, like "/neo/segment/12345/". While requesting an update, only parameters that are proposed for an update can be provided.

This is an example request to create a new Block:

 ::

    HTTP POST /electrophysiology/block/

    {
        "name": "Block of recordings from May, 10",
        "filedatetime": "2011-10-05",
        "index": 1
    }

Send the following HTTP POST request to create a new Segment and connect it to the Block with NEO_ID "block_1234":

 ::

    HTTP POST /electrophysiology/segment/

    {
        "name": "Trial 1",
        "filedatetime": "2011-10-07",
        "index": 1,
        "block": "block_1234"
    }

You may create a new Event by sending the following HTTP POST:

 ::

    HTTP POST /electrophysiology/event/

    {
        "time": {
            "data": 7.22,
            "units": "ms"
        },
        "label": "Stimuli end",
        "segment": "segment_2"
    }

In case you want to organize some Events in EventArray, create this EventArray first (in this example it will be linked to "segment_12345"):

 ::

    HTTP POST /electrophysiology/eventarray

    {
        "segment": "segment_12345"
    }

and then create these Events, indicating corresponding EventArray? as a parent, similar to how it was shown in the previous example.

If some of your Events exist already, you may send an update request for these Events, indicating appropriate EventArray as a parent, if you need to include them in the EventArray. Use the following example:

 ::

    HTTP POST /electrophysiology/event/456/

    {
        "eventarray": "eventarray_897"
    }

In some cases, instead of creating Events, you may find useful Epoch objects. Use HTTP POST request, similar to the following, to create new Epoch:

 ::

    HTTP POST /electrophysiology/epoch/

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
        "segment": "segment_5"
    }

As with EventArray, you may want to organize your Epochs in an EpochArray. You may create one first with the request, similar to

 ::

    HTTP POST /electrophysiology/epocharray/

    {
        "segment": "segment_1234"
    }

and then create Epochs, indicating newly created EpochArray? as a parent. If your Epochs exist already, update them, indicating EpochArray? as a parent (see example with Events above).

If you use Tethrodes or any other type of "groups" of electrodes within your electrophysiological recording, you may want to consider creating a Recording Channel Group:

 ::

    HTTP POST /electrophysiology/recordingchannelgroup/

    {
        "name": "Tethrode #5",
        "block": "block_12"
    }

with several electrodes (Recording Channel) which you may link to it further, like shown below.

To create a Recording Channel, please send a request similar to (if you want to link it to the Tethrode (Recording Channel Group), indicate it with the "recordingchannelgroup" parameter):

 ::

    HTTP POST /electrophysiology/recordingchannel/

    {
        "name": "Electrode #1",
        "index": 1,
        "recordingchannelgroup": "recordingchannelgroup_1569"
    }

To create a Unit (usually representing a Neuron within the data model) please send an HTTP request, similar to:

 ::

    HTTP POST /electrophysiology/unit/

    {
        "obj_type": "unit",
        "name": "Neuron 34.56 x 28.8 x 245.69",
        "recordingchannel": [
            "recordingchannel_67",
            "recordingchannel_68",
            "recordingchannel_69"
        ]
    }

Please notice, that in the case with Unit, you may specify several Recording Channels. Even if you need to relate Unit to only one channel, please use a list "[ ... ]" with only one value inside.

Please use the following HTTP request example to create AnalogSignal:

::

    HTTP POST /electrophysiology/analogsignal/

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
            "data": [12.2, 12.7, 19.0, 7.81, 3.42, 9.28, -5.86]
        },
        "segment": "segment_1234",
        "recordingchannel": "recordingchannel_69"
    }

Please note, for any "array"-type parameter you need to provide a dictionnary of parameters - "units" and "data".

If you have irregularly sampled signals, use the following request example to create IrSaAnalogSignal objects:

 ::

    HTTP POST /electrophysiology/irsaanalogsignal/

    {
        "name": "ISAS-1",
        "t_start": {
            "data": -200.0,
            "units": "ms"
        },
        "signal": {
            "units": "mV", 
            "data": [12.2, 12.7, 19.0, 7.81, 3.42, 9.28, -5.86]
        },
        "times": {
            "units": "ms", 
            "data": [155.0, 158.0, 160.0, 161.0, 162.0, 165.0, 168.0]
        },
        "segment": "segment_1234",
        "recordingchannel": "recordingchannel_69"
    }

If you need to organize several Analog Signals in an array, first create AnalogSignalArray:

 ::

    HTTP POST /electrophysiology/analogsignalarray/

    {
        "segment": "segment_1234"
    }

and futher link required Analog Signals to it (you have to update every Analog Signal to include it in the array, if they already exist). See an example with EventArray above.

This is an example request to create a single Spike:

 ::

    HTTP POST /electrophysiology/spike/

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
        "waveforms": [{
                "channel_index": 0,
                "waveform": {
                    "units": "mV", 
                    "data": [5.86, -1.46, -0.488, -7.32, -9.77, -12.7, -12.7]
                }
            },
            {
                "channel_index": 1,
                "waveform": {
                    "units": "mV", 
                    "data": [45.86, -31.46, -0.338, 67.32, -19.77, -109.7, -39.7]
                }
        }],
        "segment": "segment_1234",
        "unit": "unit_22"
    }

And this is an example for a SpikeTrain:

 ::

    HTTP POST /electrophysiology/spiketrain/

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
            "data": [-4.88, 3.42, 2.44]
        },
        "waveforms": [{
                "channel_index": 0,
                "time_of_spike": {
                    "units": "ms",
                    "data": 469.1
                },
                "waveform": {
                    "units": "mV", 
                    "data": [5.86, -1.46, -0.488, -7.32, -9.77, -12.7, -12.7]
                }
            },
            {
                "channel_index": 1,
                "time_of_spike": {
                    "units": "ms",
                    "data": 789.4
                },
                "waveform": {
                    "units": "mV", 
                    "data": [45.86, -31.46, -0.338, 67.32, -19.77, -109.7, -39.7]
                }
        }],
        "segment": "segment_1234",
        "unit": "unit_22"
    }

Please note, that you may provide a set of waveforms inside a waveform parameter, as shown in this example.
