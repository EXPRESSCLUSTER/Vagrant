# How to use Vagrant with EXPRESSCLUSTER preinstalled box


## Table of Contents

* [Evaluation Environment](#evaluation-environment)
* [Installing libvirt, KVM and Vagrant](#installing-libvirt-kvm-and-vagrant)
* [Building and adding a customized box](#building-and-adding-a-customized-box)
* [Running vagrant to create 2-node cluster](#running-vagrant-to-create-2-node-cluster)
* [Checking the created cluster](#checking-the-created-cluster)


## Evaluation Environment

* Host machine (on OCI)
  * Oracle Linux 8.7
  * Vagrant 2.3.4
  * libvirtd 8.0.0

* Guest machine
  * CentOS Linux release 7.9.2009 (Core)
    * https://app.vagrantup.com/generic/boxes/centos7 (box version v4.2.6)
  * EXPRESSCLUSTER X 5.0 (5.0.2-1)


## Installing libvirt, KVM and Vagrant

See [this document](/doc/HowToUse_with_KVM.md).


## Building and adding a customized box

1. Build your own vagrant box by using Packer.  
   See https://github.com/EXPRESSCLUSTER/Packer.

1. Add the generated box.
   ```
   $ vagrant box add --name local/ecx rhel84-ecx.box
   ```


## Running Vagrant to create 2-node cluster

1. Create work directory.
   ```
   $ mkdir vagrant-test
   $ cd vagrant-test
   ```

1. Place Vagrantfile (copy from [here](/Vagrantfile/ecx_preinstalled)).
   ```
   $ mv /path/to/Vagrantfile .
   $ ls
   Vagrantfile
   ```

1. Edit the following line in Vagrantfile if necessary.
   ```
   $ vi Vagrantfile
   ---
   lv.driver = "kvm"   # change here "qemu" if you cannot use KVM
   ---
   ```

1. Run vagrant.
   ```
   $ vagrant up
   ```


## Checking the created cluster

1. Check the status of guest machines.
   ```
   $ vagrant status
   Current machine states:

   server1                   running (libvirt)
   server2                   running (libvirt)
   ```

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
