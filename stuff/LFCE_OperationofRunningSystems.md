# LFCE Operation of Running Systems - 18%

1. [Monitor, tune and troubleshoot system performance](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_OperationofRunningSystems.md#monitor,-tune-and-troubleshoot-system-performance)
2. [Update operating systems to provide required functionality and security](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_OperationofRunningSystems.md#update-operating-systems-to-provide-required-functionality-and-security)
3. [Update the kernel and ensure the system is bootable](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_OperationofRunningSystems.md#update-the-kernel-and-ensure-the-system-is-bootable)
4. [Script automation tools to make work faster and more accurate](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_OperationofRunningSystems.md#script-automation-tools-to-make-work-faster-and-more-accurate)
5. [Train team members on new technology or changes to existing systems](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_OperationofRunningSystems.md#train-team-members-on-new-technology-or-changes-to-existing-systems)
6. [Maintain systems via configuration management tools](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_OperationofRunningSystems.md#maintain-systems-via-configuration-management-tools)
7. [Maintain the integrity and availability of hardware](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_OperationofRunningSystems.md#maintain-the-integrity-and-availability-of-hardware)
8. [Develop and test disaster recovery plans](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_OperationofRunningSystems.md#Develop-and-test-disaster-recovery-plans)
9. [Support incident management for outages/trouble](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_OperationofRunningSystems.md#support-incident-management-for-outages-trouble)
10. [Produce and deliver reports on system use (processor, memory, disk, and network), outages, and user requests](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_OperationofRunningSystems.md#produce-and-deliver-reports-on-system-use-(processor,-memory,-disk,-and-network),-outages,-and-user-requests)
11. [Monitor security and conduct audits](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_OperationofRunningSystems.md#monitor-security-and-conduct-audits)
12. [Manipulate Linux system during the recovery process](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_OperationofRunningSystems.md#Manipulate-Linux-system-during-the-recovery-process)
13. [Use udev for device detection and management](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_OperationofRunningSystems.md#Use-udev-for-device-detection-and-management)
14. [Configure and modify SELinux/AppArmor policies](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_OperationofRunningSystems.md#configure-and-modify-\SELinux-or-AppArmor-policies)


## Monitor, tune and troubleshoot system performance

- Use the following command to do it: `df, du, top, free and ps`

- Tasks:
    - Configure *swappiness* to 20:
        - ***Swappines***: Define how aggressive (sic) the kernel will swap memory pages. Higher values will increase aggressiveness, lower values decrease the amount of swap. A value of 0 instructs the kernel not to initiate swap until the amount of free and file-backed pages is less than the high water mark in a zone. More [info](https://www.howtogeek.com/449691/what-is-swapiness-on-linux-and-how-to-change-it/).
        - `/proc/sys/vm/swappiness || sysctl -a | grep swappiness` -> To check swappiness value.
        - Add to `/etc/sysctl.conf` *vm.swappiness=20* and make it persistent.
        - Changing the Linux swappiness value has an instant effect; you don’t need to reboot.
    - Set *hugepages* to 6 and *overcommit hugepages* to 1. Make it persistent.
        - Add them to `/etc/sysctl.conf`, check with `sysctl -a | grep <huge>` what is the correct key-value.
        - ***Hugepages Overcommit*** - Defines the maximum number of additional huge pages that can be created and used by the system through overcommitting memory. Writing any non-zero value indicates that the system obtains that number of huge pages from the kernel's normal page pool if the persistent huge page pool is exhausted.
    - Configure sten Group to be able to create hugepages.
        - Add it to `/etc/security/limits.conf`:
            ```bash
            @sten		  - 	memlock		unlimited
            #             OR                (max-value=Hugepages Count * Hugepage size)
            ```

## Update operating systems to provide required functionality and security

## Update the kernel and ensure the system is bootable

- Find the kernel and change it:

    `uname -sr`

    `grubby --default-kernel`

    `grubby --set-default /boot/vmlinuz..` -> Change the default kernel.

    `grubby --info=ALL `- Give general information for all kernels.

- Check [LFCS repo](https://github.com/StenlyTU/LFCS-official/blob/main/stuff/OperationofRunningSystems.md#install-configure-and-troubleshoot-bootloaders)

## Script automation tools to make work faster and more accurate

## Train team members on new technology or changes to existing systems

## Maintain systems via configuration management tools

## Maintain the integrity and availability of hardware

## Develop and test disaster recovery plans

## Support incident management for outages trouble

## Produce and deliver reports on system use (processor, memory, disk, and network), outages, and user requests

- For CPU: `mpstat` and `sar`
- For Memory: `vmstat`
    ```bash
    # vmstat
        procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
    r  b      swpd   free   buff  cache    si   so   bi    bo    in   cs  us sy id wa st
    2  0       0  3303812 87912   13199    0    0    3     6     62   84  0  0 100  0  0

    r -> The number of processes in a running state.
    b -> The number of processes in uninterruptible sleep state.
    The "si" and "so" fields indicate that the systems physical memory is full and the swap partition/file is being used.
    The "bi" and "bo" fields indicate the number of blocks being read from disk and block being written to disk.
    in -> The number of interrupts per second, including the clock.
    cs -> The number of context switches per second.
    us -> Time spent running non-kernel code.
    sy -> Time spent running kernel code.
    id -> Time spent idle.
    wa -> Time spent waiting for I/O.
    st -> Time stolen from a virtual machine.
    ```
- For Disk: `df` and `du`
- For Network: `nmon` and `vnstat`
    - More info in Networking Section.

## Monitor security and conduct audits

- Check [LFCS repo:](https://github.com/StenlyTU/LFCS-official/blob/main/stuff/OperationofRunningSystems.md#locate-and-analyze-system-log-files)

- ausearch
    - By default, ausearch queries the `/var/log/audit/audit.log` file.
    - `ausearch -p 2317` -> Search by process.
    - `ausearch -m USER_LOGIN -sv no` -> Check Failed Login Attempts in Auditd Log File.
        - -m option to identify specific messages and -sv to define the success value
    - `ausearch -ua tecmint` -> Find User Activity in Auditd Log File.
    - `ausearch -ua tecmint -ts yesterday -te now -i` -> User search for specific time.
        -  -ts for start date/time and -te for specifying end date/time as follows (note that you can use words such as now, recent, today, yesterday, this-week, week-ago, this-month, this-year
    - `auditctl -w /etc/passwd -p rwa -k passwd_changes` -> Create Audit rule which will log any attempts to access or modify the /etc/passwd.
        - `ausearch -k passwd_changes | less` -> And search for it
        - `auditctl -l` -> Show the configured rules.
        - `auditctl -W /etc/passwd -p rwa -k passwd_changes` -> To remove the rule.

## Manipulate Linux system during the recovery process

## Use udev for device detection and management

- Udev (userspace /dev) is a Linux sub-system for dynamic device detection and management. It dynamically creates or removes device nodes (an interface to a device driver that appears in a file
system as if it were an ordinary file, stored under the /dev directory) at boot time or if you add a
device to or remove a device from the system. It then propagates information about a device or
changes to its state to user space. One of the pros of udev is that it can use persistent device names to guarantee consistent naming of devices across reboots, despite their order of discovery. This feature is useful because the kernel simply assigns unpredictable device names based on the order of discovery. Udev is based on rules - it’s rules are flexible and very powerful. Every received device event is matched against the set of rules read from files located in **/lib/udev/rules.d** and **/run/udev/rules.d**. You can write custom rules files in the /etc/udev/rules.d/ directory (files should end with the .rules extension) to process a device. To view udev log use *udevadm* tool.

    `systemctl status systemd-udevd` -> Check udev systemd daemon.

    `udevadm monitor` -> Check udev events.

    `udevadm info /dev/sdb1` -> Query the device attributes from the udev database, use the info command.

- A rule comprises of a commaseparated list of one or more key-value pairs. Rules allow you to rename a device node from the default name, modify permissions and ownership of a device node, trigger execution of a program
or script when a device node is created or deleted, among others.

    `vim /etc/udev/rules.d/80-test.rules` - Create new rule with the following content:

    ```bash
    SUBSYSTEM=="usb", ACTION=="add", ENV{DEVTYPE}=="usb_device", RUN+="/bin/device_added.sh"
    SUBSYSTEM=="usb", ACTION=="remove", ENV{DEVTYPE}=="usb_device", RUN+="/bin/device_removed.sh"
    ```
    "==": is an operator to compare for equality.
    
    "+=": is an operator to add the value to a key that holds a list of entries.
    
    SUBSYSTEM: matches the subsystem of the event device.
    
    ACTION: matches the name of the event action.
    
    ENV{DEVTYPE}: matches against a device property value, device type in this case.
    
    RUN: specifies a program or script to execute as part of the event handling.

    `man udev` -> For more info regarding the Udev rules syntax.

    `sudo udevadm control --reload` -> Tell systemd-udevd to reload the rules files (this also reloads other databases such as the kernel module index)

    `/usr/lib/udev/rules.d/` -> Systemd installed rules. From here you can see EXAMPLES!

    `/etc/udev/rules.d/` -> Custom-made rules.

    `/dev/disk` -> Worth cheking this folder.

## Configure and modify SELinux or AppArmor policies

- Check the info for SELinux from [LFCS repo:](https://github.com/StenlyTU/LFCS-official/blob/main/stuff/OperationofRunningSystems.md#list-and-identify-selinuxapparmor-file-and-process-contexts)

- Examples:
    - Change SSH port to 8888 and configure the SELinux ssh ports: `semanage port`
    - Choosing a DocumentRoot outside /var/www/html for a virtual host on Apache: `semanage fcontext`

[Back to top of the page: ⬆️](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_OperationofRunningSystems.md#lfce-operation-of-running-systems---18)
