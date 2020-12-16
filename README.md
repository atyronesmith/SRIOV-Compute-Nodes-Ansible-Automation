# SR-IOV Compute Nodes Ansible Automation

This project serves as a collaboration between the NFV and OpenShift on OpenStack team to create an *unofficial* but polished way to automate the creation of SR-IOV compute nodes in an OpenShift on OpenStack environment. This project contains ansible scripts that can be used in place of the current [UPI compute node ansible scripts](shiftstack-upi-compute-nodes-ansible) to create compute nodes that have attached SR-IOV NICs, and the associated SR-IOV networking resources.

[shiftstack-upi-compute-nodes-ansible]: https://github.com/openshift/installer/blob/master/upi/openstack/compute-nodes.yaml

## Overview

The changes provided in this repository allow the specification of additional networks to be added to OpenShift Workers during deployment.  An **additionalNetworks** variable has been added to *inventory.yaml*.  An example **aditionalNetworks** data structure is shown below.

````yaml
additionalNetworks: 
      - id: radio_uplink
        count: 2
        type: direct
        port_security_enabled: no
      - id: radio_downlink
        count: 2
        type: direct
        port_security_enabled: no
      - id: uplink1
        security_groups: "{{ os_sg_worker }}"
        count: 2
      - id: uplink2
        count: 2
````

In this example, eight ports are attach to the the OpenShfit worker.  Two ports from each of **radio_uplink**, **radio_downlink**, **uplink1**, and **uplink2** are attached to the worker.  **radio_uplink** and **radio_downlink** are SR-IOV networks *(type: direct)* are attached with port security disabled.  **uplink1** is attached using the *os_sg_worker* security group (same as the OpenShift SDN port).  **uplink2** is attach with no security group, but with port security still enabled.  

The definition of the **additionalNetworks** fields are as follows:

| Field | Example | Default | Description |
| :---  | :---:   | :---:   | :---------- |
| **id** | uplink1 \| 80f11b60-ce77-4614-a002-... | no default | Name or UUID or the OpenStack network to attach to the worker |
| **count** | 4 | 1 | Number of ports to create and connect to the worker |
| **port_security_enabled** | no | yes | Should port security be enabled for this port? |
| **security_groups** | ['my_security_group'] | none | List of defined security groups to attach to this port |

Notes:

- **count** - If no count field is specified, count defaults to 1.
- **security_groups** - If the default security group is desired, specify [default]
