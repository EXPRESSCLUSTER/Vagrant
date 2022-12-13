# How to use with Qemu


## Table of Contents

* [Evaluation Environment](#evaluation-environment)
* [Installing Vagrant and libvirt](#installing-vagrant-and-libvirt)
* [Running vagrant to create 2-node cluster](#running-vagrant-to-create-2-node-cluster)
* [Checking the created cluster](#checking-the-created-cluster)


## Evaluation Environment

* Host machine
  * Ubuntu 22.04 LTS
  * Vagrant 2.2.19
  * libvirtd 8.0.0

* Guest machine
  * CentOS Linux release 7.9.2009 (Core)
    * https://app.vagrantup.com/generic/boxes/centos7 (box version v4.2.6)
  * EXPRESSCLUSTER X 5.0 (5.0.2-1)


## Installing Vagrant and libvirt

1. Apply latest packages.
   ```
   $ sudo apt update
   $ sudo apt upgrade
   ```

1. Install Vagrant and libvirt packages.
   ```
   $ sudo apt install vagrant libvirt-daemon-system
   ```

1. Reboot OS (in order to start daemons).
   ```
   $ sudo reboot
   ```


## Running Vagrant to create 2-node cluster

1. Create work directory.
   ```
   $ mkdir vagrant-test
   $ cd vagrant-test
   ```

1. Place Vagrantfile and EXPRESSCLUSTER files (rpm and license keys).
   ```
   $ mv /path/to/files .
   $ ls
   Vagrantfile  base-license.key  expresscls-5.0.2-1.x86_64.rpm  repl-license.key
   ```

1. Run vagrant.
   ```
   $ vagrant up
   ```


## Checking the created cluster

1. Login to a guest machine.
   ```
   $ vagrant ssh server1   # or server2
   ```

1. Display the cluster status.
   ```
   $ sudo clpstat
    ========================  CLUSTER STATUS  ===========================
     Cluster : cluster-vagrant
     <server>
      *server1 .........: Online
         lankhb1        : Normal           Kernel Mode LAN Heartbeat
       server2 .........: Online
         lankhb1        : Normal           Kernel Mode LAN Heartbeat
     <group>
       failover1 .......: Online
         current        : server1
         fip1           : Online
         md1            : Online
     <monitor>
       fipw1            : Normal
       mdnw1            : Normal
       mdw1             : Normal
       userw            : Normal
    =====================================================================
   ```
