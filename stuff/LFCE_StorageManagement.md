# LFCE: Storage Management

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/7/72/Storage_icon_of_three_disks.svg/1044px-Storage_icon_of_three_disks.svg.png" width="300" align="right"/></a>

1. [Manage advanced LVM configuration](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_StorageManagement.md#Manage-advanced-LVM-configuration)

2. [Identify storage devices using block device attributes](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_StorageManagement.md#Identify-storage-devices-using-block-device-attributes)

3. [Manage Linux file system features and flags](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_StorageManagement.md#Manage-Linux-file-system-features-and-flags)

4. [Implement and configure remote block storage devices](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_StorageManagement.md#Implement-and-configure-remote-block-storage-devices)

5. [Implement and configure network shares](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_StorageManagement.md#Implement-and-configure-network-shares)


## Manage advanced LVM configuration

- See the [LFCS info](https://github.com/StenlyTU/LFCS-official/blob/main/stuff/StorageManagement.md#manage-and-configure-lvm-storage) and read this [article](https://www.digitalocean.com/community/tutorials/how-to-use-lvm-to-manage-storage-devices-on-ubuntu-18-04)

- Logical volumes can be created with some *advanced options* as well. Some options that you may wish to consider are:

    **–type**: This specifies the type of logical volume, which determines how the logical volume is allocated. Some of the available types will not be available if there are not enough underlying physical volumes to correctly create the chosen topography. Some of the most common types are:

    - **linear**: The default type. The underlying physical devices used (if more than one) will simply be appended to each other, one after the other.

    - **striped**: Similar to RAID 0, the striped topology divides data into chunks and spread in a round-robin fashion across the underlying physical volumes. This can lead to performance improvements, but might lead to greater data vulnerability. This requires the -i option described below and a minimum of two physical volumes.

    - **raid1**: Creates a mirrored RAID 1 volume. By default, the mirror will have two copies, but more can be specified by the -m option described below. Requires a minimum of two physical volumes.

    - **raid5**: Creates a RAID 5 volume. Requires a minimum of three physical volumes.

    - **raid6**: Creates a RAID 6 volume. Requires a minimum of four physical volumes.

    **-m**: Specifies the number of additional copies of data to keep. A value of “1” specifies that one additional copy is maintained, for a total of two sets of data.

    **-i**: Specifies the number of stripes that should be maintained. This is required for the striped type, and can modify the default behavior of some of the other RAID options.

    **-s**: Specifies that the action should create a snapshot from an existing logical volume instead of a new independent logical volume.

    Examples:

    To create a striped volume, you must specify at least two stripes. This topology and stripe count requires a minimum of two physical volumes with available capacity:

    ```bash
    sudo lvcreate --type striped -i 2 -L 10G -n striped_vol LVMVolGroup
    ```

    To create a mirrored volume, use the raid1 type. If you want more than two sets of data, use the -m option. This example uses -m 2 to create a total of three sets of data (LVM counts this as one original data set with two mirrors). You will need at least three physical volumes for this to succeed:

    ```bash
    sudo lvcreate --type raid1 -m 2 -L 20G -n mirrored_vol LVMVolGroup
    ```

    To create a snapshot of a volume, you must provide the original logical volume to snapshot instead of the volume group. Snapshots do not take up much space initially, but grow in size as changes are made to the logical volume it is tracking:

    ```bash
    sudo lvcreate -s -L 10G -n snap_test LVMVolGroup/test
    ```

    Note: To revert a logical volume to the point-in-time of a snapshot, use:

    ```bash
    sudo lvconvert --merge LVMVolGroup/snap_test
    ```

## Identify storage devices using block device attributes

- Use the following commands and their options: [`blkid`](https://linux.101hacks.com/unix/blkid/), `lsblk`, etc.

    `blkid -t TYPE=ext4` -> Search by type.

    `blkid -o list` -> Show additional info for every device like: fs_type, label, mount point.

    `blkid -l -t LABEL=STEN_XFS` -> Search for specific label.

- You can also use the directories under `/dev/disk` to filter by *id*, *label*, *path* or *uuid*.

- `udevadm info -a -p /sys/block/sda` -> To see a lot of info for *sda* partition.

## Manage Linux file system features and flags

- Check some commands in [LFCS repo](https://github.com/StenlyTU/LFCS-official/blob/main/stuff/StorageManagement.md#create-and-configure-file-systems)

- `man mkfs.ext4` -> and search for -E which is the flag for extended options.

- `mkfs.xfs -f -l logdev=/dev/sde1,size=1000b /dev/sde2` -> Make xfs fs and separate data from logs on different partitions.

- `mount -o logdev=/dev/sde1 /dev/sde2 /tmp/mount_point` -> Use this mount.

## Implement and configure remote block storage devices

- The server can export local storage to remote clients. We are dedicating the device to the remote system.
    - On the server:
        - `yum install targetcli` - Install the CLI tool.
        - `systemctl enable target --now` - Enable and start the service.
        - `targetcli`
        - `/backstores/fileio create file1 /tmp/disk.img 200M write_back=false`
        - `cd iscsi & create iqn.2018-04.com.localnet.filedisk1` - Create the target.
        - `cd /iscsi/iqn.2018-04.com.localnet.filedisk1/tpg1/`
        - `luns/ create /backstores/fileio/file1` - Create the LUN.
        - `acls/ create iqn.2018-04.com.localnet:osboxes` - Create the ACL.
        - `cd /iscsi/iqn.2018-04.com.localnet.filedisk1/tpg1/acls/iqn.2018-04.com.localnet:osboxes`
        - `set auth userid=osboxes`
        - `set auth password=password`
        - `exit` - Use exit to save the above config.
        - `sudo netstat -atn | grep 3260`
        - `sudo firewall-cmd --permanent --add-service=iscsi-target; sudo firewall-cmd --reload`

    - On the client:
        - `yum install iscsi-initiator-utils`
        - `systemctl enable iscsid --now`
        - `systemctl enable iscsi --now`
        - `sudo vi /etc/iscsi/initiatorname.iscsi` - and add the following to the file:
            ```bash
            InitiatorName=iqn.2018-04.com.localnet:osboxes
            ```
        - `sudo vi /etc/iscsi/iscsid.conf ` and change the following lines:
            ```bash
            node.session.auth.authmethod = CHAP
            node.session.auth.username = osboxes
            node.session.auth.password = password
            ```
        - `systemctl restart iscsi; systemctl restart iscsid`
        - `sudo iscsiadm --mode discovery --type sendtargets --portal 192.168.0.105` - not working
        - `sudo iscsiadm -m node -T iqn.2018-04.com.localnet.filedisk1 -p 192.168.0.105:3260 --login`
            ```bash
            # vi /etc/fstab
            UUID="XXXXXXXXXX" /storage ext4  _netdev  0  0
            ```
        - `lsblk  --scsi`

        All above info can be found here: https://unixcop.com/iscsi-target-and-initiator-installation-on-centos8-rhel8/

- For more detailed info check the following Linked Learning course: https://www.linkedin.com/learning/linux-storage-systems/configure-remote-block-storage-devices

## Implement and configure network shares

- Have a look at the info in [LFCS repo](https://github.com/StenlyTU/LFCS-official/blob/main/stuff/StorageManagement.md#configure-systems-to-mount-file-systems-at-or-during-boot)

- NFS additional info:

    `/etc/exports.d/*exports` -> Is directory where you can add additional exports as seperate files.

    `/var/lib/nfs/etab` -> List of available shares.

    `/etc/sysconfig/nfs` -> Server and Client configuration file.

    Structure of the `/etc/exports` file:
    ```bash
    export host1(options) host2(options) host3(options)
    ```

    `nfsstat` & `nfsiostat` -> Provide NFS statistics.

    For more info see: `man nfs`

- Samba additional info:

    - Samba is more complex than NFS.

    - `testparm` - check an smb.conf configuration file for internal correctness.

    - For more info check:
        - `man samba`
        - `man smb.conf` -> Have good examples.
        - `/etc/samba.smb.conf.example` -> Example configuration file.

    - Example:
        ```bash
        sudo mkdir sambapublic
        sudo semanage boolean samba_export_all_ro --on
        sudo semanage boolean samba_export_all_rw --on
        sudo chown nobody:nobody /home/sambapublic/
        sudo chmod 775 /home/sambapublic/
        sudo semanage fcontext -at public_content_rw_t "/home/sambapublic(/.*)?"
        ls -dZ /home/sambapublic/
        sudo firewall-cmd --permanent --add-service samba_export_all_ro
        sudo firewall-cmd --reload
        sudo vi /etc/samba/smb.conf

        # Append the following to the smb.conf file:
        [sambapublic]
                comment = public samba share
                path = /home/sambapublic
                writable = yes
                public = yes
                #read only = no #Other options
                #guest ok = yes
                #valid users = vagrant

        testparm # check the smb.conf file.
        # Test locally
        smbclient -L //localhost # when promped for password hit enter.
        \> quit
        smbclient //locahost/sambapublic # when promped for password hit enter.
        \> help tar
        \> mkdir testdir

        # On remote host:
        mount -t cifs -o guest,noperm //SAMBA_SERVER_IP/sambapublic /home/sambapublic_local
        ```

- If you have access to *Linked Learning* have a look at the following courses:
    - https://www.linkedin.com/learning/linux-storage-systems
    - https://www.linkedin.com/learning/linux-system-engineer-network-filesystems-using-nfs-and-samba


[Back to top of the page: ⬆️](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_StorageManagement.md#Manage-advanced-LVM-configuration)
