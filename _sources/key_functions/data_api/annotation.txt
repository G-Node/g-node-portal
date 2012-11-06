===============
Data Annotation
===============

Imagine you have an experiment with stimulus which changes its color across experimental trials. After several trials you need to indicate, which signals were recorded under which stimulus color. We call this basic linking of data and metadata as "data annotation". In this section we describe how it works in this context.

 This is Assume in the experiment section tree you already defined a property, say, "StimulusColor" with values "red, green, blue". In that case, annotate all required Analog Signals with appropriate metadata values, assigning a "red" value to the signals recorded when the Stimulius was red etc.*


You may annotate (or "tag") one or several :ref:`ePhys Objects <ePhys Objects>` with particular metadata values by using the following request:

 ::
    
    Request: POST /electrophysiology/analogsignal/11/

    {
        "metadata": [
            "/metadata/values/2",
            "/metadata/values/15"
        ]
    }

If the POST request is sent to the selection (several objects), all objects in the selection will be updated. Importantly, by default metadata values are added to the existing metadata values. If you need to completely overwrite object's metadata, provide m2m_append=0 parameter in the request. This is the way to remove object metadata, providing empty list together with m2m_append=0.

Another feature is that metadata propagates down the electrophysiological hierarchy by default, e.g. if you annotate a :ref:`Segment <Segment>` that contains some :ref:`AnalogSignals <AnalogSignal>` or :ref:`Events <Event>`, all these signals and events will be also annotated. This can be controlled with the "cascade" attribute, provide cascade=0 to cancel propagation.

