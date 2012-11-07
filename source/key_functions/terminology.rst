=============================
Key concepts and Object model
=============================

Throughout this entire documentation, it's refered to a series of terms like Section_ or Datafile_, which are intended to have specific meanings.

.. _common_terms:

---------
permalink
---------

    A permanent link to an object, similar to the URL. Every object in this context has a permalink. For example, a Datafile_ could have a permalink like https://portal.g-node.org/data/datafiles/15

-----------------
object identifier
-----------------

    Every object has two IDs: a "local" ID - an integer number, unique within the object type namespace (like there is only one Event_ with ID 15 but there could be other objects, say, a Segment_ with id 15 too), which is used in permalinks to access an object, and a global ID (guid) - a 40 symbols hash code (like c30fb5dee94e44930abc572e1b3e4847a0f51dd3), unique across all objects.


.. _`ePhys Objects`:

-----------------------------------
electrophysiology ( ePhys Objects )
-----------------------------------

    ePhys Objects are used to represent the recorded (raw) electrophysiological data in a flexible, but consistent way. ePhys Objects directly represent raw data structure with data as arrays of numerical values with associated mandatory attributes (units, sampling frequency, etc.). An original concept is described `here <http://neo.readthedocs.org/en/latest/core.html>`_, however we provide all descriptions down below for convenience. A good example :ref:`is described in the overview <overview_ephys>`. The following raw data objects are supported:

 * AnalogSignal_
 * IrSaAnalogSignal_
 * AnalogSignalArray_
 * SpikeTrain_
 * Spike_
 * Event_
 * EventArray_
 * Epoch_
 * EpochArray_
 * Block_
 * Segment_
 * Unit_
 * RecordingChannelGroup_
 * RecordingChannel_

Every object serves a specific purpose to organize your electrophysiological data. 'Block's mainly represent one experiment (or the whole data, recorded within one experiment). 'Segment' represents an experimental trial, a "time frame" within an experiment with unique experimental conditions. Blocks may contain several segments, like an experiment consists of several trials. A 'Segment' may contain 'AnalogSignal's, 'Event's or 'Epoch's, which are used to organize recorded signals, single time events, or events with duration inside one experimental time-frame. Segment may also contain 'SpikeTrain' objects to accommodate the spike data. For every 'Block' one may define a set of 'RecordingChannel's according to the experimental setup. These channels can be organized under a 'RecordingChannelGroup', to keep track of tethrodes. 'AnalogSignals' can be linked to the appropriate 'RecordingChannel's to make the dataset consistent.

The overall data model for electrophysiology looks like this:

.. image:: ../_static/ephys_om.png
    :width: 800 px
    :align: center

Every object in this context has a set of *attributes* and *data fields*, it may also have relationships, like *parents* and *children*. For example, a segment has to have an attribute 'name'. 'AnalogSignal' should have a 'sampling_rate' data field, which consists of the unit (like Hz or KHz) and a value (like 20000). A 'Block' consists of 'Segments', which means the 'Block' has a child 'Segment', and a 'Segment' has a parent 'Block'. Below for each object you can find its definition, as well as the detailed listing of its attributes and relationships.

.. _available_units:

*Note. Some attributes have units. Here is the list of available units values:*

 * in the time domain:
   * "s", "ms", "us"
 * in the signal domain:
   * "V", "mV", "uV"
 * in the signal sampling domain:
   * "Hz", "KHz", "MHz", "1/s"

*Note. Attributes with \* asterisk are mandatory.*

`ePhys Objects`_ consist of:

.. _AnalogSignal:

^^^^^^^^^^^^
AnalogSignal
^^^^^^^^^^^^

    A regular sampling of a continuous, analog signal.

==================   ==========================
Parameter            Type
==================   ==========================
name\*               string
sampling_rate\*      float + units
t_start\*            float + units
signal\*             array of floats 1D + units
segment              foreign key to Segment_
recordingchannel     foreign key to RecordingChannel_
==================   ==========================

See exact object representation in :doc:`Matlab <../reference/matlab_examples>`, :doc:`Python <../reference/python_examples>` or :doc:`JSON DATA API <data_api/object_examples>`.
 

.. _IrSaAnalogSignal:

^^^^^^^^^^^^^^^^
IrSaAnalogSignal
^^^^^^^^^^^^^^^^

    An analog signal with non-regular sampling.

