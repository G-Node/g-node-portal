============
Key concepts
============

Throughout this entire documentation, it's refered to a series of terms like :ref:`Section <Section>` or :ref:`Datafile <Datafile>`, which are intended to have specific meanings.

.. _common_terms:

---------
permalink
---------

    A permanent link to an object, similar to the URL. Every object in this context has a permalink. For example, a :ref:`Datafile <Datafile>` could have a permalink like https://portal.g-node.org/data/datafiles/15

-----------------
object identifier
-----------------

    Every object has two IDs: a "local" ID - an integer number, unique within the object type namespace (like there is only one :ref:`Event <Event>` with ID 15 but there could be other objects, say, a :ref:`Segment <Segment>` with id 15 too), which is used in permalinks to access an object, and a global ID (guid) - a 40 symbols hash code (like *c30fb5dee94e44930abc572e1b3e4847a0f51dd3*), unique across all objects.


.. _`ePhys Objects`:

-----------------------------------
electrophysiology ( ePhys Objects )
-----------------------------------

    ePhys Objects are used to represent the recorded (raw) electrophysiological data in a flexible, but consistent way. ePhys Objects directly represent raw data structure with data as arrays of numerical values with associated mandatory attributes (units, sampling frequency, etc.). An original concept is described `here <http://neo.readthedocs.org/en/latest/core.html>`_, however we provide all descriptions down below for convenience. A good example :ref:`is described in the overview <overview_ephys>`. 
The following objects in the domain of electrophysiology are supported:

 * :ref:`AnalogSignal <AnalogSignal>`
 * :ref:`IrSaAnalogSignal <IrSaAnalogSignal>`
 * :ref:`AnalogSignalArray <AnalogSignalArray>`
 * :ref:`SpikeTrain <SpikeTrain>`
 * :ref:`Spike <Spike>`
 * :ref:`Event <Event>`
 * :ref:`EventArray <Event>`
 * :ref:`Epoch <Epoch>`
 * :ref:`EpochArray <Epoch>`
 * :ref:`Block <Block>`
 * :ref:`Segment <Segment>`
 * :ref:`Unit <Unit>`
 * :ref:`RecordingChannelGroup <RecordingChannelGroup>`
 * :ref:`RecordingChannel <RecordingChannel>`

Every object serves a specific purpose to organize your electrophysiological data. :ref:`Block <Block>` mainly represent one experiment (or the whole data, recorded within one experiment). A :ref:`Segment <Segment>` represents an experimental trial, a "time frame" within an experiment with unique experimental conditions. Blocks may contain several segments, like an experiment consists of several trials. A segment may contain :ref:`AnalogSignal <AnalogSignal>`, :ref:`Event <Event>` or :ref:`Epoch <Epoch>` objects, which are used to organize recorded signals, single time events, or events with duration inside one experimental time-frame. Segment may also contain :ref:`SpikeTrain <SpikeTrain>` objects to accommodate the spike data. For every block one may define a set of :ref:`Recording Channels <RecordingChannel>` according to the experimental setup. These channels can be organized under a :ref:`RecordingChannelGroup <RecordingChannelGroup>`, to keep track of tethrodes. AnalogSignals can be linked to the appropriate RecordingChannels to make the dataset consistent.

Having `ePhys Objects`_ is usually not enough to describe the experiment or evenmore the whole project. For other information, like the description of an Animal or a Stimuli, we use Metadata_.

.. _Metadata:

--------
metadata
--------

    In this context metadata is any information about an experiment, EXcluding the information, described using `ePhys Objects`_. Our metadata concept is based on `odML <http://www.g-node.org/projects/odml>`_, which is essentially a flexible way to describe your experimental parameters using :ref:`Section <Section>` (simple container) tree with :ref:`Properties with Values <Properties with Values>` (key-value pairs). Take a look on the :ref:`example <overview_metadata>` in the overview.


.. _Data annotation:

---------------
data annotation
---------------

    Data annotation is the process of assinging metadata to the data, when some special connection is required. It is needed basically in order to establish a connection between data and metadata for easy search and generic access. An example could be a case when you, say, described the color of your Stimuli as a property in a Stimuli section, and after you acquire the data you need to indicate, which particular signals were recorded at which particular color frequency. In this case, data annotation means establishing a link between particular signals and particular values of the color property.


.. _acl_term:

-------------------
access control list
-------------------

    Access Control List (ACL) is a property of an object that is used to keep a list of users, having access to the object. Every user in this list can have either read-only or read-write permissions. ACL is independent from basic sharing state (safety level) for an object: even if an object is private, people, listed in the ACL, **will be able to access** an object. Not all objects can have ACLs, here is a list of objects with sharing support:

 * all ePhys objects:
   * :ref:`AnalogSignal <AnalogSignal>`
   * :ref:`IrSaAnalogSignal <IrSaAnalogSignal>`
   * :ref:`AnalogSignalArray <AnalogSignalArray>`
   * :ref:`SpikeTrain <SpikeTrain>`
   * :ref:`Spike <Spike>`
   * :ref:`Event <Event>`
   * :ref:`EventArray <Event>`
   * :ref:`Epoch <Epoch>`
   * :ref:`EpochArray <Epoch>`
   * :ref:`Block <Block>`
   * :ref:`Segment <Segment>`
   * :ref:`Unit <Unit>`
   * :ref:`RecordingChannelGroup <RecordingChannelGroup>`
   * :ref:`RecordingChannel <RecordingChannel>`
 * metadata Section
   * :ref:`Section <Section>`
 * and any file
   * :ref:`Datafile <Datafile>`


