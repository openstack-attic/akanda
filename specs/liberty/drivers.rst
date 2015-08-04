Akanda Drivers
==============
Requirements dictate that the Rug be capable of orchestrating high level network
services such as Load Balancing and Firewall in service instances. To accomplish
this we need to abstract much of the current behavior of the rug to use a driver
to perform much of its work. In this context a driver is the abstraction of
certain behaviors such as configuration and health monitoring into a modular
driver class.


Problem Description
===================
Currently the rug is hard coded to manage router instances. A reworking of many
parts of the code is required to enable the rug to orchestrate things
other than neutron routers.

Hard coded router management;
    * instance_manager.py
    * worker.py
    * populate.py
    * scheduler.py
    * state.py
    * tenant.py
    * debug.py
    * commands.py
    * main.py
    * api/nova.py
    * cli/browse.py

In addition to code in these areas needing to be abstracted. The tests written
to cover these areas of the codebase will have to be reworked.

Proposed Change
===============
Identify common portions of code that should remain consistent between higher
level services (routing, load balancing, firewall, vpn). Then abstract all other
behavior into a router driver.

Common behaviors;
    * life cycle state
    * base networking config
    * worker population
Router driver specific behavior;
    * vrrp
Shared behavior with different procedures;
    * health monitoring
    * interactions with neutron
    * configuration management

Data Model Impact
-----------------
N/A

REST API Impact
---------------
APIs should remain consistent.

Security Impact
---------------
This change should have no impact on security.

Notifications Impact
--------------------
N/A

Other End User Impact
---------------------
N/A

Performance Impact
------------------
N/A

Other Deployer Impact
---------------------
This change will have several more configuration options and will ultimately
expand deployers options with our software.

Developer Impact
----------------
Once finished this will provide a flexible harness for developers to interface
new behaviors with.

Community Impact
----------------
This change will open the Rug up to allow it to manage any high level service
which will invite participation from across the community in ways that cannot
be predicted.

Alternatives
------------
At this time no viable alternatives have been suggested.

Implementation
==============

Assignee(s)
-----------
David Lenwell - davidlenwell

Work Items
----------
This is a complex change. Please forgive the following hand waving.

    * Create abstract driver class that illustrates the minimum functionality of
      an akanda driver.
    * Create router driver class that inherits base object.
        * move router specific code into the driver object.
    * Create driver factory class in driver init to ease selection of drivers
      and error handling.
    * Modify code in populate.py to cycle through driver types and populate
      workers for each logical resource type.
    * Modify browse and debug cli interfaces so that they can display and debug
      any higher level service in an instance.
    * Fix tests and expand coverage for drivers.


Dependencies
============
N/A

Testing
=======
Many of the unit tests will require a rework.

Tempest Tests
-------------

Functional Tests
----------------
Should have no effect on functional tests.

API Tests
---------
The api should remain consistent.

Unit Tests
----------
Many of the unit tests will need to be reworked.

Documentation Impact
====================

User Documentation
------------------
N/A

Developer Documentation
-----------------------
New documentation around the use of "built in" drivers as well as documentation
for creating new drivers will be required.


References
==========
