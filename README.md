Zookeeper Role
=========

Configure and start Zookeeper in a docker container using the image `indigodatacloud/zookeeper:latest`. 

This role has been specifically developed to be used for the deployment of Mesos in the framework of INDIGO-DataCloud project.

Role Variables
--------------

- `zookeeper_host_list` (optional): list of zookeeper server nodes - alternatively, you can use a proper inventory file specifying the hosts group [zookeeper_servers]
- `zookeeper_version` (default: latest)
- `zookeeper_image` (default: indigodatacloud/zookeeper:{{zookeeper_version}}) 
- `zookeeper_client_port` (default: 2181)

Dependencies
------------

- `indigo-dc.docker`

Example Playbook
----------------

    - hosts: servers
      roles:
         - { role: indigo-dc.zookeeper, zookeeper_host_list: ["10.10.10.1", "10.10.10.2", "10.10.10.3" ] }

License
-------

Apache Licence v2 [1]

[1] http://www.apache.org/licenses/LICENSE-2.0

