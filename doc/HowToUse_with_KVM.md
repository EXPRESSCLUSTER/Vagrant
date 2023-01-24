# How to use with KVM


## Table of Contents

* [Evaluation Environment](#evaluation-environment)
* [Installing libvirt, KVM and Vagrant](#installing-libvirt-kvm-and-vagrant)
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

1. Confirm a CPU in the environment supports virtualization 
   ```
   $ lscpu | grep svm
   Flags:               fpu (snip) svm (snip) arch_capabilities
   ```

1. Apply latest packages.
   ```
   $ sudo dnf update
   ```

1. Install libvirt and KVM packages.
   ```
   $ sudo dnf install qemu-kvm libvirt
   ```

1. Configure libvirtd.
   ```
   $ sudo vi /etc/libvirt/libvirtd.conf   # edit the following parameters
   ---
   unix_sock_group = "libvirt"
   unix_sock_rw_perms = "0770"
   ---

   $ sudo usermod -G libvirt -a opc
   $ sudo systemctl restart libvirtd
   ```

1. Install Vagrant.
   ```
   $ sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
   $ sudo dnf install vagrant
   ```

1. Build libraries to use vagrant-libvirt.  
   (ref: https://vagrant-libvirt.github.io/vagrant-libvirt/installation.html)
   ```
   ## install required tools ##
   $ sudo dnf install libvirt-devel cmake gcc-c++ byacc

   ## build libssh ##
   $ mkdir libssh
   $ cd libssh
   $ dnf download --source libssh
   $ rpm2cpio libssh-0.9.6-3.el8.src.rpm | cpio -imdV
   $ tar xf libssh-0.9.6.tar.xz
   $ mkdir build
   $ cd build
   $ cmake ../libssh-0.9.6 -DOPENSSL_ROOT_DIR=/opt/vagrant/embedded/
   $ make
   $ sudo cp lib/libssh* /opt/vagrant/embedded/lib64/

   ## build libk5crypto ##
   $ cd ~
   $ mkdir krb5
   $ cd krb5
   $ dnf download --source krb5-libs
   $ rpm2cpio krb5-1.18.2-22.0.1.el8_7.src.rpm | cpio -imdV
   $ tar xf krb5-1.18.2.tar.gz
   $ cd krb5-1.18.2/src/
   $ ./configure
   $ make
   $ sudo cp -P lib/crypto/libk5crypto* /opt/vagrant/embedded/lib64/
   ```

1. Install vagrant-libvirt.
   ```
   $ vagrant plugin install vagrant-libvirt
   ```


## Running Vagrant to create 2-node cluster

1. Create work directory.
   ```
   $ mkdir vagrant-test
   $ cd vagrant-test
   ```

1. Place Vagrantfile (copy from [here](/Vagrantfile/qemu)) and EXPRESSCLUSTER files (rpm and license keys).
   ```
   $ mv /path/to/files .
   $ ls
   Vagrantfile  base-license.key  expresscls-5.0.2-1.x86_64.rpm  repl-license.key
   ```

1. Edit Vagrantfile.
   ```
   $ vi Vagrantfile   # comment out (or delete) the following line
   ---
   lv.driver = "qemu"
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
