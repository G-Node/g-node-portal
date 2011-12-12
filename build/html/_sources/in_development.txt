
======================
2. DATAFILE MANAGEMENT
======================

-----------------------------
2.1 Getting list of datafiles
-----------------------------

 ::

    Request: GET /datafiles/list/[<section_id>]/?params

parameters:
 * [section_id] - files in a specific section (root by default)
 * [visibility] - private, public, shared, all (default) - which types of files to return
 * [scope] - full (default), restricted - amount information about each file to return
 * [filter_criteria] - ere you can also apply different filter criteria, adding them as POST parameters. 

TBD
(as q in Google API)

 ::

    Response:
    TBD

------------------------
2.2 Getting file details
------------------------
 ::
    
    Request: GET /datafiles/<datafile_id>


 ::
    
    Response:
    TBD


(Conversion status (not able to convert, not converted, NEO))
(if convertable, provide additional statistics)

----------------------------------------------
2.3 Create datafile with a native format (NEO)
----------------------------------------------

 ::
    
    Request: POST /datafiles/create/[section_id]/?params


 * parameters:
 * [section_id] - a section id where to put the file (root by default)
 * name - file name
 * [description] - description

(be careful with ACL!! should be by default like for parent section)

 ::
    
    Response:
    TBD

---------------------------------------------------
2.4. Upload a datafile (with or without conversion)
---------------------------------------------------

 ::
    
    Request: POST /datafile/upload/[section_id]/?params


parameters:
 * [convert] - true (default), false - whether try to convert the file into native format

*Note. ACL for the new file is by default assigned as for the parent section.*

 ::
    
    Response:
    TBD

------------------------------
2.5 Modify datafile attributes
------------------------------

 ::
    
    Request: POST /datafile/<datafile_id>/?params


parameters:
 * [name] - file name
 * [description] - description
 * [convert] - true, false - if true, a request to convert the file to native format is made (asynchronous)

*Note. The conversion operation is asynchronous, which means you'll not see the status of the conversion in the response. You may use a details request (2.2) to check whether the file was converted successfully or not.*

 ::
    
    Response:
    TBD

-------------------
2.6 Export datafile
-------------------

When the file is not converted, it simply returns the originally uploaded file for download. When file is in native format, it can convert all underlying data into a file with the format specified and return it for download.[[BR]]

 ::
    
    Request: GET /datafiles/download/<datafile_id>/?params


parameters:
 * [format] - required file format

 ::
    
    Response:
    TBD

-------------------
2.7 Delete datafile
-------------------
 ::
    
    Request: DELETE /datafile/<datafile_id>/?params


parameters:
 * [force] - true, false (default) - use "true" to delete the file even if there are other users with collaboration role (able to write). If "false" or omitted, the file will not be deleted being in the state having collaborators.

 ::
    
    Response:
    TBD


----
== 3. ORGANIZE YOUR DATA - MANAGING SECTIONS & PROPERTIES ==

'''3.1 Getting list of sections'''[[BR]]

 ::
    
Request: GET /sections/list/[<section_id>]

parameters:
 * [section_id] - sections in a specific section (root by default)
 * [visibility] - private, public, shared, all (default) - which types of sections to return
 * [scope] - full (default), restricted - amount information about each section to return
 * [filter_criteria] - here you can also apply different filter criteria, adding them as POST parameters. TBD (as q in Google API)
 * [recursive] - true, false (default) - do a recursive export

 ::
    
Response:
TBD


'''3.2 Getting list of properties in a section'''[[BR]]

 ::
    
Request: GET /properties/list/[<section_id>]

parameters:
 * [section_id] - properties in a specific section (root by default)
 * [scope] - full (default), restricted - amount information about each property to return
 * [filter_criteria] - here you can also apply different filter criteria, adding them as POST parameters. TBD (as q in Google API)

 ::
    
Response:
TBD


'''3.3 Create or update a section'''
 ::
    
