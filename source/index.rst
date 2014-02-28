.. g-node-portal documentation master file, created by
   sphinx-quickstart on Thu Sep 22 17:35:49 2011.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

**********************************
Welcome to the G-Node Data Portal!
**********************************

G-Node Data Portal (GNData) is a platform for Neuroscientists to facilitate data access, data storage, data analysis and exchange. With GNData you may store and efficiently organize your experimental data, exchange data with collaborators, search for published data or scientists with similar interests. 

Start by reading the GNData :doc:`overview <overview>`.

You may sign up and instantly `try the platform <http://test.g-node.org>`_ via web browser with the user "demo" and password "demo". If you are interested in using the platform from the Matlab or Python, consider our tutorials (`matlab <https://github.com/G-Node/gnode-client-matlab/wiki/Getting-started>`_, `python <http://g-node.github.io/python-gnode-client/getting_started.html>`_). If you are interested in the common DATA API, please start exploring :doc:`the principles here <key_functions/data_api/principles>`.

Everything you want to know about GNData Platform:

"""""""""""
First steps
""""""""""" 
 * get started: 

   * :doc:`overview <overview>` | `try the web demo (with "demo", "demo") <http://test.g-node.org>`_

 * tutorials:

   * `tutorial for Matlab users <https://github.com/G-Node/gnode-client-matlab/wiki/Getting-started>`_ | `tutorial for Python users <http://g-node.github.io/python-gnode-client/getting_started.html>`_

""""""""""""""""""
Download & Install
""""""""""""""""""
 * `client for Matlab <https://github.com/G-Node/gnode-client-matlab/wiki/Installation>`_ | `client for Python <http://g-node.github.io/python-gnode-client/install.html>`_ | :doc:`soon: back-end server <install/_backend>`

"""""""""""""
Key functions
"""""""""""""
 * Introduction: 

   * :doc:`key concepts <key_functions/terminology>` | :doc:`objects and data model <key_functions/object_model>`

 * Matlab:

   * `getting started <https://github.com/G-Node/gnode-client-matlab/wiki/Getting-started>`_ | `general concepts <https://github.com/G-Node/gnode-client-matlab/wiki/General>`_ | `retrieval <https://github.com/G-Node/gnode-client-matlab/wiki/Retrieval>`_ | `upload <https://github.com/G-Node/gnode-client-matlab/wiki/Upload>`_ | `utilities <https://github.com/G-Node/gnode-client-matlab/wiki/Utilities>`_

 * Python:

   * `getting started <http://g-node.github.io/python-gnode-client/getting_started.html>`_ | `general concepts <http://g-node.github.io/python-gnode-client/general.html>`_ | `retrieval <http://g-node.github.io/python-gnode-client/retrieval.html>`_ | `upload <http://g-node.github.io/python-gnode-client/upload.html>`_

 * DATA API: 

   * current version 0.2: 

     * :doc:`REST API principles <key_functions/data_api/principles>` | :doc:`authentication <key_functions/data_api/authentication>` | :doc:`electrophysiology <key_functions/data_api/electrophysiology>` | :doc:`metadata <key_functions/data_api/metadata>` | :doc:`data annotation <key_functions/data_api/annotation>` | :doc:`files and formats <key_functions/data_api/files>` | :doc:`array data <key_functions/data_api/array_data>` | :doc:`search and query <key_functions/data_api/query>` | :doc:`permissions <key_functions/data_api/permissions>` | :doc:`history <key_functions/data_api/history>` | :doc:`object specs and examples (JSON) <key_functions/data_api/object_examples>`

   * version 0.1: 

     * :doc:`API reference <key_functions/data_api/0.1/api_specification>` | :doc:`object specs and examples (JSON) <key_functions/data_api/0.1/api_object_examples>`


"""""""""""""""""
Labs using G-Node
"""""""""""""""""
 * `Laboratoire de Physiologie Cérébrale UMR8118 <http://www.biomedicale.univ-paris5.fr/physcerv/>`_
 * `Neural processing in sensory systems, LMU München <http://neuro.bio.lmu.de/research_groups/res-benda_j/index.html>`_


.. 
   toctree::
   :maxdepth: 3

   overview.rst
   tutorial/tutorial_index.rst
   data_api/data_api_index.rst
   
