===============
FILE MANAGEMENT
===============

About...

.. _file_types:

--------------------------------
File types and supported formats
--------------------------------


List supported formats and corresponsing file types

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
            "operations_log": "python-neuroshare: failure ascii-csv: success ascii-csv: parsing successul ascii-csv: parsing successul objects parsed: 3ascii-csv: parsing successul objects parsed: 3",
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

---------------------------
Getting single file details
---------------------------

Requesting information about a single file is like a normal :ref:`single object request <get_single_object>`:

 ::
    
    Request: GET /datafiles/<datafile_id>


----------------------------------------------
Upload a datafile (with or without conversion)
----------------------------------------------

 ::
    
    Request: POST /datafiles/?params

    the file should be sent with "raw_file" key.

parameters:
 * [section_id] - provide an ID of the section in which to store the file (recommended).
 * [convert] - 1 (default), 0 - whether try to convert the file into native format, if possible. For the moment the following types are supported: neuroshare, ascii-csv (a csv file where every line is a signal).

*Note. If the file is uploaded into a specific section, the security settings for the new file will be assigned as for the parent section. When no section is specified, the file is private by default.*


------------------------------------------------
Modify datafile attributes, move file to Section
------------------------------------------------

 ::
    
    Request: POST /datafiles/<datafile_id>/?params

    {
        "caption": "Some REAL description goes here..",
        "section": 1236,
    }

*Note. The conversion operation is asynchronous, which means you'll not see the status of the conversion immediately in the response. You may use a details request (2.2) to check whether the file was converted successfully or not.*

*Note. ACL for the file is not changed when moved to a different section.*


-------------------
Datafile conversion
-------------------

The following request

 ::
    
    Request: GET /datafiles/<datafile_id>/convert/


initiates file conversion.


-----------------
Download datafile
-----------------

When the file is not converted, you may get the originally uploaded file. When file is in native format, it can convert all underlying data into a file with the format specified and return it for download.

 ::
    
    Request: GET /datafiles/<datafile_id>/download/?params

New in development version.
in addition to the usual filters, use can use the following parameters:
 * [format] - required file format. The following formats are supported: HDF5. Leave this empty to download an original file.


---------------
Delete datafile
---------------

 ::
    
    Request: DELETE /datafiles/<datafile_id>/?params


in addition to the usual filters, use can use the following parameters:
 * [force] - true, false (default) - use "true" to delete the file even if there are other users with access to the file. If "false" or omitted, the file will not be deleted being in the state having collaborators.


