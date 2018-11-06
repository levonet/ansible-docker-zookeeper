Zookeeper Role
==============

Configure and start Zookeeper in a docker container using the official image `zookeeper:latest`. 

Role Variables
--------------

- `zookeeper_id` (required): The id must be unique within the ensemble and should have a value between 1 and 255.
- `zookeeper_servers` (required): This variable allows you to specify a list of machines of the Zookeeper ensemble. Each entry has the form of `server.id=host:port:port`. Entries are separated with space.
  In 3.5, the syntax of this has changed. Servers should be specified as such: `server.id=<address1>:<port1>:<port2>[:role];[<client port address>:]<client port>` [Zookeeper Dynamic Reconfiguration](http://zookeeper.apache.org/doc/r3.5.3-beta/zookeeperReconfig.html).
- `zookeeper_name` (default: zookeeper): This name used in name of home folder path and in name of container.
- `docker_zookeeper_version` (default: latest)
- `docker_zookeeper_image` (default: zookeeper:{{docker_zookeeper_version}})
- `docker_zookeeper_expose` (default: [2181, 2888, 3888])
- `docker_zookeeper_ports` (default: [2181, 2888, 3888]) The zookeeper client port, follower port, election port respectively.
- `docker_zookeeper_env` (optional): See [Environment variables](https://github.com/31z4/zookeeper-docker#environment-variables) for more information.
- `docker_zookeeper_network_mode` (default: host): Connect the container to a network.
- `docker_zookeeper_networks` (default: []): List of networks the container belongs to.
- `docker_zookeeper_log_driver` (default: json-file): Specify the logging driver.
- `docker_zookeeper_log_options` (optional): Dictionary of options specific to the chosen log_driver. See [Configure logging drivers](https://docs.docker.com/engine/admin/logging/overview/) for details.

Dependencies
------------

- `docker`

Example Playbook
----------------

In `hosts`:
```ini
[zooservers]
10.10.10.1 zookeeper_id=1
10.10.10.2 zookeeper_id=2
10.10.10.3 zookeeper_id=3
```

In playbook:
```yaml
- hosts: zooservers
  roles:
    - role: levonet.docker-zookeeper
      zookeeper_servers: "server.1=10.10.10.1:2888:3888 server.2=10.10.10.2:2888:3888 server.3=10.10.10.3:2888:3888"
      docker_zookeeper_log_driver: syslog
      docker_zookeeper_log_options:
        syslog-facility: local0
        tag: "{{ zookeeper_name }}"
      when: zookeeper_id is defined
```

An example of running a Zookeeper cluster on a single server
------------------------------------------------------------

```yaml
- hosts: all
  vars:
    zookeeper_host: "{{ hostvars[inventory_hostname]['ansible_default_ipv4']['address'] }}"
    docker_zookeeper_version: "3.5"
  roles:
  - role: levonet.docker-zookeeper
    zookeeper_name: zoo-1
    docker_zookeeper_env:
      ZOO_MY_ID: 1
      ZOO_SERVERS: server.1=0.0.0.0:2788:3788;2181 server.2={{ zookeeper_host }}:2888:3888;2182 server.3={{ zookeeper_host }}:2988:3988;2183
    docker_zookeeper_expose: [2181, 2788, 3788]
    docker_zookeeper_ports:
    - 2181
  - role: levonet.docker-zookeeper
    zookeeper_name: zoo-2
    docker_zookeeper_env:
      ZOO_MY_ID: 2
      ZOO_SERVERS: server.1={{ zookeeper_host }}:2788:3788;2181 server.2=0.0.0.0:2888:3888;2182 server.3={{ zookeeper_host }}:2988:3988;2183
    docker_zookeeper_expose: [2182, 2888, 3888]
    docker_zookeeper_ports:
    - 2182
  - role: levonet.docker-zookeeper
    zookeeper_name: zoo-3
    docker_zookeeper_env:
      ZOO_MY_ID: 3
      ZOO_SERVERS: server.1={{ zookeeper_host }}:2788:3788;2181 server.2={{ zookeeper_host }}:2888:3888;2182 server.3=0.0.0.0:2988:3988;2183
    docker_zookeeper_expose: [2183, 2988, 3988]
    docker_zookeeper_ports:
    - 2183
```

License
-------

[Apache Licence v2](http://www.apache.org/licenses/LICENSE-2.0)

Author Information
------------------

This role was created by [Marica Antonacci](https://github.com/maricaantonacci)  
Current maintainer [Pavlo Bashynskyi](https://github.com/levonet)
