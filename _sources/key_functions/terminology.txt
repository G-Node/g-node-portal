============
Key concepts
============

Throughout this entire documentation, it's refered to a series of terms like :ref:`Section <Section>` or :ref:`Datafile <Datafile>`, which are intended to have specific meanings.

.. _common_terms:

---------
permalink
---------

    A permanent link to an object, similar to the URL. Every object in this context has a permalink. For example, a :ref:`Datafile <Datafile>` could have a permalink like 

 ::

    https://portal.g-node.org/data/datafiles/3ABETEVKFI/

-----------------
object identifier
-----------------

    Every object has base 32 string ID of a length 10 (like 3ABETEVKFI), unique within the object type namespace, used to identify this object. It is also used in permalinks to access an object. There is also a "global" unique ID (guid) - a 40 symbols hash code (like c30fb5dee94e44930abc572e1b3e4847a0f51dd3), used to identify a certain version of the same object.


.. _`ePhys Objects`:

-----------------------------------
electrophysiology ( ePhys Objects )
-----------------------------------

    ePhys Objects are used to represent the recorded (raw) electrophysiological data in a flexible, but consistent way. ePhys Objects represent data structure with data as arrays of numerical values with associated mandatory attributes (units, sampling frequency, etc.). These objects are based on NEO data model (an original concept is described `here <http://neo.readthedocs.org/en/latest/core.html>`_ ), we provide all descriptions down below for convenience. A good example explaining supported electrophisyology objects :ref:`is described in the overview <overview_ephys>`.
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

Every object serves a specific purpose to organize your electrophysiological data. :ref:`Block <Block>` mainly represents one experiment (or the whole data, recorded within one experiment). A :ref:`Segment <Segment>` represents an experimental trial, a "time frame" within an experiment with unique experimental conditions. Blocks may contain several segments, like an experiment consists of several trials. A segment may contain :ref:`AnalogSignal <AnalogSignal>`, :ref:`Event <Event>` or :ref:`Epoch <Epoch>` objects, which are used to organize recorded signals, single time events, or events with duration inside one experimental time-frame. Segment may also contain :ref:`SpikeTrain <SpikeTrain>` objects to accommodate the spike data. For every block one may define a set of :ref:`Recording Channels <RecordingChannel>` according to the experimental setup. These channels can be organized under a :ref:`RecordingChannelGroup <RecordingChannelGroup>`, to keep track of tethrodes. AnalogSignals can be linked to the appropriate RecordingChannels to make the dataset consistent. The whole NEO concept at its best is described `here <http://neo.readthedocs.org/>`_.

Having `ePhys Objects`_ is usually not enough to describe the experiment or even more the whole project. For other information, like the description of an Animal or a Stimuli, a set of Metadata_ objects is used.

.. _Metadata:

--------
metadata
--------

    In this context metadata is any information about an experiment, excluding the information, described using `ePhys Objects`_. Our metadata concept is based on `odML <http://www.g-node.org/projects/odml>`_, which is essentially a flexible way to describe experimental parameters using :ref:`Section <Section>` (simple container) tree with :ref:`Properties with Values <Properties with Values>` (key-value pairs). Take a look on the :ref:`example <overview_metadata>` in the overview.


.. _Data annotation:

---------------
data annotation
---------------

    Data annotation is the process of assinging metadata to the data, when some special connection is required. It is needed basically in order to establish a connection between data and metadata for easy search and generic access. A good example could be a case with an experiment having stimulus changing its property (say, color for visual stimuli), when for every trial the appropriate stimulus color has to be indicated. In this case, data annotation means establishing a link between particular time segments (trials) and particular values of the color property, stored in the stimuli-type odML section.


.. _acl_term:

-------------------
access control list
-------------------

    Access Control List (ACL) is a property of an object that is used to keep a list of users, having access to the object. Every user in this list can have either read-only or read-write permissions. Besides defined ACL and object has its own publication state, which indicates whether an object is open for all other users or not (private by default). ACL is independent from a publication state: if an object is private, people, listed in the ACL, **will be able to access** an object. Not all objects can have ACLs, here is a list of objects with ACL support:

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