==================   ==========================
Parameter            Type
==================   ==========================
name\*               string
t_start\*            float + units
signal\*             array of floats 1D + units
times\*              array of floats 1D + units
segment              foreign key to Segment_
recordingchannel     foreign key to RecordingChannel_
==================   ==========================

See exact object representation in :doc:`Matlab <../reference/matlab_examples>`, :doc:`Python <../reference/python_examples>` or :doc:`JSON DATA API <data_api/object_examples>`.
 

.. _AnalogSignalArray:

^^^^^^^^^^^^^^^^^
AnalogSignalArray
^^^^^^^^^^^^^^^^^

    A regular sampling of a multichannel continuous analog signal. This representation (as a 2D array) may be more efficient for subsequent analysis than the equivalent list of individual AnalogSignal objects.

=====================   ==========================
Parameter               Type
=====================   ==========================
t_start\*               float + units
sampling_rate\*         float + units
signal\*                array of floats 2D + units
segment                 foreign key to Segment_
recordingchannelgroup   foreign key to RecordingChannelGroup_
=====================   ==========================

See exact object representation in :doc:`Matlab <../reference/matlab_examples>`, :doc:`Python <../reference/python_examples>` or :doc:`JSON DATA API <data_api/object_examples>`.
 

.. _Spike:

^^^^^
Spike
^^^^^

    One action potential characterized by its time and waveform.

=====================   ==========================
Parameter               Type
=====================   ==========================
time\*                  float + units
sampling_rate\*         float + units
left_sweep              float + units
waveform\*              array of floats 2D + units
segment                 foreign key to Segment_
unit                    foreign key to Unit_
=====================   ==========================

See exact object representation in :doc:`Matlab <../reference/matlab_examples>`, :doc:`Python <../reference/python_examples>` or :doc:`JSON DATA API <data_api/object_examples>`.


.. _SpikeTrain:

^^^^^^^^^^
SpikeTrain
^^^^^^^^^^

    A set of action potentials (spikes) emitted by the same unit in a period of time (with optional waveforms).

=====================   ==========================
Parameter               Type
=====================   ==========================
t_start\*               float + units
t_stop\*                float + units
times\*                 array of floats 1D + units
waveforms               array of floats 3D + units
segment                 foreign key to Segment_
unit                    foreign key to Unit_
=====================   ==========================

See exact object representation in :doc:`Matlab <../reference/matlab_examples>`, :doc:`Python <../reference/python_examples>` or :doc:`JSON DATA API <data_api/object_examples>`.


.. _Event:

^^^^^^^^^^^^^^^^^^^^
Event and EventArray
^^^^^^^^^^^^^^^^^^^^

    A time point representng an event in the data, or an array of such time points.

=====================   ==========================
Parameter               Type
=====================   ==========================
label(s)\*              string/array of strings 1D
time(s)\*               float/array of floats 1D + units
segment                 foreign key to Segment_
=====================   ==========================

See exact object representation in :doc:`Matlab <../reference/matlab_examples>`, :doc:`Python <../reference/python_examples>` or :doc:`JSON DATA API <data_api/object_examples>`.


.. _Epoch:

^^^^^^^^^^^^^^^^^^^^
Epoch and EpochArray
^^^^^^^^^^^^^^^^^^^^
    An interval of time representing a period of time in the data, or an array of such intervals.

=====================   ==========================
Parameter               Type
=====================   ==========================
label(s)\*              string/array of strings 1D
time(s)\*               float/array of floats 1D + units
duration(s)\*           float/array of floats 1D + units
segment                 foreign key to Segment_
=====================   ==========================

See exact object representation in :doc:`Matlab <../reference/matlab_examples>`, :doc:`Python <../reference/python_examples>` or :doc:`JSON DATA API <data_api/object_examples>`.


`ePhys Objects`_ comprise a simple hierarchy of containers:

.. _Segment:

^^^^^^^
Segment
^^^^^^^

    A container for heterogeneous discrete or continous data sharing a common clock (time basis) but not necessarily the same sampling rate, start time or end time. A Segment can be considered as equivalent to a “trial”, “episode”, “run”, “recording”, etc., depending on the experimental context. May contain any of the `ePhys Objects`_.

=====================   ==========================
Parameter               Type
=====================   ==========================
name\*                  string
filedatetime            datetime
index                   int
block                   foreign key to Block_
=====================   ==========================

