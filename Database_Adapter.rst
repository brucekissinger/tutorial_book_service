Database Adapter Service
========================

As stated in the CloudI FAQ
`<http://cloudi.org/faq.html#7_MySQL>`_
, a CloudI database service accepts requests from other CloudI services.  The service expects SQL input and provides the query result either as Erlang data or binary encoded data based on whether the input was Erlang data ora binary SQL string.

.. index::
 single: Database Service 

Creating the MySQL Database Service
-----------------------------------

CloudI provides out-of-the-box adapters for many types of databases.  The example shown below is for a MySQL database, but the same general steps would apply for other database types.


1. Stop CloudI.
 
2. Modify the cloudi.conf stored in the 
**/usr/local/etc/cloudi/** directory and add the following lines. 

.. NOTE::
	Note that the database hostname, port, username, and password need to be changed for your environment.

::

        %
        % Book Recommendation services
        %
        {internal,
                "/db/mysql/",
               cloudi_service_db_mysql,
               [{database, "book"},
                {timeout, 20000}, % ms
                {encoding, utf8},
                {hostname, "192.168.0.5"},
                {username, "cloudi"},
                {password, "secret"},
                {port, 3306}],
               none,
               5000, 5000, 5000, undefined, undefined, 1, 5, 300, []}


.. ATTENTION::
	Notice that the username and password must be stored in plain-text in the configuration file leading to a potential security vulnerability.  In addition, the username and password is reported when the list of services is requested.  
.. NOTE::
	The name of the database adapter service will be a combination of the database type and the name listed for the ``database`` key.  In this example, the service name will be ``/db/mysql/book``.


3.  When you are done changing the cloudi.conf file, start CloudI and inspect the log files for any errors. A common error is to have an extra or missing comma at the end of the block that was added.  

4.  List the CloudI services and verify that the database is included in the list. 

::

  curl http://localhost:6467/cloudi/api/erlang/services

.. index::
 single: Erlang Shell

Testing the Service
-------------------

You can test the database service by using the CloudI module and the associated Erlang VM shell.  A simple example is shown below. Note that Erlang commands are case-sensitive and each command must be terminated with a period character.

::

  sudo cloudi attach

  Context = cloudi:new().  
  cloudi:send_sync(Context, "/db/mysql/book", "select * from items where id=1").

.. NOTE::
	To exit the Erlang shell, press the Control D keys simultaneously.
