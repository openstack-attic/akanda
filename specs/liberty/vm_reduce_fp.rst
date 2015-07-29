This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.

 http://creativecommons.org/licenses/by/3.0/legalcode


Title of your blueprint
=======================

Reduce VM Footprint

Problem Description
===================

Currently, the Service VM image is prepared out of stable
debian (wheezy) image, and integrating the necessary components
of akanda-rug, akanda-applicance and other necessary librararies /
packages. With this the size of the image is around 460 MegaBytes.
And as features get supported as part of the Service VM, it is
imperative that it will keep growing.

Some of the side effects of this growth could result in:
a. Longer duration of spawning the VMs because of copying to be done,
b. Increased storage requirements
c. Possible side effect in the form of affecting the performance


Proposed Change
===============
The approach that would be considered as part of this blue print is
to identify all the packages / libraries that would not be relevant,
marked as "recommended" could be done away with it. The goal is to
have the Service VM be a consumer of the OS and Akanda not take
ownership of maintaining the release software libraries / packages.
The strategy is to build the Service VM image as it is followed now,
and identify the packages / libraries that are not relevant to be
stripped-off before the final 'qcow2' image preparation.


Data Model Impact
----------------

n/a

REST API Impact
---------------

n/a

Security Impact
---------------

To be verified

Notifications Impact
--------------------


Other End User Impact
---------------------

n/a

Performance Impact
------------------
As part of the Service VM size reduction, an effort will also be spent
in analyzing the performance impact of such a change.


Other Deployer Impact
---------------------

n/a

Developer Impact
----------------

n/a

Community Impact
----------------

n/a


Alternatives
------------
The other alternative that was considered included hand-picking the libraries/
packages and then prepare the final Service VM. This approach would be detrimental
in the longer run, as the onus of maintaining compatibility may fall on Akanda Dev
team, which is an avoidable over head to be taken.

Implementation
==============
As outlined in the "Proposed Change" section, all the 'Recommended' packages will
be stripped out before the final service vm qcow2 image preparation.


Assignee(s)
-----------


Work Items
----------
* Identify all the packages / libraries that are deemed optional.
* Strip those identified libraries / packages in the previous step, out of the
final service vm image.
* Verify that the Service VM is successfully installed as part of the Dev stack image
* Log-into the Service VM and perform standard operations that is expected of the
Service VM.
* Perform if possible scenario for rebuilding, reloading the Service VM.


Dependencies
============


Testing
=======

Tempest Tests
-------------
* Current gated tests will be performed on the Image before publishing the changes upstream.

Functional Tests
----------------


API Tests
---------
n/a

Documentation Impact
====================

User Documentation
------------------
n/a

Developer Documentation
-----------------------
n/a

References
==========
