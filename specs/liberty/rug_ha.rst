..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.

 http://creativecommons.org/licenses/by/3.0/legalcode


Title of your blueprint
=======================

Rug HA and scaleout

Problem Description
===================

The RUG is a multi-process, multi-worker service but it be cannot be
scaled out to multiple nodes for purposes of high-availability and
distributed handling of load.  The only currently option for a
highly-available is to do an active/passive cluster using Pacemaker
or similar, which is less than ideal and does not address scale-out
concerns.

Proposed Change
===============

This proposes allowing multiple RUG processes to be spawned across
many nodes.  Each RUG process is responsible for a fraction of the
total running appliances.  RUG_process->appliance(s) mapping will be
managed by a distributed hash table. One of the many RUG processes
will be elected a leader and responsible for managing the DHT and
coordinating it with the other nodes.  When a leader dies, another
node will be elected leader and the hash table rebalanced.

This allows operators to scale out to many RUG instances, eliminating
the single-point-of-failure and allowing appliances to be evenly
distributed across multiple worker processes.


Data Model Impact
-----------------

n/a

REST API Impact
---------------

n/a

Security Impact
---------------

None

Notifications Impact
--------------------


Other End User Impact
---------------------

n/a

Performance Impact
------------------

There will be some new overhead introduced the messaging layer as Neutron
notifications and RPCs will need to be distributed to per-RUG message queue.

Other Deployer Impact
---------------------

Deployers will need to evaluate and choose an appropriate backend to be used
by tooz for leader election.  memcached is a simple yet non-robust solution,
while zookeeper is a less light-weight but proven one.  More info at [2]

Developer Impact
----------------

n/a

Community Impact
----------------

n/a


Alternatives
------------

One alternative to having each RUG instance declare its own messaging queue and
inspect all incoming messages would be to have the DHT master also serve as a
notification master. That is, the leader would be the only instance of the RUG
listening to and processing incoming Neutron notificatons, and then
re-distributing them to specific RUG workers based on the state of the DHT.

Another option would be to do away with the use of Neutron notifications
entirely and hard-wire the akanda-neutron plugin to the RUG via a dedicated
message queue.


Implementation
==============

This proposes enabling operators to run multiple instances of the RUG.
Each instance of the RUG will be responsible for a subset of the managed
appliances.  A distributed hash table (DHT) will be used to map appliances
to their respective RUG instance. The Ironic project is already doing
something similar and has a hashring implementation we can likely leveage
to get started [1]

Among the cluster of RUG processes, one will be elected a leader using an
external coordination service (ie, zookeeper). We can leverage python-tooz
to provide an API for leader election that functions ontop of multiple
coordination server technologies. This node will serve as the elected DHT
leader and only be responsible for managing and distributing the DHT's hash
key to the other nodes (via RPC).  If this leader goes offline, a new leader
election will take place and the new DHT leader will rebalance the hash table
and propagate to remaining nodes.

Each RUG process will declare its own message queue that will receive
event notifications and RPCs from Neutron.  Event processing code will need
to be extended in a way that allows workers to discard messages that are not
relevant to any of the appliances it is managing (according the DHT).

Note that there is some new overhead here in that we are replicating
RPC messages for every new worker that comes online. Also, each new worker
will be spending cycles examaining the payload of all incoming messages, even
though it will only care about a small fraction of those that are received.
This is a trade off between some increased overhead and the added complexity of
making the elected leader reponsible for incoming event processing and
distribution to non-leaders.

Assignee(s)
-----------


Work Items
----------

* Setup RUG worker to listen on a per-host message queue

* Introduce leader election using python-tooz

* Implement a distributed hash table for managing worker:appliance
assignment from the leader node and re-distribution of hashing data
to peers

* Add logic to the RPC layer to discard messages that are not relevant
to any of the appliances managed by the worker that receives the message

Dependencies
============

This work is dependent on migration to oslo.messaging, addressed by
https://review.openstack.org/#/c/190401/


Testing
=======

Tempest Tests
-------------


Functional Tests
----------------

If we cannot sufficiently test this using unit tests, we could potentially
spin up our devstack job with multiple copies of the akanda-rug-service
running on a single host, and having multiple router appliances.  This
would allow us to test ring rebalancing by killing off one of the multiple
akanda-rug-service processes.

API Tests
---------


Documentation Impact
====================

User Documentation
------------------

Deployment docs need to be updated to mention this feature is dependent
on an external coordination service.

Developer Documentation
-----------------------


References
==========

[1] https://git.openstack.org/cgit/openstack/ironic/tree/ironic/common/hash_ring.py
[2] http://docs.openstack.org/developer/tooz/drivers.html

