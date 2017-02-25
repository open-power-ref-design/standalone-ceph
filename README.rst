=====================================
Standalone Ceph Block Storage Cluster
=====================================

General information about Ceph Block Storage can be found at:

    - http://docs.ceph.com/docs/jewel/

This repository provides the following:

    - `Bill of Materials <https://github.com/open-power-ref-design/standalone-ceph/blob/master/standalone-ceph.pdf>`_
    - `Deployment configuration file <https://github.com/open-power-ref-design/standalone-ceph/blob/master/config.yml>`_

The Bill of Materials document provides a description and representation of Database
as a Service that is tuned for OpenPOWER servers.  It provides information
such as model numbers and feature codes to simplify the ordering process
and it provides racking and cabling rules for the preferred layout of
servers, switches, and cables.

The Deployment configuration file provides a mapping of servers and switches
to software for the purposes of deployment.  Each server is mapped to a set
of OpenStack based software roles constituting the control plane
and storage plane.  Each role is defined in terms of operating system
based resources such as users and networks that need to be configured
to satisfy that role.

The Deployment configuration file needs to be edited so that it reflects the
configuration that is to be installed.  This is mostly a matter of making sure
that the numbers of servers represented match the number of servers to be 
installed and that IP addresses are allocated so that the installation is 
properly integrated into the data center.   

The installation process is split into two parts::

    > Bare metal installation of Linux 
    > Installation of OpenStack, Ceph storage, and Operational Management

The Deployment configuration file is fed into the bare metal installation
process which is performed by cluster-genesis.  The operating system is loaded
and configured as specified in the configuration file.  Users, networks, and
switches are configured during this step.  The last step is to invoke a small
script that installs OpenStack and Ceph which is performed by os-services.

More properly, in the bare metal installation step, only the installation tools
for OpenStack and Ceph were installed, not the actual services.  The next step
is to configure these tools, so that they install the actual services in a
prescribed manner so that they fit properly in the data center.  The two 
projects are os-services and ceph-services.  See the README files of each project
to determine what is required here.

The final step is to invoke cluster-create.sh in the os-services
repository to install and configure the cluster.  os-services orchestrates
the installation process of OpenStack, Ceph, and Operational Management which are
loaded into the first controller that is setup by cluster-genesis.

The OpenStack dashboard may be reached through your browser:

https://<ipaddr or hostname of an OpenStack control node>

This recipe also includes an operational management console which is
integrated into the OpenStack dashboard.  It monitors the cloud infrastructure
and shows metrics relates to the capacity, utilization, and health of the 
cloud infrastructure.  It may also be configured to generate alerts when 
components fail.  It is provided through the opsmgr repository.

.. Hint:: 
   Only os-services must be configured before invoking create-cluster.  For 
   more info, see related projects below.

   Passwords may be found in /etc/openstack_deploy/user_secrets*.yml on 
   the first OpenStack controller node.

Getting Started
---------------

The toolkit runs on an Ubuntu 16.04 OpenPOWER server or VM that is connected
to the internet and management switch in the cluster to be configured.

#. Read `standalone-ceph.pdf <https://github.com/open-power-ref-design/standalone-ceph/blob/master/standalone-ceph.pdf>`_

#. Rack and cable hardware as indicated

#. Get a local copy of this repository::

   $ git clone git://github.com/open-power-ref-design/standalone-ceph
   $ cd standalone-ceph
   $ TAG=$(git describe --tags $(git rev-list --tags --max-count=1))
   $ git checkout $TAG
   $ CFG=$(pwd)/config.yml

#. Edit the configuration file:

   Instructions for editing the file are included in the file itself.

   Additional information may be found in the
   `Cluster Genesis User Guide <http://cluster-genesis.readthedocs.io/en/latest/>`_

#. Validate the configuration file::

   $ git clone git://github.com/open-power-ref-design/os-services
   $ cd os-services
   $ git checkout $TAG
   $ ./scripts/validate_config.py --file $CFG
   
#. Place the configuration file::

   $ git clone git://github.com/open-power-ref-design-toolkit/cluster-genesis
   $ cd cluster-genesis
   $ git checkout 1.1.0
   $ cp $CFG .

#. Invoke cluster-genesis to perform the bare metal installation process:

   Instructions may be found in Cluster Genesis User Guide identified above.

#. Wait for cluster-genesis to complete, ~3 hours:

#. Edit the OpenStack Installer configuration file:

   OpenStack installation is performed by openstack-ansible.  Instructions 
   for editing the user configuration files of OpenStack is described in 
   general terms in os-services.

#. Invoke the toolkit again to complete the installation::

   $ ./scripts/create-cluster

   Note this command is invoked on the first controller node.  The commands
   listed above are invoked on the deployer node.  When cluster-genesis completes,
   it displays on the screen instructions for invoking the command above. 

Related projects
----------------

Recipes for OpenPOWER servers are located here:

    - `Recipe directory <https://github.com/open-power-ref-design/>`_

Here, you will find several OpenStack based recipes:

    - `Private cloud w/ and w/o Swift Object Storage <https://github.com/open-power-ref-design/private-compute-cloud/blob/master/README.rst>`_
    - `Database as a Service (OpenStack Trove) <https://github.com/open-power-ref-design/dbaas/blob/master/README.rst>`_
    - `Standalone Swift Clusters (OpenStack Swift) <https://github.com/open-power-ref-design/standalone-swift/blob/master/README.rst>`_

The following projects provides services that are used as major building blocks in
recipes:

    - `cluster-genesis <https://github.com/open-power-ref-design-toolkit/cluster-genesis>`_
    - `os-services <https://github.com/open-power-ref-design-toolkit/os-services>`_
    - `ceph-services <https://github.com/open-power-ref-design-toolkit/ceph-services>`_
    - `opsmgr <https://github.com/open-power-ref-design-toolkit/opsmgr>`_

