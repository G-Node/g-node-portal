===============
FILE MANAGEMENT
===============

In this section we describe how to upload, download and convert files.

.. _file_types:

--------------------------------
File types and supported formats
--------------------------------

We support data conversion from several open and proprietary formats, available in the domain of neuroscience (feature is not available at the demo server). If you upload a file of the supported format, you may request to extract data from it further to be able to directly access your experimental data and metadata or export and download it in different format. We do support data extraction from ZIP/TAR archives too. Here is the list of supported formats:

=================   ====    ==================================
Format              Type    Comments
=================   ====    ==================================
neuroshare\*        1       `Neuroshare <http://neuroshare.sourceforge.net/standards.shtml>`_ compatible. Extracted into :ref:`ePhys Objects <ePhys Objects>`
NEO I/O*\           2       compatible with `NEO I/O <http://neo.readthedocs.org/en/latest/io.html>`_. Extracted into :ref:`ePhys Objects <ePhys Objects>`
ascii-csv           3       a csv file where every line represents a signal as comma-separated floats. Extracted into :ref:`AnalogSignal <AnalogSignal>` objects
odML\*              4       compatible with `odML <http://www.g-node.org/projects/odml>`_. Extracts odML structure and creates the same metadata structure in the root of the metadata tree
HDF5                5       a file should have an array in the root ("/"). Can be used with any :ref:`data-containing object <array_upload_note>` as a corresponding data array.
ZIP                 6       ZIP archive. Extracted folders are replicated as metadata :ref:`Section <Section>` objects containing original files from the archive.
TAR                 7       TAR archive. Extracted folders are replicated as metadata :ref:`Section <Section>` objects containing original files from the archive.
=================   ====    ==================================

*Note. Conversion from types marked with asterisk are in development.*

-------------------------
Getting list of datafiles
-------------------------

 ::

    Request: GET /datafiles/?params

Typically you should get the following response:

 ::
    
    Response: HTTP SUCCESS (200)
    
    {
    "logged_in_as": "bob",
    "objects_selected": 2,
    "selected": [
        {
        "fields": {
            "last_task_id": "2a3c89f4-9328-46aa-801e-b7bf88740f8f",
            "title": "ascii_csv.txt",
            "extracted_info": null,
            "tags": "",
            "current_state": 10,
            "operations_log": "python-neuroshare: failure ascii-csv: success ",
            "conversion_type": 3,
            "caption": "",
            "last_modified": "2012-03-14 17:27:08",
            "safety_level": 3,
            "date_created": "2012-03-14 17:27:08"
        },
        "model": "datafiles.datafile",
        "permalink": "/datafiles/9"
        },
        {
        "fields": {
            "last_task_id": "7c260cfa-2395-40b7-907e-065088f41498",
            "title": "ascii_csv.txt",
            "extracted_info": null,
            "tags": "",
            "current_state": 10,
            "operations_log": "python-neuroshare: failure ascii-csv: success ... ",
            "conversion_type": 3,
            "caption": "",
            "last_modified": "2012-03-15 10:27:45",
            "safety_level": 3,
            "date_created": "2012-03-14 17:29:21"
        },
        "model": "datafiles.datafile",
        "permalink": "/datafiles/10"
        }
    ],
    "message": "Here is the list of requested objects.",
    "selected_range": [0, 1],
    "message_type": "object_selected"
    }

.. _getting_single_file:

---------------------------
Getting single file details
---------------------------

Requesting information about a single file is like a normal :ref:`single object request <get_single_object>`:

 ::
    
    Request: GET /datafiles/<datafile_id>

See the response example above; it's very similar.

----------------------------------------------
Upload a datafile (with or without conversion)
----------------------------------------------

 ::
    
    Request: POST /datafiles/?params

    the file should be sent with "raw_file" key.

parameters:
 * [section_id] - provide an ID of the section in which to store the file (recommended).
 * [convert] - 1 (default), 0 - whether try to convert the file into native format, if possible. See the :ref:`supported file types in this table <file_types>`. 

*Note. If the file is uploaded into a specific section, the security settings for the new file will be assigned as for the parent section. When no section is specified, the file is private by default.*

*Note. For bulk file upload consider this* :ref:`description in the overview <bulk_upload>`.

----------------------------------------------------
Modify datafile attributes, move file to the Section
----------------------------------------------------

 ::
    
    Request: POST /datafiles/<datafile_id>/?params

    {
        "caption": "Some REAL description goes here..",
        "section": 1236,
    }

*Note. ACL for the file is not changed when moved to a different section.*


-------------------
Datafile conversion
-------------------

The following request

 ::
    
    Request: GET /datafiles/<datafile_id>/convert/


initiates file conversion.

*Note. The conversion operation is asynchronous, which means you'll not see the status of the conversion immediately in the response. You may use a normal :ref:`details request <getting_single_file>` to check whether the file was converted successfully and see log for details.*

-----------------
Download datafile
-----------------

When the file is not converted, you may get the originally uploaded file. When file is in native format, it can convert all underlying data into a file with the format specified and return it for download.

 ::
    
    Request: GET /datafiles/<datafile_id>/download/?params


---------------
Delete datafile
---------------

 ::
    
    Request: DELETE /datafiles/<datafile_id>/?params


in addition to the usual filters, use can use the following parameters:
 * [force] - true, false (default) - use "true" to delete the file even if there are other users with access to the file. If "false" or omitted, the file will not be deleted being in the state having collaborators.


