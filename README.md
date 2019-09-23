Solr Configuration as Master or Slave
=========
[![License](https://img.shields.io/badge/license-Apache-green.svg?style=flat)](https://raw.githubusercontent.com/lean-delivery/ansible-role-solr-cluster/master/LICENSE)
[![Build Status](https://travis-ci.org/lean-delivery/ansible-role-solr-cluster.svg?branch=master)](https://travis-ci.org/lean-delivery/ansible-role-solr-cluster)
[![Build Status](https://gitlab.com/lean-delivery/ansible-role-solr-cluster/badges/master/build.svg)](https://gitlab.com/lean-delivery/ansible-role-solr-cluster/pipelines)
[![Galaxy](https://img.shields.io/badge/galaxy-lean__delivery.solr__cluster-blue.svg)](https://galaxy.ansible.com/lean_delivery/solr_cluster)
![Ansible](https://img.shields.io/ansible/role/d/40128.svg)
![Ansible](https://img.shields.io/badge/dynamic/json.svg?label=min_ansible_version&url=https%3A%2F%2Fgalaxy.ansible.com%2Fapi%2Fv1%2Froles%2F40128%2F&query=$.min_ansible_version)
## Summary

This role:
  - Configures Solr on Centos 7, Ubuntu or Windows host to be master or slave.

Requirements
------------
  - Minimal Version of the ansible for installation: 2.7
  - **Java 8** [![Build Status](https://travis-ci.org/lean-delivery/ansible-role-java.svg?branch=master)](https://travis-ci.org/lean-delivery/ansible-role-java)
  - **Solr installed** [![Build Status](https://travis-ci.org/lean-delivery/ansible-role-solr-standalone.svg?branch=master)](https://travis-ci.org/lean-delivery/ansible-role-solr-standalone)
  - **Supported OS**:
    - CentOS
      - 7
    - Debian
      - "Stretch"
    - Ubuntu
      - "Xenial"
      - "Bionic"
    - Windows
      - "Windows Server 2008"
      - "Windows Server 2008 R2"
      - "Windows Server 2012"
      - "Windows Server 2012 R2"
      - "Windows Server 2016"
      - "Windows Server 2019"
      - "Windows 7"
      - "Windows 8.1"
      - "Windows 10"

[Prepared Windows System](https://docs.ansible.com/ansible/latest/user_guide/windows_setup.html)

## Role Variables
  - `solr_instance_type` - determines solr instance type

    default: `master`

  - `solr_version` - matches available version on https://archive.apache.org/dist/lucene/solr/. Tested versions 5.3-8.x

    default: `8.0.0`

  - `solr_config_lines` - configuration for master/slave configuration (depends on OS type and solr_instance_type)

  - `solr_master_generated_url` - URL for master replication. Configured for slave nodes

    default: `{{ (solr_ssl_enabled) | ternary('https', 'http') }}://\
              {{ solr_master_defined_host | default(groups[(master_group | default('solr_master'))]|first) }}:\
              {{ solr_port }}/solr/${solr.core.name}/replication`

  - `solr_dest_main_path` - root directory to store solr folder

    default: `/opt` for Linux

    default: `C:\Solr` for Windows

  - `solr_dest_path` - solr folder path

    default: `{{ dest_main_path }}/solr-{{ solr_version }}`

  - `solr_configset_path` - solr configset folder path

    default: `{{ solr_home }}/configsets` for Linux

    default: `{{ solr_dest_path }}\server\solr\configsets` for Windows

  - `solr_service_name` - solr service name

    default: `solr`

  - `solr_with_systemd` - to run solr as a service

    default: `True`

  - `solr_service_start` - to start solr service in the end of role/Playbook

    default: `True`

  - `configset_list` - list of configset directories

    default: `- default`

  - `auto_populate_configset_list` - get all configset directories automatically

    default: `True`

  - `solr_master_defined_host` - solr master host name. If defined - it will be used, else first server from solr_master group from inventory file

    default: `undefined`

  - `master_group` - solr master group name in inventory file

    default: `solr_master`

  - `solr_port` - solr master port

    default: `8983`

  - `solr_base_path` - path to solr base

    default: `/var/solr`

  - `solr_home` - path to SOLR_HOME

    default: `{{ solr_base_path }}/data`

  - `solr_ssl_enabled` - defined if solr master using ssl for connection

    default: `True`


Example Inventory
----------------
```ini
[solr_master]
solr_master.example.com

[solr-slave]
solr.example.com

[solrwin-slave]
solrwin.example.com

[solrwin-slave:vars]
ansible_user=admin
ansible_password=password
ansible_connection=winrm
ansible_winrm_server_cert_validation=ignore

[solrwin-master]
solrwin.example.com

[solrwin-master:vars]
ansible_user=admin
ansible_password=password
ansible_connection=winrm
ansible_winrm_server_cert_validation=ignore
```

Example Playbook
----------------

```yml
- name: Configure Solr as slave
  hosts: solr-slave
  roles:
    - role: lean_delivery.java
    - role: lean_delivery.solr_standalone
    - role: lean_delivery.solr_cluster
      solr_instance_type: slave

- name: Configure Solr as master
  hosts: solr_master
  roles:
    - role: lean_delivery.java
    - role: lean_delivery.solr_standalone
    - role: lean_delivery.solr_cluster
```

License
-------

Apache

Author Information
------------------

authors:
  - Lean Delivery Team <team@lean-delivery.com>