Segment can contain objects of the following types:
 * AnalogSignal_
 * IrSaAnalogSignal_
 * AnalogSignalArray_
 * SpikeTrain_
 * Spike_
 * Event_
 * EventArray_
 * Epoch_
 * EpochArray_

See exact object representation in :doc:`Matlab <../reference/matlab_examples>`, :doc:`Python <../reference/python_examples>` or :doc:`JSON DATA API <data_api/object_examples>`.

.. _Block:

^^^^^
Block
^^^^^

    The top-level container gathering all of the data, discrete and continuous, for a given recording session. Contains Segment_ and RecordingChannelGroup_ objects.

=====================   ==========================
Parameter               Type
=====================   ==========================
name\*                  string
filedatetime            datetime
index                   int
section                 foreign key to Section_
=====================   ==========================

Block can contain objects of the following types:
 * Segment_
 * RecordingChannelGroup_

See exact object representation in :doc:`Matlab <../reference/matlab_examples>`, :doc:`Python <../reference/python_examples>` or :doc:`JSON DATA API <data_api/object_examples>`.


`ePhys Objects`_ also include *Grouping objects*. These objects express the relationships between data items, such as which signals were recorded on which electrodes, which spike trains were obtained from which membrane potential signals, etc. They contain references to data objects that cut across the simple container hierarchy.

.. _RecordingChannel:

^^^^^^^^^^^^^^^^
RecordingChannel
^^^^^^^^^^^^^^^^

    Links AnalogSignal_ and/or SpikeTrain_ objects that come from the same logical and/or physical channel inside a Block_, possibly across several Segment_ objects.

=====================   ==========================
Parameter               Type
=====================   ==========================
name\*                  string
index                   int
recordingchannelgroup   foreign key to RecordingChannelGroup_ 
=====================   ==========================

RecordingChannel can contain objects of the following types:
 * Unit_
 * AnalogSignal_
 * IrSaAnalogSignal_

See exact object representation in :doc:`Matlab <../reference/matlab_examples>`, :doc:`Python <../reference/python_examples>` or :doc:`JSON DATA API <data_api/object_examples>`.


.. _RecordingChannelGroup:

^^^^^^^^^^^^^^^^^^^^^
RecordingChannelGroup
^^^^^^^^^^^^^^^^^^^^^
    A group for associated RecordingChannel objects. This has several possible uses: for linking several AnalogSignalArray objects across several Segment objects inside a Block, for multielectrode arrays, where spikes may be recorded on more than one recording channel, and so the RecordingChannelGroup can be used to associate each Unit with the group of recording channels from which it was calculated, as well as for grouping several RecordingChannel objects. There are many use cases for this. For instance, for intracellular recording, it is common to record both membrane potentials and currents at the same time, so each RecordingChannelGroup may correspond to the particular property that is being recorded. For multielectrode arrays, RecordingChannelGroup is used to gather all RecordingChannel objects of the same array.

=====================   ==========================
Parameter               Type
=====================   ==========================
name\*                  string
block                   foreign key to Block_ 
=====================   ==========================

RecordingChannelGroup can contain objects of the following types:
 * RecordingChannel_
 * AnalogSignalArray_

See exact object representation in :doc:`Matlab <../reference/matlab_examples>`, :doc:`Python <../reference/python_examples>` or :doc:`JSON DATA API <data_api/object_examples>`.


.. _Unit:

^^^^
Unit
^^^^

    A Unit gathers all the `SpikeTrain`_ objects within a common Block_, possibly across several Segments, that have been emitted by the same cell. A Unit is linked to RecordingChannelGroup_ objects from which it was detected.

=====================   ==========================
Parameter               Type
=====================   ==========================
name\*                  string
recordingchannel        foreign key to RecordingChannel_ 
=====================   ==========================

Unit can contain objects of the following types:
 * SpikeTrain_
 * Spike_

See exact object representation in :doc:`Matlab <../reference/matlab_examples>`, :doc:`Python <../reference/python_examples>` or :doc:`JSON DATA API <data_api/object_examples>`.



--------
Metadata
--------

Having `ePhys Objects`_ is usually not enough to describe the experiment or evenmore the whole project. For other information, like the description of an Animal or a Stimuli, we use Metadata_.

.. _Metadata:

