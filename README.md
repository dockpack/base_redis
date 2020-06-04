base_redis
=============

Redis on Centos/RHEL 7

Requirements
------------

Yum with Software Collections.

Role Variables
--------------

See defaults/main.yml

Dependencies
------------

In your inventory define hosts like:

```
[redis_master]
node0.example.com

[redis_slave]
node1.example.com
node2.example.com

[redis_sentinel]
node0.example.com
node1.example.com
node2.example.com

```
Example Playbook
----------------

```
---
# Install Redis Sentinel on 3 RHEL7 VMs

- name: Install Redis master
  hosts: redis_master
  become: true
  vars:
    redis_server: true
  roles:
    - {role: base_redis, tags: master}

- name: Install Redis slaves
  hosts: redis_slave
  become: true
  vars:
    redis_server: true
    redis_slaveof: "{{ master_ip }} {{ redis_port }}"
  roles:
    - {role: base_redis, tags: slave}

- name: Configure Redis sentinel nodes
  hosts: redis_sentinel
  become: true
  vars:
    redis_sentinel: true
  roles:
    - {role: base_redis, tags: sentinel}
```


License
-------

MIT

Author Information
------------------
@bbaassssiiee