Request: POST /sections/<section_id>/?params


parameters:
 * [where] - a section id where to put new section (root by default)
 * [section_id] - if provided, update is performed (when found); new object is created otherwise in [where]
 * name - section name, required if creating new section
 * type - type of the section according to the odML methodology. Required when creating new section. The list of types TBD here.
 * [link]       = some odML parameter
 * [repository] = some odML parameter
 * [mapping]    = some odML parameter
 * [reference]  = some odML parameter

''Note. ACL for the file is by default assigned as for the parent section.''

 ::
    
Response:
TBD


'''3.4 Getting section details'''

 ::
    
Request: GET /sections/<section_id>


 ::
    
Response:
TBD


'''3.5 Delete section'''

 ::
    
Request: DELETE /sections/<section_id>/?params


parameters:
 * [force] - true, false (default) - use "true" to delete the file even if there are other users with collaboration role (able to write). If "false" or omitted, the file will not be deleted being in the state having collaborators.


 ::
    
Response:
TBD



'''3.6 Create or update a property in section'''

 ::
    
Request: POST /properties/[section_id]/?params


parameters:
 * [where] - a section id where to put new property (root by default)
 * [property_id] - if provided, update is performed (when found); new object is created otherwise in [where]
 * name - property name
 * type - type of the property values, according to the odML methodology. The list of types TBD here.
 * value[, value] - value(s) of the property
 * [unit] - units of the property
 * [definition] - some odML property attribute
 * [mapping] - some odML property attribute
 * [dependency] - some odML property attribute


 ::
    
Response:
TBD



'''3.7 Getting property'''

 ::
    
Request: GET /properties/<property_id>



 ::
    
Response:
TBD



'''3.8 Delete property'''

 ::
    
Request: DELETE /properties/<property_id>


 ::
    
Response:
TBD


'''3.9 Section - copy & move'''

 ::
    
Request: POST /sections/copy/?params


parameters:
 * source_section_id - section to copy
 * target_section_id - a section to copy into
 * [mode] - move (default), copy - whether to move or copy
 * [with_properties] - whether or not to copy properties, which belong to the section (in copy mode)
 * [with_datafiles] - whether or not to copy links (!!) to datafiles, which belong to the section (in copy mode).
 * [recursive] - true, false - copy all subsections recursively (in copy mode)

''Note. Files themselves are never duplicated while using copy/move function.''


 ::
    
Response:
TBD



'''3.10 Get the root of the section? TBD'''[[BR]]

'''3.11 Get all sections as a tree? TBD'''[[BR]]


----

== 4. MANAGE PERMISSIONS ==

Datafiles and sections sharing is controlled via the access control lists. Access control lists are just basic lists that show who has access to a given resource. In the ACLs, the following roles are available for a given document or folder:
 * owner — the owner of the resource (section or file). As an owner you have the ability to modify the ACL, delete the file, etc.
 * writer — a collaborator.
 * reader — a viewer (equivalent to read-only access).
A resource (datafile or section) by itself has also a sharing state, which can be 
 * public — all users may see the contents of the resource.
 * friendly — all friends have a reader role, with no need to assign them for every resource.
 * private — noone, except users, specified explicitly, may see the resource.
With no dependence on the state, people, assigned explicitly by owner of the resource as readers or writers, have corresponding access to the resource.

By <resource_type> in this paragraph we assume either "sections", either "datafiles".

'''4.1 Getting resource permissions'''

 ::
    
Request: GET /<resource_type>/acl/<resource_id>/


 ::
    
Response:
TBD


'''4.2 Set resource state (public, friendly, private)'''

 ::
    
Request: POST /<resource_type>/acl/<resource_id>/?param


parameters:
 * state - public, friendly, private - resource safety level
 * [recursive] - true, false (default) - apply to all resources recursivery (when resource_type is sections)

 ::
    
Response:
TBD


'''4.3 Share resource with a user'''

 ::
    
Request: POST /<resource_type>/acl/share/<resource_id>/?param


