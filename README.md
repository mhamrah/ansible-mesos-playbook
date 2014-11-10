ansible-mesos-playbook
======================

An ansible playbook for launching a mesos cluster with native docker and mesos executors, along with [Marathon](https://github.com/mesosphere/marathon)), [Consul](http://consul.io) and HAProxy support. Run this on Ubuntu 14.04 LTS (preferred) or Centos/RHEL 6. [Read the blog post](http://blog.michaelhamrah.com/2014/06/setting-up-a-multi-node-mesos-cluster-running-docker-haproxy-and-marathon-with-ansible/) for a descriptive overview.

### Getting Started

* Install [ansible](http://docs.ansible.com/intro_installation.html#installing-the-control-machine), version >= 1.7.
* Install [librarian-ansible](https://github.com/bcoe/librarian-ansible) via ```gem install librarian-ansible```
* Run ```librarian-ansible install```
* Spin up a bunch of Ubuntu 14.04 servers, say 5, on your favorite cloud provider.
* ```cp hosts.sample hosts``` and update the ```mesos_masters``` and ```mesos_slaves``` groups.
* ```cp ansible.cfg.sample ansible.cfg``` to ensure librarian_roles is in the ansible path (```ansible.cfg``` is git-ignored).
* Run ```ansible-playbook playbook.yml```.

### The Setup

* zookeeper, haproxy, mesos-master, consul and marathon with ha mode run on nodes in the mesos_primaries group. The zoo_id host variable is used to configure zookeeper, and consul_bootstrap is set on one node to initialize the cluster.
* mesos-slave runs in the mesos_workers group and are passed the list of mesos_primaries for coordination.
* Docker and native mesos are configured as containerizers on mesos-slaves. 
* A cron job on each master is set up to query the marathon api and configure HAProxy.
* HAProxy routes a frontend (listening on port 80) to backends based on marathon tasks.
* Consul for service discovery. It's not hooked into any other services, just part of the default setup.
* You probably want to tweak the HAProxy configuration script (in /opt/marathon/bin) for your needs. With the current setup you can have a wildcard dns prefix route to a backend matching the marathon name: i.e. www.example.com would do a least-connection proxy to the www task.

### Altering the Playbook

There are a variety of tweaks you can make to this playbook for your needs.

* Don't want Marathon, Consul or HAProxy? Simply remove roles from mesos_primaries.yml
* Don't want to overload the primaries? Simply add new groups and remap roles appropriately. 

### Troubelshooting

If you have trouble, ```/var/log/syslog``` on Ubuntu and ```/var/log/messages``` on RHEL is your friend. For Zookeeper, try ```/var/log/zookeeper/zookeeper.log```. You can try re-running the playbook; the roles aren't perfect but most are idempotent. 

Ansible lets you perform actions on groups of servers. You can try query or restart zookeeper and/or mesos:

```
$ ansible mesos_primaries -a "sudo status zookeeper"
$ ansible mesos_primaries -a "sudo restart zookeeper"
```

### Notes

Currently this installs Mesos 0.20.1 with Marathon 0.7.3. 

### Launching a Container

POST to /v2/apps:

```
{
    "id": "mlh", 
    "container": {
        "docker": {
            "image": "mhamrah/mesos-sample",
            "network": "BRIDGE",
            "portMappings": [
                { "containerPort": 8080, "hostPort": 0, "protocol": "tcp" }
            ]
        },
        "type": "DOCKER"
    },
    "cpus": 0.5,
    "mem": 512,
    "instances": 1
}
```
