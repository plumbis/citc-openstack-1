# OpenStack for Cumulus in the Cloud

This repository is used to configure a reference topology with Openstack Pike for Cumulus-in-the-Cloud deployment.

I've leaned heavily on the instructions in https://docs.openstack.org/pike/.

## Network Architecture

This is based on the cldemo-vagrant topology:
![Reference Topology](https://raw.githubusercontent.com/CumulusNetworks/cldemo-vagrant/master/documentation/cldemo_topology.png "Reference Topology")

The only difference being that the oob-mgmt-server also has a direct connection to leaf01 and leaf02 for routing purposes. 

There are two physical networks in this system, one is an out-of-band network used stictly for provisioning and low level operations.  The other is a high speed, fully redundant data plane IP fabric built using BGP unnumbered; each node is dual stack, addressable with IPv4 and IPv6.

This environment uses FRRouting from HostPack for host L3 peering with the data plane network and the NetQ host agents for host visibility. 

## Openstack Environment

Openstack Pike is deployed on four functional Ubuntu 16.04 nodes.  The controller and services master is server01, and server02-server04 are nova compute nodes.  The environment comes up with a demo "application" consisting of two cirros VMs connected on a tenant network which is in turn connected to the provider network with a router. One of the VMs has a public IP address.  The virtual networks are constructed neutron using VXLAN overlays.

All four servers have two files in the /home/cumulus directory that can be `source`d for Openstack CLI
- `os_user.sh` for commands around the specific project
- `os_admin.sh` for any admin commands.

## UIs
Besides console/ssh access into the oob-mgmt-server, the environment exports two web based UIs: NetQ Telemetry Server and OpenStack Horizon.

## Using
First, connect to the `oob-mgmt-server` with `vagrant ssh oob-mgmt-server`

From there you can `cd openstack` to access the directory with the automation.

Next, run the playbook to deploy Openstack and configure the network.
```
ansible-playbook setup.yaml
```

After the environment is established you can deploy two VMs with
```
ansible-playbook deploy.yml
```
and the UIs are accessed at the Telemetry Server's (jumpbox) IP adderess at...

NetQ    : http://IP:9000 (user/password = admin/CumulusNetQ!)

Horizon : http://IP:80/horizon (project/user/password = default/admin/CumulusLinux)
