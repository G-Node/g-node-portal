
===================
METADATA MANAGEMENT
===================

You may organize your experimental data using :ref:`odML Sections and Properties <Metadata>`. They are useful elements to created flexible nested structures, containing key-value pairs, aimed to help scientists to describe their experiments and annotate recorded data. Below we describe actions available for you to manage your experimental metadata. To better understand our metadata concept please consider a nice example in the :ref:`overview <overview_metadata>`. For object specification refer to the :ref:`object model <Metadata>`.

------------------------
Getting list of sections
------------------------

 ::
    
    GET /metadata/section/?params

in addition to the :doc:`usual filters <query>`, use the following parameters:

 * [top] - owned (default), shared - return only top (no parent) sections, owned by the user or shared with the user

Example response:

 ::
    
    HTTP SUCCESS (200)
    
    {
    "logged_in_as": "jeff",
    "objects_selected": 3,
    "selected": [
        {
        "fields": {
            "parent_section": null,
            "tree_position": 0,
            "odml_type": 0,
            "name": "bla",
            "datafile_set": [
                "/datafiles/4",
                "/datafiles/10"
            ],
            "property_set": [],
            "current_state": 10,
            "is_template": false,
            "local_id": 1,
            "safety_level": 3,
            "block_set": [
                "/electrophysiology/block/2",
                "/electrophysiology/block/3"
            ],
            "owner": "/profiles/profile/5",
            "date_created": "2012-08-30T16:18:02",
            "section_set": [(0)],
            "guid": "bef93665387c2702f8eff6302e9ac83d600ae56b",
            "user_custom": null,
            "description": ""
            },
            "model": "metadata.section",
            "permalink": "/metadata/section/1"
        },
        ...
        {
        "fields": {
            "parent_section": "/metadata/section/1",
            "tree_position": 1,
            "odml_type": 0,
            "name": "Electrode",
            "datafile_set": [],
            "property_set": [
                "/metadata/property/1",
                "/metadata/property/2",
                "/metadata/property/3",
                "/metadata/property/4"
            ],
            "current_state": 10,
            "is_template": false,
            "local_id": 3,
            "safety_level": 3,
            "block_set": [],
            "owner": "/profiles/profile/5",
            "date_created": "2012-11-05T17:16:17",
            "section_set": [],
            "guid": "c16eee29a69da4c19bce19f6d16ea7b9406cc366",
            "user_custom": null,
            "description": "Settings and other experimental setup"
            },
            "model": "metadata.section",
            "permalink": "/metadata/section/3"
        }
    ],
    "message": "Here is the list of requested objects.",
    "selected_range": [(2)
    0,
    2
    ],
    "message_type": "object_selected"
    }

In this example you can see the :ref:`section <Section>` with ID = 1 has a couple of :ref:`datafiles <Datafile>` inside plus a couple of :ref:`blocks <Block>` linked to it. The second section (ID = 2) has no data inside however holds several properties, which describe the experiment.


-------------------------
Section: create or update
-------------------------

Create and update operations are implemented inline with the :doc:`key API principles <principles>`:

 ::
    
    Request: POST /metadata/section/[section_id/]?params

    {
        "parent_section": 2,
        "name": "Electrode",
        "tree_position": 1,
        "odml_type": 0,
        "safety_level": 3,
        "description": "Settings and other experimental setup"
    }

*New in the development version:*
if you need to create a section from the template (or from other section) provide the parameters below:

 * source_template - section to copy
 * [with_properties] - whether or not to copy properties, which belong to the section ()
 * [with_datafiles] - whether or not to copy links (!!) to datafiles, which belong to the section (in copy mode).
 * [recursive] - true, false - copy all subsections recursively (in copy mode)

*Note. Files and blocks, contained in the template section, are never duplicated while making a copy: newly created section(s) will not contain any files or other data.*

Response:
    
 ::
    
    HTTP CREATED (201)
    
    {
    "logged_in_as": "bob",
    "objects_selected": 1,
    "selected": [{
        "fields": {
            "tree_position": 1,
            "odml_type": 0,
            "description": "Settings and other experimental setup",
            "current_state": 10,
            "is_template": false,
            "last_modified": "2012-04-10 15:59:00",
            "safety_level": 3,
            "date_created": "2012-04-10 15:59:00",
            "name": "Electrode"
        },
        "model": "metadata.section",
        "permalink": "/metadata/section/18"
    }],
    "message": "Here is the list of requested objects.",
    "selected_range": [0, 0],
    "message_type": "object_selected"
    }


-----------------------
Getting section details
-----------------------

Similarly to the usual :ref:`GET single object <get_single_object>` request:

 ::
    
    GET /metadata/section/<section_id>/

