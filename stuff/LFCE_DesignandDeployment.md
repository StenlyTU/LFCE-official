# LFCE: Design and Deployment

1. [Define a capacity planning strategy](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_DesignandDeployment.md#Define-a-capacity-planning-strategy)

2. [Conduct post deployment verifications](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_DesignandDeployment.md#Conduct-post-deployment-verifications)

3. [Create and maintain software packages](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_DesignandDeployment.md#Create-and-maintain-software-packages)

4. [Create, configure and maintain containers](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_DesignandDeployment.md#Create,-configure-and-maintain-containers)

4. [Deploy, configure, and maintain high availability/clustering/replication](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_DesignandDeployment.md#Deploy,-configure,-and-maintain-high-availability/clustering/replication)

## Define a capacity planning strategy

## Conduct post deployment verifications

## Create and maintain software packages

- To build an rpm file based on the spec file that we just created, we need to use rpmbuild command
    ```bash
    yum install rpm-build rpmdevtools
    rpmdev-setuptree
    ```
    - `wget http://downloads.xiph.org/releases/icecast/icecast-2.3.3.tar.gz` -> Download source tar file for the package that you want to build and move it to **..rpmbuild/SOURCES**
    - Go to **..rpmbuild/SPECS** and execute rpmdev-newspec spec_name.spec to create simple spec file.
    - Edit the spec file and fill all needed.
    - `rpmbuild -v -bb ./SPECS/icecast.spec` -> Build it

- To downgarde the rpm package, use the below command syntax:
   - `yum --showduplicates list firefox` -> Show old versions.
   - `[package-name]-[version].[architecture]` -> Structure of the package name.
   - `yum install yum-utils` -> To install yumdownloader.
   - `yumdownloader firefox-91.2.0-4.el7.centos.x86_64` -> To download the old package.
   - `yum install firefox-91.2.0-4.el7.centos.x86_64` -> Works only when upgrading.
   - `rpm -Uvh --oldpackage [downloaded_lower_version_package]` -> Revert to the old version.

## Create, configure and maintain containers

- LXC:
    - acronym for Linux Containers, is a lightweight Linux kernel based virtualization solution,
      which practically runs on top of the Operating System, allowing you to run multiple isolated
      distributions the same time.

    - The difference between LXC and KVM virtualization is that LXC doesn’t emulates hardware, but
    shares the same kernel namespace, similar to chroot applications. This makes LXC a very fast virtualization solution compared to other virtualization solutions, such as KVM, XEN or VMware

    - `yum install epel-release && yum install lxc lxc-templates` -> Install it.

    - `systemctl start lxc; lxc-checkconfig` -> Check the kernel for LXC support.

    - `ls -l /usr/share/lxc/templates/` -> List available LXC container templates.

    - `lxc-create -n container_name -t container_template` -> Create LXC container.
        - `lxc-create -n mydcb -t centos`

    - `lxc-ls; lxc-info --name mydcb` -> To see the containers. If lxc-ls is not found install it using: `yum whatprovides /usr/bin/lxc-ls`

    - `lxc-start -n mydcb -d` -> Start it.

    - `lxc-console -n mydcb` or `lxc-attach` -> Connect to it.

    - `lxc-stop -n mydcb`, `lxc-destroy -n mywheez`, `lxc-clone mydeb mydeb-clone`

    - `lxc-copy -n bucket2 -N bucket3` -> lxc-copy is the newest command for cloning LXC containers.

    - `lxc-create -n debby -t download -- -d debian -r bullseye -a amd64`
        - -n = name
        - -t = template
        - -d = distibution
        - -a = arch
        - -r = release

    - `echo "lxc.start.auto = 1" >> /var/lib/lxc/autostart_container/config` -> Make the container to autostart.

    - And finally, all created containers reside in **/var/lib/lxc/** directory. If for some reason you need to
    manually adjust container settings you must edit the config file from each container directory.

- Docker:

    - https://github.com/StenlyTU/LFCS-official/blob/main/stuff/ServiceConfiguration.md#manage-and-configure-containers

- KVM:
    - https://github.com/StenlyTU/LFCS-official/blob/main/stuff/ServiceConfiguration.md#manage-and-configure-virtual-machines

## Deploy, configure, and maintain high availability/clustering/replication

- TBD

[Back to top of the page: ⬆️](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_DesignandDeployment.md#Define-a-capacity-planning-strategy)