parameters:
 * user_id - a user to add to the share
 * role - reader, writer - a role for this user
 * [recursive] - true, false (default) - apply to all resources recursivery (when resource_type is sections)

''Note. A user specified will be added to the list of collaborators (readers).''


 ::
    
Response:
TBD



'''4.4 Retreat resource to completely private state'''

 ::
    
Request: POST /<resource_type>/acl/retreat/<resource_id>/?param


parameters:
 * [recursive] - true, false (default) - apply to all resources recursivery (when resource_type is sections)


 ::
    
Response:
TBD



'''4.5 Remove a user from resource share'''

 ::
    
Request: POST /<resource_type>/acl/unshare/<resource_id>/?param


parameters:
 * user_id - a user to remove from the share
 * [recursive] - true, false (default) - apply to all resources recursivery (when resource_type is sections)


 ::
    
Response:
TBD



'''4.6 Remove a user from ALL shares'''

 ::
    
Request: POST /<resource_type>/acl/disgrace/<user_id>


parameters:
 * user_id - a user for total remove from any sharing


 ::
    
Response:
TBD


----

== 5. SEARCH USING G-NODE API ==

TBD


----



'''2.7 Computations with specified objects'''

 ::
    
Request: POST /electrophysiology/<operation>/?params


parameters:
 * operation - an operation to perform
 * object_type - analogsignal or spiketrain
 * object_list - a tuple of object ids
 * [query_string] - if defined, a query string will be used to select object for computation. object_list will be ignored.
 * datafile_id - where to save the results


 ::
    
Response:
TBD



'''2.8 Delete an object'''

 ::
    
Request: DELETE /electrophysiology/?params


parameters:
 * obj_type - type of the object
 * obj_id - object id


 ::
    
Response:
TBD




----

== 7. RESOURCE ACTION HISTORY (draft) ==

In order not to forget, how certain scientific computations were achieved, or what is the source of a certain analog signal, you may request a history of an action with objects. An action history is a table indicating a source for an operation, description of an operation made and a result.

'''7.1 Get object action history'''

 ::
    
Request: GET /history/object/?params


parameters:
 * obj_type - type of the object
 * obj_id - object id
 * [start_date] - start date of the history
 * [end_date] - start date of the history


 ::
    
Response:
TBD



'''7.2 Get user action history'''

 ::
    
Request: GET /history/user/?params


parameters:
 * user_id - object id
 * [start_date] - start date of the history
 * [end_date] - start date of the history


 ::
    
Response:
TBD



''Note. Usually you will deal with the following HTTP status codes:''

201 - "Created" - new object was successfully created.
200 - "OK" - the object was successfully updated or GET operation performed successfully.
404 - "Not Found" - you have provided an [obj_id], however, such an object does not exist. Or URL is wrond and not supported.
403 - "Forbidden" - you don't have access to create, modify or view an object.
400 - "Bad request" - some of the request parameters are not provided correctly. Consider the "message" contents.
401 - "Unauthorized" - authorization key not provided.


----

----

FUTURE:
 * archiving mechanisms
 * include links in respresentations (http://www.infoq.com/articles/rest-anti-patterns)
 * caching!! see google + http://www.mnot.net/cache_docs/
 * etags???
 * version management!!!
 * import / conversion using Google Refine technologies
 * (SOLVED) NEO: /electrophysiology/info/<neo_id>/ - short info: think about what kind of info could there be, like number of segments/signals in a block, total data in MB etc.
 * NEO: as above, in /electrophysiology/select/<obj_type>/ - provide short info about every object + a summary about all (total volume, for example)
 * NEO: /electrophysiology/select/<obj_type>/ add query mechanism
 * (SOLVED) NEO: if an array data is missing for data-related objects, should we throw an error? or just allow a user to create it? Throw an error
 * (SOLVED) NEO: should we assign units by default, if not provided? NO
 * NEO security: based on files? or not?
