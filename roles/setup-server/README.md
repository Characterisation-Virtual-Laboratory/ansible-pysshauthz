# Server Setup for pysshauthz

This document uses Nectar as an example to spin up a Ubuntu virtual machine in the cloud.
The playbook will update the virtual machine with the latest packages and repositories.

## Requirement
* Nectar project allocation
* [Openstack command line client](https://docs.openstack.org/newton/user-guide/common/cli-install-openstack-command-line-clients.html) or:
* Access to [Nectar dashboard](https://dashboard.rc.nectar.org.au/)

## Setup Server
1. Launch a Ubuntu 18.04 server either from Openstack command line or from the Nectar dashboard
2. Configure the IP address of the server in `inventory` file (see `inventory.example` file for the correct format of the file)
3. Run the playbook with the `--tags=setup-server` switch to run this role
```
ansible-playbook -i inventory play.yml --tags=setup-server
```

