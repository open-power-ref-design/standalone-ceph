=======================================
Standalone Ceph Cluster (Block storage)
=======================================

General information about Ceph Block Storage can be found at:

    - http://docs.ceph.com/docs/jewel/

This repository provides the following:

    - `Bill of Materials <https://github.com/open-power-ref-design/standalone-ceph/blob/master/standalone-ceph.pdf>`_
    - `Deployment configuration file <https://github.com/open-power-ref-design/standalone-ceph/blob/master/config.yml>`_

The Bill of Materials document provides a description and representation of
a Standalone Ceph Cluster with Operational Management
that is tuned for OpenPOWER servers.  It provides information
such as model numbers and feature codes to simplify the ordering process
and it provides racking and cabling rules for the preferred layout of
servers, switches, and cables.

The Deployment configuration file provides a mapping of servers and switches
to software for the purposes of deployment.  Each server is mapped to a set
of software roles constituting the control plane for the Ceph monitors
and Operational Management, and a set of OSD nodes. Each role is defined in terms
of a Linux distribution (Ubuntu) to be loaded and a set of operating system based
resources such as users and networks that need to be configured
to satisfy that role.

The Deployment configuration file must be edited prior to deployment
to reflect the actual configuration that is to be installed.  This is
mostly a matter of making sure that the numbers of servers represented
match the number of servers to be installed and that externally visible
IP addresses are allocated from a user specified pool, so that the
installation is properly integrated into the data center.

At the highest level, the installation process is split into two parts::


    > Bare metal installation of Linux
    > Installation of the Ceph cluster, OpenStack control plane, and Operational Management

The Deployment configuration file is fed into the bare metal installation
process which is performed by cluster-genesis.  The operating system is loaded
and configured as specified in the configuration file.  Users, networks, and
switches are configured during this step.  The last step is to invoke a small
script that installs the cluster software which is performed by os-services.

More properly, in the bare metal installation step, only the installation tools
for cluster software are installed, not the actual services.  The next step
is to configure these tools, so that they install the actual services in a
prescribed manner so that they fit properly in the data center.  The three
projects that are involved are os-services, ceph-services, and opsmgr.
See the README files of each project to determine what may be configured.

The final step is to invoke cluster-create.sh in the os-services
repository to install and configure the cluster.  os-services orchestrates
the installation process of Ceph, OpenStack, and Operational Management.

The OpenStack dashboard may be reached through your browser:

https://<ipaddr from external-floating-ipaddr in the config.yaml>

This recipe also includes an operational management console which is
integrated into the OpenStack dashboard.  It monitors the cluster infrastructure
and shows metrics relates to the capacity, utilization, and health of the
cluster.  It may also be configured to generate alerts when
components fail.  It is provided through the opsmgr repository.


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

   $ apt-get install python-pip
   $ pip install pyyaml
   $ git clone git://github.com/open-power-ref-design-toolkit/os-services
   $ cd os-services
   $ git checkout $TAG
   $ ./scripts/validate_config.py --file $CFG

#. Place the configuration file::

   $ git clone git://github.com/open-power-ref-design-toolkit/cluster-genesis
   $ cd cluster-genesis
   $ git checkout 1.2.1
   $ cp $CFG .

#. Invoke cluster-genesis to perform the bare metal installation process:

   Instructions may be found in Cluster Genesis User Guide identified above.

#. Wait for cluster-genesis to complete, ~3 hours:

#. Edit the OpenStack installer configuration file:

   OpenStack installation is performed by OpenStack-Ansible.  Instructions
   for editing the user configuration files of OpenStack is described in
   general terms in os-services.

#. Customize the Ceph cluster configuration:

   The Ceph cluster creation is performed by Ceph Ansible through ceph-services.
   Customizing the installation is optional and customization instructions are
   described in the `ceph-services README <https://github.com/open-power-ref-design-toolkit/ceph-services/README.rst>`_.

#. Invoke the toolkit again to complete the installation::

   $ ./scripts/create-cluster

   Note this command is invoked on the first controller node.  The commands
   listed above are invoked on the deployer node.  When cluster-genesis completes,
   it displays on the screen instructions for invoking the command above.

Verifying an install
--------------------

After successful installation, verify that Ceph services are running correctly.

* On the controller check the health of the ceph cluster using ``ceph -s``.

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

