.. _overview:

Overview
========

The aim of the G-Node Portal is to provide a platform for Neuroscientists for data storage, analysis and exchange. It was designed to support normal experimental workflow, so you may use it from your familiar analytical environment, like Matlab or Python. Here we give an overview of the platform and available services.

The goal of this document is to present you the potential usage of the platform for your specific experimental workflow. If you want to try it now or you're ready for a more detailed description - please `login <https://portal.g-node.org/data/>`_ or `sign up <https://portal.g-node.org/data/account/signup/>`_, or proceed with our tutorials (:doc:`tutorial for Matlab users <tutorial_matlab>` | :doc:`tutorial for Python users <tutorial_python>`).

.. _overview_ephys:

-------------------------------
Organize your experimental Data
-------------------------------

Intuitively, these objects can be all drawn on this diagram:

.. image:: ../_static/ephys_basic.png
    :align: center


.. _overview_metadata:

------------------------------
Create your metadata structure
------------------------------

.. image:: _static/metadata_example.png
    :align: center


.. _annotation_example:

-----------------
Annotate datasets
-----------------


Imagine you have an experiment with stimulus which changes its color across experimental trials. The "color" is an arbitrary parameter, we cannot predict the exact setup of your experimental study. In your future research you want to indicate, which signals were recorded under which stimulus color, to be able to perform some valueable data analysis. Essentially this is a need to connect recorded signal data with arbitrary metadata; we call this "data annotation" process. In this section we describe how it works.

The idea is that you use objects in electrophysiology ...


Assume you want to group channels by some spatial property, and later compare the results from this group to the results from all channels. Bla..


Assume in the experiment section tree you already defined a property, say, "Stimulus Color" with values "red, green and blue". In that case, annotate all required Analog Signals with appropriate metadata values, assigning a "red" value to the signals recorded when the Stimulius was red etc.


----------------------
Query your experiments
----------------------

This section is being described..

--------------------------------
Open your data for Collaborators
--------------------------------

This section is being described..

-------------
Track changes
-------------

This section is being described..

----------------
Bulk data upload
----------------

^^^^^^^^^^^^
Zip archives
^^^^^^^^^^^^

This section is in development..

^^^^^^^^^^^^^^^
Upload via sFTP
^^^^^^^^^^^^^^^

This section is in development..


