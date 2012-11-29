==================
MANAGE PERMISSIONS
==================

There is a possibility to share your data with other users in the G-Node Portal. To get an overview of object sharing consider the :ref:`nice example in the overview <sharing_overview>`.

----------------------------
Getting resource permissions
----------------------------

For objects, supporting sharing, you may request an **object state** together with :ref:`ACL <acl_term>` by requesting: 

::
    
    GET /electrophysiology/<resource_type>/<resource_id>/acl/

Response:

::
    
    Response: HTTP SUCCESS (200)

    {
        "message": "Here is the list of requested objects.",
        "logged_in_as": "/profiles/profile/5",
        "message_type": "object_selected",
        "safety_level": <access_level>, # 1 = public, 2 = friendly, 3 = private
        "shared_with": {
            "<user_id>": <user_permissions>, # 1 = read, 2 = read and write
            "<user_id>": <user_permissions>
        }
    }


Of course, getting just an **object owner** and **safety level** can be easy as :ref:`usual single object GET request <get_single_object>`:

::

    Request: GET /<namespace>/<object_type>/<obj_id>/


You'll find corresponding fields in the response as:


::

    Response: HTTP SUCCESS (200)

    {
        ...
        "selected": [{
            "fields": {
                ...
                "safety_level": 2,
                "owner": "/profiles/profile/5",
            },
            ...
        }],
        ...
    }




.. _update_acl:

-----------------------------------
Update resource ACL (share/unshare)
-----------------------------------

To set particular ACL send the following HTTP POST:

::
    
    POST /electrophysiology/<resource_type>/<resource_id>/acl/?params

    {
        "safety_level": "<access level>", # 1: public, 2: friendly, 3: private
        "shared_with": {
            "<user_id>": "<user_permissions>", # 1: read, 2: read and write
            "<user_id>": "<user_permissions>"
        }
    }

parameters:
 * [cascade] - true, false (default) - apply to all objects recursivery. All nested objects will be also updated.

In development:
 * [notify] - true, false (default?) - users will be notified per e-mail


::
    
    Response: HTTP SUCCESS (200)

    {
        "message": "Here is the list of requested objects.",
        "logged_in_as": "/profiles/profile/5",
        "message_type": "object_selected",
        "safety_level": "<access level>", # 1: public, 2: friendly, 3: private
        "shared_with": {
            "<user_id>": "<user_permissions>", # 1: read, 2: read and write
            "<user_id>": <user_permissions>
        }
    }

---------------
Bulk ACL update
---------------

We consider developing this feature. Please drop a short email to the support@g-node.org to let us know that you need it. It will make the development much faster.


