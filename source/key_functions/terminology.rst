======================================
Terminology used in this specification
======================================

Throughout this specification, it's refered to a series of terms like Section_ or Datafile_, which are intended to have specific meanings.


.. _Datafile:

**Datafile**
    Datafile represents an arbitrary file, uploaded by a user. Some data or metadata can be extracted from the Datafile if it is in one of the supported formats (`NEO I/O <http://neo.readthedocs.org/en/latest/io.html>`_, `Neuroshare <http://neuroshare.sourceforge.net/index.shtml>`_, `odML <http://www.g-node.org/projects/odml>`_). All data-related objects, like AnalogSignal_ or Spike_, have their data part also stored as HDF5 files (`what is HDF5? <http://www.hdfgroup.org/HDF5/whatishdf5.html>`_), having array in the file root.

.. _Section:

**Section**
    An element used to group and organize your metadata in a tree structure. Intuitively it's like a folder in a usual file system. A Section can contain other Sections, `Properties with Values`_, Datafile_ or Block_. The Section® is a prototype of the `odML <http://www.g-node.org/projects/odml>`_ section and is implemented inline with odML concepts and methodology.

.. _`Properties with Values`:

**Properties and Values**
    Inspired by the "key-value pairs" concept, Properties and Values used similarly as a flexible way to annotate your data (implemented in line with `odML <http://www.g-node.org/projects/odml>`_) within any metadata Section_. Some good examples could be a model of your recording device, duration of the stimulus, a layer of the cell you've recorded from. Properties and Values can be used to "label" your `Data objects`_ (AnalogSignal_, SpikeTrain_ etc.) to indicate certain metadata for them. 

Below you will find descriptions of the NEO objects, which are used to represent the recorded (raw) data in a flexible, but consistent way. An original description of the NEO objects is given `here <http://neo.readthedocs.org/en/latest/core.html>`_, however we still provide all these descriptions down below for your convenience.

.. _`Data objects`:

Here we describe *Data objects*. These objects directly represent data as arrays of numerical values with associated mandatory attributes (units, sampling frequency, etc.).

.. _AnalogSignal:

**AnalogSignal**
    A regular sampling of a continuous, analog signal.

**AnalogSignalArray**
    A regular sampling of a multichannel continuous analog signal. This representation (as a 2D array) may be more efficient for subsequent analysis than the equivalent list of individual AnalogSignal objects.

.. _Spike:

**Spike**
    One action potential characterized by its time and waveform.

.. _SpikeTrain:

**SpikeTrain**
    A set of action potentials (spikes) emitted by the same unit in a period of time (with optional waveforms).

.. _Event:

**Event and EventArray**
    A time point representng an event in the data, or an array of such time points.

.. _Epoch:

**Epoch and EpochArray**
    An interval of time representing a period of time in the data, or an array of such intervals.

There is also a simple hierarchy of containers:

.. _Segment:

**Segment**
    A container for heterogeneous discrete or continous data sharing a common clock (time basis) but not necessarily the same sampling rate, start time or end time. A Segment can be considered as equivalent to a “trial”, “episode”, “run”, “recording”, etc., depending on the experimental context. May contain any of the `Data objects`_.

.. _Block:

**Block**
    The top-level container gathering all of the data, discrete and continuous, for a given recording session. Contains Segment_ and RecordingChannelGroup_ objects.

We support also *Grouping objects*. These objects express the relationships between data items, such as which signals were recorded on which electrodes, which spike trains were obtained from which membrane potential signals, etc. They contain references to data objects that cut across the simple container hierarchy.

.. _RecordingChannel:

**RecordingChannel**
    Links AnalogSignal_ and/or SpikeTrain objects that come from the same logical and/or physical channel inside a Block, possibly across several Segment objects.

**RecordingChannelGroup**
    A group for associated RecordingChannel objects. This has several possible uses: for linking several AnalogSignalArray objects across several Segment objects inside a Block, for multielectrode arrays, where spikes may be recorded on more than one recording channel, and so the RecordingChannelGroup can be used to associate each Unit with the group of recording channels from which it was calculated, as well as for grouping several RecordingChannel objects. There are many use cases for this. For instance, for intracellular recording, it is common to record both membrane potentials and currents at the same time, so each RecordingChannelGroup may correspond to the particular property that is being recorded. For multielectrode arrays, RecordingChannelGroup is used to gather all RecordingChannel objects of the same array.

.. _Unit:

**Unit**
    A Unit gathers all the SpikeTrain objects within a common Block, possibly across several Segments, that have been emitted by the same cell. A Unit is linked to RecordingChannelGroup objects from which it was detected. This replaces the Neuron class in the previous version of Neo (v0.1).

    Our system support data conversion from files to the data and metadata objects, listed above, if the Datafile_ is compartible with supported formats (see Datafile_ above).