**Metadata**
    In this context metadata is any information about an experiment, EXcluding the information, described using `ePhys Objects`_. Our metadata concept is based on `odML <http://www.g-node.org/projects/odml>`_, which is essentially a flexible way to describe your experimental parameters using Section_ (simple container) tree with `Properties with Values`_ (key-value pairs). Take a look on the :ref:`example <overview_metadata>` in the overview.

The general metadata object model looks like:

.. image:: ../_static/metadata_om.png
    :align: center

it is implemented inline with `odML <http://www.g-node.org/projects/odml>`_ concept and consists of objects like Section_, `Properties with Values`_.  Here goes the complete metadata objects specification.

.. _Section:

^^^^^^^
Section
^^^^^^^

    An element used to group and organize your metadata in a tree structure. Intuitively it's like a folder in a usual file system. A Section can contain other Sections, `Properties with Values`_, Datafile_ or Block_. The Section is a prototype of the `odML <http://www.g-node.org/projects/odml>`_® section and is implemented inline with odML concepts and methodology.

=====================   ==========================
Parameter               Type
=====================   ==========================
name\*                  string
description             string
odml_type               int (0-99)
tree_position           int
parent_section          foreign key to Section_ 
=====================   ==========================

Section can contain objects of the following types:
 * Section_
 * Block_
 * Datafile_

See exact object representation in :doc:`Matlab <../reference/matlab_examples>`, :doc:`Python <../reference/python_examples>` or :doc:`JSON DATA API <data_api/object_examples>`.


.. _`Properties with Values`:

^^^^^^^^^^^^^^^^^^^^^
Properties and Values
^^^^^^^^^^^^^^^^^^^^^

    Inspired by the "key-value pairs" concept, Properties and Values used similarly as a flexible way to annotate your data (implemented in line with `odML <http://www.g-node.org/projects/odml>`_) within any metadata Section_. Some good examples could be a model of your recording device, duration of the stimulus, a layer of the cell you've recorded from. Properties and Values can be used to "label" your `ePhys Objects`_ (AnalogSignal_, SpikeTrain_ etc.) to indicate certain metadata for them. 

Property:

=====================   ==========================
Parameter               Type
=====================   ==========================
name\*                  string
definition              string
dependency              string
dependency_value        string
mapping                 string
unit                    string
dtype                   string
uncertainty             string
comment                 string
section\*               foreign key to Section_ 
=====================   ==========================

Property can contain one or several Value_ objects:


Value:

=====================   ==========================
Parameter               Type
=====================   ==========================
data\*                  string
parent_property\*       foreign key to Section_ 
=====================   ==========================

See exact objects representation in :doc:`Matlab <../reference/matlab_examples>`, :doc:`Python <../reference/python_examples>` or :doc:`JSON DATA API <data_api/object_examples>`.


.. _Data annotation:

^^^^^^^^^^^^^^^
Data annotation
^^^^^^^^^^^^^^^
    Data annotation is the process of assinging metadata to the data, when some special connection is required. It is needed basically in order to establish a connection between data and metadata for easy search and generic access. An example could be a case when you, say, described the color of your Stimuli as a property in a Stimuli section, and after you acquire the data you need to indicate, which particular signals were recorded at which particular color frequency. In this case, data annotation means establishing a link between particular signals and particular values of the color property.

.. _Datafile:

-----
Files
-----

^^^^^^^^
Datafile
^^^^^^^^

    Datafile represents an arbitrary file, uploaded by a user. Some data or metadata can be extracted from the Datafile if it is in one of the supported formats (`NEO I/O <http://neo.readthedocs.org/en/latest/io.html>`_, `Neuroshare <http://neuroshare.sourceforge.net/index.shtml>`_, `odML <http://www.g-node.org/projects/odml>`_). All data-related objects, like AnalogSignal_ or Spike_, have their data part also stored as HDF5 files (`what is HDF5? <http://www.hdfgroup.org/HDF5/whatishdf5.html>`_), having array in the file root.

=====================   ==========================
Parameter               Type
=====================   ==========================
name\*                  string
caption                 string
section\*               foreign key to Section_
=====================   ==========================

The system supports data conversion from files to the :ref:`ePhys <ePhys Objects>` and Metadata_ objects, listed above, if the Datafile_ is compartible with supported formats (see Datafile_ above).

-------
Summary
-------

Just to summarize this document, the data model for the whole system looks like:

.. image:: ../_static/common_om.png
    :width: 800 px
    :align: center

