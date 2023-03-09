<!-- 
TODO: 
- Create YAML format of the inventory
- 

-->

# Scaling Automation Mesh 
This document is to give a high-level perspective on how to scale the Ansible Automation Platform (AAP) automation mesh by utilizing the feature of `instance groups` in AAP

## Table of Contents
- Requirements
- Installation
- Usage
- Instance Groups
- Scheduling


## Requirements

- Environment(s)
- Inventory
- Setup Bundle
- Instance Group(s)

### Environment(s)
- Running Setup on AAP
    - if running on AAP, a Project, and job template will be required
    - A job template with a task to run the `setup.sh` will need to be created
- Running Setup on CLI
    - if running on a build server, the installation inventory is required. 

### Inventory
A master inventory is required in order to reference any nodes that will be part of the cluster.  This will be the same inventory that was used when installing AAP, and will be the main inventory that will be used when running a job to add or scale additional nodes.

- file used for installation

#### Inventory Repository
The best practice is to have the inventory saved in a repository where AAP
will be able to connect and sync a project.  the installation the inven

### Setup Bundle
Recommended to pre-download the bundle on a file-share or the build server where the `setup.sh` will be run from.  

#### Bundle Download URL
- located [here](https://ansible.com/)
    - Preferred method is to have the setup bundle saved on a file-share 
so it can be downloaded at runtime.  

## Scaling Additional Nodes
### Configuring the Inventory

when preparing to add additional instances (Execution Nodes) for the automation mesh, in the inventory, in addition to having all nodes in the `execution_nodes` inventory group, it is also recommended to create additional inventory groups for the separate nodes.  An example of an inventory would look like this. 

```console
[automationcontroller]
126-addr.tatu.home 

[automationcontroller:vars]
peers=execution_nodes

[execution_nodes]
110-addr.tatu.home
111-addr.tatu.home
112-addr.tatu.home

[instance_group_equinix]
111-addr.tatu.home 

[instance_group_ssc]
110-addr.tatu.home 

[instance_group_atc]
110-addr.tatu.home 
```

### Running the Setup Bundle
If preferred to run the `setup.sh` from an AAP job_template or job_workflow, 
the creation of a playbook and task to run the `setup.sh` bundle will be required.

Typically this can be done by creating a task similar to the following:

```yaml

# Installation task
- name: run setup.sh
  shell: >
     ./setup.sh -i <inventory-file> #need to validate this
  register: setup_results
    
```

## Usage of Additional Nodes
- Instance Groups
- 
<!--
### Configuring Environment
### Using the Inventory
-->

### Creating Instance Groups
After the installation of the `setup.sh` file has been completed, within AAP create 
an `instance group(s)` for the execution nodes that have just been added to the cluster.

### Scheduling Jobs
There is an order of priority when determining the best place to assign the `instance groups` to an AAP resource.  The order of precedence is taken from [here](https://docs.ansible.com/automation-controller/4.3/html/administration/containers_instance_groups.html#instance-groups)

1. Job Templates
2. Inventories
3. Organizations


## Resources 
*Containers and Instance Groups*
- https://docs.ansible.com/automation-controller/4.3/html/administration/containers_instance_groups.html#instance-groups