Response:

 ::
    
    HTTP SUCCESS (200)
    
    {
    "logged_in_as": "bob",
    "objects_selected": 1,
    "selected": [{
        "fields": {
            "tree_position": 1,
            "odml_type": 0,
            "description": "Settings and other experimental setup",
            "current_state": 10,
            "is_template": false,
            "last_modified": "2012-04-10 15:59:00",
            "safety_level": 3,
            "date_created": "2012-04-10 15:59:00",
            "name": "Electrode"
            },
        "model": "metadata.section",
        "permalink": "/metadata/section/18"
    }],
    "message": "Here is the list of requested objects.",
    "selected_range": [0, 0],
    "message_type": "object_selected"
    }


--------------
Delete section
--------------

 ::

    DELETE /metadata/section/<section_id>/?params


parameters:
 * [force] - true, false (default) - use force=1 to delete the section even if there are other users with collaboration role (able to write). If omitted, the section will not be deleted being in the state having collaborators.

Response:

 ::

    HTTP SUCCESS (200)
    
    {
        "message": "Object was deleted.",
        "logged_in_as": "bob",
        "message_type": "deleted"
    }


-----------------------------------------
Getting list of properties in the section
-----------------------------------------

You basically have to query properties with the filter set on the particular section:

 ::
    
    GET /metadata/property/?section=<section_id>


Response:

 ::
    
    HTTP SUCCESS (200)
    
    {
    "logged_in_as": "bob",
    "objects_selected": 1,
    "selected": [
        {
            "fields": {...},
            "model": "metadata.property",
            "permalink": "/metadata/property/1"
        },
        {
            "fields": {...},
            "model": "metadata.property",
            "permalink": "/metadata/property/2"
        }
    ],
    ...
    }


-----------------------------------------------
Getting list of blocks and files in the section
-----------------------------------------------

By analogy with the previous example, these requests will look like:

 ::
    
    GET /datafiles/?section=<section_id>

and

 ::
    
    GET /electrophysiology/block/?section=<section_id>


---------------------------
Create or update a property
---------------------------

Managing propeties is similar to managing sections and is implemented inline with the :doc:`key API principles <principles>`:

 ::
    
    Request: POST /metadata/property/[<property_id>]/

    {
        "comment": "insert some comments here..",
        "definition": "the type of electrode.",
        "name": "type",
        "dependency_value": "",
        "dtype": "",
        "section": 1,
        "uncertainty": "",
        "mapping": "",
        "dependency": "",
        "safety_level": 3,
        "unit": ""
    }

Response:

 ::
    
    HTTP SUCCESS (200) or CREATED (201)
    
    {
    "logged_in_as": "bob",
    "objects_selected": 1,
    "selected": [{
        "fields": {
            "comment": "insert some comments here..",
            "definition": "the type of electrode.",
            ...
            "current_state": 10,
            "last_modified": "2012-04-10 16:18:26",
            "safety_level": 3,
            "date_created": "2012-04-10 16:18:26",
            "unit": ""
        },
        "model": "metadata.property",
        "permalink": "/metadata/property/23"
    }],
    ...
    }


------------------
Getting a property
------------------

 ::
    
    GET /metadata/property/<property_id>/


Response is very similar to the one provided above.

---------------
Delete property
---------------

 ::
    
    DELETE /metadata/property/<property_id>

This request permanently moves the property into archive.


------------------------------------
Getting list of values of a property
------------------------------------

Query values with the filter set on the particular property:

 ::
    
    GET /metadata/values/?parent_property=<property_id>


Response:

 ::
    
    HTTP SUCCESS (200)
    
    {
    "logged_in_as": "jeff",
    "objects_selected": 1,
    "selected": [
        {
        "fields": {
            "current_state": 10,
            "local_id": 1,
            "safety_level": 3,
            "owner": "/profiles/profile/5",
            "date_created": "2012-09-17T18:19:25",
            "guid": "e02f5eda3c5f5793db8d0f2fbbbee3e524e58799",
            "data": "fre",
            "parent_property": "/metadata/property/1"
        },
        "model": "metadata.value",
        "permalink": "/metadata/value/1"
        }
    ],
    "message": "Here is the list of requested objects.",
    "selected_range": [
    0,
    0
    ],
    "message_type": "object_selected"
    }


------------------------
Create or update a value
------------------------

 ::
    
    Request: POST /metadata/value/[<value_id>]/

    {
        "data": 375,
        "parent_property": 14586
    }


--------------
Delete a value
--------------

 ::
    
    DELETE /metadata/value/<value_id>

This request removes value from the associated property.

