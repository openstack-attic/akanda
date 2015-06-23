..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.

 http://creativecommons.org/licenses/by/3.0/legalcode


Appliance Instance Pooling
==========================


Problem Description
===================

The RUG currently boots Nova instances on-demand as they are required.  The
majority of time spent provisioning new router appliances is spent waiting for
the Nova instance to build, come up and initialize.  Some operators would
prefer router appliances get provisioned from a pool of pre-provisioned hot
stand-bys. This would cut provisioning time of new routers significantly, and
make it much easier to push out new appliance images.

Proposed Change
===============

This proposes adding a new subprocess to the RUG that will manage a pool
of pre-provisioned appliance instances. The size of this pool will be configurable
and configuring the size to be 0 would preserve the current RUG behavior of
provisioning instances on demand.

We will call this new process the Pez Dispencer, or Pez for short.  Its
two primarily responsibilities include keeping the pool of nodes full
and providing consumers with nodes when requesting from the pool. When a node
is removed from the pool on request of a consumer, it is no longer the
responsibility of Pez.

Since we assume we'll be running in a multi-Rug world, the Pez will be its
own standalone service process and not run as a sub-process of the Rug.
It will communicate with Rug workers via the RPC bus via oslo.messaging.

An unused node in the pool will consist of a Nova instance booted from the
currently configured router image, spawned and initialized to the point where
its REST API server is up and listening on the management network. The
management network will be the only network attached to unused nodes.  The RUG
currently searches for a router's corresponding Nova instance by name (ie,
ak-$router_id). Pez will similarly differentiate between used and unused nodes
by name. Unused nodes will, by default, be named ak-unused.  When a consumer
requests a node, Pez will update its name accordingly before handing it off
to the consumer and spawning a new standby.

We will need to add a layer of abstraction around the current Insance Manager
``akanda.rug.instance_manager.InstanceManager`` and its integration points with
the state machine. Instead of requesting node resources directly from the
VmManager, the state machine will instead interface with Pez. Pez itself
should be modular in its backend node provider such that it could be
extended in the future to provide nodes from things other than Nova.
It should also understand different resource types in addition to routers
(ie, load-balancers, firewalls, etc). Thus, a request for a router appliance
in a Nova Instance would would look something like:

 Pez.get_node(appliance_type="vm", resource_type="router")

The resource returned by Pez will need to be a new class that wraps the current
InstanceManager and exposes the main methods used by the state machine, namely:
update_state(), replug(), stop(), configure().  New sub-classes can be created
from this new class to introduce different resource types to the RUG (ie,
load-balancers).

The Pez class will implement all required RPC client functionality.

Finally, the rug-ctl should expose some new commands in a new group of "pool" 
commands, ie:

* rug-ctl pool debug - Dump current pool statistics to the log (ie, node count)
* rug-ctl pool flush - Flush/terminate entire pool and rebuild, useful for
  upgrading appliances.

Data Model Impact
-----------------

n/a

REST API Impact
---------------

n/a

Security Impact
---------------

n/a

Notifications Impact
--------------------

n/a

Other End User Impact
---------------------

n/a

Performance Impact
------------------

The pool of unused nodes will contain a number of unused, idle Nova instances.
This may be a concern for operators with limited compute capacity and the
size should be tuned accordingly, or the pool disabled by a configured size of
0.

Other Deployer Impact
---------------------

The RUG will be spawning an additional subprocess.

Developer Impact
----------------

n/a

Community Impact
----------------

n/a

Alternatives
------------

The original idea was to leverage the nodepool project to do this, but that
project is heavily geared toward a CI usecase, with a hard-dependency on
Jenkins as well as ZeroMQ.

Implementation
==============

Assignee(s)
-----------


Work Items
----------

* Create a pez module that will spawn as a process and manage the pool
of nodes as per configuration.

* Expose Pez as a service listening on the RPC bus.

* Implement the Pez client-side component that will be used by the main
Rug.

* Decouple state machine from InstanceManager and have it instead request
resources from Pez, and use the returned resource for lifecycle management.

* Add rug-ctl commands to provide some diagnostic information about the
pool.

Dependencies
============


Testing
=======

Tempest Tests
-------------


Functional Tests
----------------


API Tests
---------


Documentation Impact
====================

User Documentation
------------------


Developer Documentation
-----------------------


References
==========

