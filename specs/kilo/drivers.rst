Akanda Drivers
==============
Business requirements dictate that the Rug be capable of orchestrating high
level network services. To accomplish this we need to abstract much of the
current behavior into a driver. In this context a driver is the abstraction of
certain behaviors such as configuration and health monitoring into a modular
driver class.

Problem Description
===================
Currently the Rug is hard coded around managing router instances. A reworking
of many parts of the code are required to enable the rug to orchestrate things
other than neutron routers.

Proposed Change
===============
Identify common portions of code that should remain consistent between higher
level services (routing,load balancing, vpn). Then abstract all other behavior
into a new router driver.

Common behaviors;

    * instance power state
    * base networking config

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
The api should remain consistent.


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
N/A

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

