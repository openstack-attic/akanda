Installing Akanda
=================


Deploying Akanda using All-In-One Single Instance DevStack (Developer QuickStart)
---------------------------------------------------------------------------------

These steps provide guidance for new developers looking to get up and running
with an Akanda development environment. The Akanda components may be easily
deployed alongside OpenStack using DevStack. For more information about
DevStack visit ``http://docs.openstack.org/developer/devstack/guides/single-machine.html``.

Preparation and Prerequisites
+++++++++++++++++++++++++++++

Deploying DevStack on your local workstation is not recommended. Instead,
developers should use a dedicated virtual machine.  Using VirtualBox
``https://www.virtualbox.org/wiki/Downloads`` for running locally or a public
hosting provider such as DreamHost ``https://www.dreamhost.com/cloud/computing/``
for running remotely is recommended. Currently, Ubuntu Trusty 14.04 is the
tested and supported base operating system. Additionally, you'll need at least
8GB of RAM and to have ``git`` installed::

    sudo apt-get -y install git


First clone the DevStack repository::

    sudo mkdir -p /opt/stack/
    sudo chown `whoami` /opt/stack
    git clone https://git.openstack.org/openstack-dev/devstack /opt/stack/devstack


Configuring DevStack
++++++++++++++++++++

Next, you will need to enable the Akanda plugin in the DevStack configuration
and enable the relevant services::

    cat >/opt/stack/devstack/local.conf <<END
    [[local|localrc]]
    enable_plugin akanda-rug https://github.com/stackforge/akanda-rug
    enable_service q-svc q-agt ak-rug
    disable_service n-net

    HOST_IP=127.0.0.1
    LOGFILE=/opt/stack/devstack/devstack.log
    DATABASE_PASSWORD=secret
    RABBIT_PASSWORD=secret
    SERVICE_TOKEN=secret
    SERVICE_PASSWORD=secret
    ADMIN_PASSWORD=secret
    END

Deploying
+++++++++

Simply run DevStack and allow time for the deployment to complete::

    cd /opt/stack/devstack
    ./stack.sh

After it has completed, you should have a ``akanda-rug`` process running
alongside the other services and an Akanda router appliance booted as a Nova
instance.

Deploying Akanda using Multi-Node DevStack
------------------------------------------

These steps provide guidance for new developers looking to get up and running
with an Akanda development environment. The Akanda components may be easily
deployed alongside OpenStack using DevStack. For more information about
DevStack visit ``http://docs.openstack.org/developer/devstack/guides/multinode-lab.html``.

Control Node, Preparation and Prerequisites
+++++++++++++++++++++++++++++++++++++++++++

Follow Preparation and Prerequisites section from All-In-One Single Instance
DevStack

Control Node, Configuring DevStack
++++++++++++++++++++++++++++++++++
cat >/opt/stack/devstack/local.conf <<END
[[local|localrc]]
enable_plugin akanda-rug https://github.com/stackforge/akanda-rug
enable_service q-svc q-agt ak-rug
disable_service n-net

HOST_IP=10.0.1.10
FLAT_INTERFACE=eth0
FIXED_RANGE=10.0.2.0/20
FIXED_NETWORK_SIZE=4096
FLOATING_RANGE=10.0.3.0/25
MULTI_HOST=1
LOGFILE=/opt/stack/devstack/devstack.log
DATABASE_PASSWORD=secret
RABBIT_PASSWORD=secret
SERVICE_TOKEN=secret
SERVICE_PASSWORD=secret
ADMIN_PASSWORD=secret
END

Control Node, Deploying
+++++++++++++++++++++++

Simply run DevStack and allow time for the deployment to complete::

    cd /opt/stack/devstack
    ./stack.sh

After it has completed, you should have a ``akanda-rug`` process running
alongside the other services and an Akanda router appliance booted as a Nova
instance.

Compute Node, Preparation and Prerequisites
+++++++++++++++++++++++++++++++++++++++++++

Follow Preparation and Prerequisites section from All-In-One Single Instance
DevStack

Control Node, Configuring DevStack
++++++++++++++++++++++++++++++++++
    cat >/opt/stack/devstack/local.conf <<END
    [[local|localrc]]
    HOST_IP=10.0.1.11
    FLAT_INTERFACE=eth0
    FIXED_RANGE=10.0.2.0/20
    FIXED_NETWORK_SIZE=4096
    FLOATING_RANGE=10.0.3.0/25
    MULTI_HOST=1
    LOGFILE=/opt/stack/logs/stack.sh.log
    ADMIN_PASSWORD=labstack
    MYSQL_PASSWORD=supersecret
    RABBIT_PASSWORD=supersecrete
    SERVICE_PASSWORD=supersecrete
    SERVICE_TOKEN=xyzpdqlazydog
    DATABASE_TYPE=mysql
    SERVICE_HOST=10.0.1.10 # this is the controller node
    MYSQL_HOST=$SERVICE_HOST
    RABBIT_HOST=$SERVICE_HOST
    GLANCE_HOSTPORT=$SERVICE_HOST:9292
    ENABLED_SERVICES=n-cpu,n-net,n-api-meta,c-vol
    NOVA_VNC_ENABLED=True
    NOVNCPROXY_URL="http://$SERVICE_HOST:6080/vnc_auto.html"
    VNCSERVER_LISTEN=$HOST_IP
    VNCSERVER_PROXYCLIENT_ADDRESS=$VNCSERVER_LISTEN
    END

Control Node, Deploying
+++++++++++++++++++++++

DevStack and allow time for the deployment to complete::

    cd /opt/stack/devstack
    ./stack.sh


Building a Custom Service Instance
----------------------------------

By default, the Akanda plugin downloads a pre-built official Akanda image.  To
build your own from source, enable ``BUILD_AKANDA_APPLIANCE_IMAGE`` and specify
a repository and branch to build from::

    cat >>/opt/stack/devstack/local.conf <<END

    BUILD_AKANDA_APPLIANCE_IMAGE=True
    AKANDA_APPLIANCE_REPO=http://github.com/stackforge/akanda-appliance.git
    AKANDA_APPLIANCE_BRANCH=master
    END

To build the appliance using locally modified ``akanda-appliance`` code, you
may point devstack at the local git checkout by setting the
AKANDA_APPLIANCE_DIR variable.  Ensure that any changes you want included in
the image build have been committed to the repository and it is checked out
to the proper commit.
