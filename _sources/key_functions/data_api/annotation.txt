===============
Data Annotation
===============

Data annotation is the process of connecting data and metadata, by "tagging" recorded :ref:`ePhys elements <ePhys Objects>` with appropriate metadata values. To understand data annotation better, consider a :ref:`nice example in the overview <annotation_example>` or :ref:`its definition <Data annotation>`.

You may annotate (or "tag") one or several :ref:`ePhys Objects <ePhys Objects>` with particular metadata values by using the following request:

 ::
    
    Request: POST /electrophysiology/analogsignal/11/

    {
        "metadata": [
            "/metadata/values/2",
            "/metadata/values/15"
        ]
    }

If the POST request is sent to the selection (several objects), all objects in the selection will be updated. Importantly, by default metadata values are added to the existing metadata values. If you need to completely overwrite object's metadata, provide m2m_append=0 parameter in the request. This is also the way to **remove** object metadata, providing empty list together with m2m_append=0.

Another feature is that metadata propagates down the :ref:`electrophysiological hierarchy <ePhys Objects>` by default, e.g. if you annotate a :ref:`Segment <Segment>` that contains some :ref:`AnalogSignals <AnalogSignal>` or :ref:`Events <Event>`, all these signals and events will be also annotated. This can be controlled with the "cascade" attribute: provide cascade=0 to cancel cascade propagation.
