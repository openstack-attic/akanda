# Akanda
[akanda.io](https://akanda.io)

Akanda is the only open source network virtualization solution built by OpenStack 
operators for real OpenStack clouds. Akanda eliminates the need for complex SDN 
controllers, overlays and multiple plugins for cloud networking by providing a
simple integrated networking stack (routing, firewall, load balancing) for 
connecting and securing multi-tenant OpenStack environments. 

Akanda is layer 2 agnostic and interfaces with the OpenStack Neutron REST APIs.
Akanda includes a sophisticated lifecycle management and orchestration platform
to monitor, configure, and manage 3rd party virtualized routers, load balancers
and firewalls.

----

### The Name

Project names are a powerful tool because they can be used to bond teams,
communicate effectively and convey the end goal. Like many projects, we
considered many names until a member of our team sought out to find a word
appropriate for an open project. This word enables us to say something more
clearly and with a bevy of excellent synonyms by using the Sanskrit word
अखण्ड (akhaNDa) which has such lovely connotations as "non-stop, "undivided,
"entire," "whole," and most importantly, "**not broken**."


## Subprojects

The code for the Akanda project lives in several separate repositories to ease
packaging and management:


  * *this* repository focuses on project overview and documentation

  * [Akanda Rug](https://github.com/stackforge/akanda-rug) – Orchestration
    service for managing the creation, configuration, and health of Akanda
    Software Routers in an OpenStack cloud.

  * [Akanda Appliance](https://github.com/stackforge/akanda-appliance) –
    Supporting software for the Akanda Software Router appliance, which is
    a Linux-based service VM that provides routing and L3+ services in
    a virtualized network environment. This includes a REST API for managing
    the appliance via the `Akanda Rug` orchestration service.

  * [Akanda Neutron](https://github.com/stackforge/akanda-neutron) – 
    Ancillary subclasses of several OpenStack Neutron plugins and supporting code.

  * [Akanda Horizon](https://github.com/stackforge/akanda-horizon) -
    OpenStack Horizon supporting code.


## Project Details

Akanda is publicly managed through the [Akanda Launchpad project](https://launchpad.net/akanda)


## Code Review

The code goes to get reviewed by collaborators and merged at
[OpenStack Gerrit review](https://review.openstack.org)

## Documentation

Can be found at [docs.akanda.io](http://docs.akanda.io)


## Project Status

[Though Ohloh and Blackduck](https://www.openhub.net/p/akanda)


## Community

Talk to the developers through IRC [#akanda channel on freenode.net]
(http://webchat.freenode.net/?randomnick=1&channels=%23akanda&prompt=1&uio=d4)


## License and Copyright

Akanda is licensed under the Apache-2.0 license and is Copyright 2015,
Akanda, Inc.
