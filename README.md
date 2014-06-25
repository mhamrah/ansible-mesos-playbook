ansible-mesos-playbook
======================

An ansible playbook for launching a mesos cluster with docker (via [Deimos](https://github.com/mesosphere/deimos) and [Marathon](https://github.com/mesosphere/marathon) support. Run this on Ubuntu 14.04 LTS.

### Getting Started

* Install [librarian-ansible](https://github.com/bcoe/librarian-ansible) via ```gem install librarian-ansible```
* Run ```librarian-ansible install```
* Spin up a bunch of Ubuntu 14.04 servers, say 5.
* ```cp hosts.sample hosts``` and update the ```mesos_masters``` and ```mesos_slaves``` groups.
* Run ```ansible-playbook -i hosts playbook.yml```.

### The Setup

* zookeeper, haproxy, mesos-master and marathon with ha mode run on nodes in the mesos_master group. The zoo_id host variable is used to configure the zookeeper master nodes.
* mesos-slave runs in the mesos_slaves group and are passed the list of mesos_masters for coordination.
* Deimos is installed on the slaves.
* A cron job is set up to query the marathon api and configure frontend (listening on port 80) and backend (running marathon jobs) groups.
