==============
AUTHENTICATION
==============

*Note. You maybe want to just try the API from the usual web-browser (like Firefox, Chrome, Safari etc.). In this case you may just go to the* `login page`_ *, fill in the login form and submit to authenticate. After successful authentication your sessionid will be stored in the browser, and you may use the address bar (or any REST-client, for example* `Postman REST Client for Chrome`_ *) to query the API.*

.. _Postman REST Client for Chrome: https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm
.. _login page: http://test.g-node.org/account/login/

In general, for authenticatation, please use the following request:

 ::

    Request: POST /account/authenticate/

    POST parameters:
     * username - user account name
     * password - user password

A successful response will contain a cookie called *sessionid*. Use this cookie within every request to work with the API.

To reset the session, send the GET request to the

 ::

    Request: GET /account/logout/

Previously acquired cookie will no longer work.

