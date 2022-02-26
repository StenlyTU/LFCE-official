# LFCE Networking

<img src="https://image.flaticon.com/icons/png/512/36/36181.png" width="150" align="right"/></a>

1. [Monitor, tune and troubleshoot network performance](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_Networking.md#monitor,-tune-and-troubleshoot-network-performance)

2. [Configure network traffic tunneling](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_Networking.md#configure-network-traffic-tunneling)

3. [Configure a system to perform Network Address Translation](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_Networking.md#Configure-a-system-to-perform-Network-Address-Translation)

4. [Dynamically route IP traffic](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_Networking.md#Dynamically-route-IP-traffic)

5. [Implement advanced packet filtering](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_Networking.md#Implement-advanced-packet-filtering)


## Monitor, tune and troubleshoot network performance

- nmon is is a systems administrator, tuner, benchmark tool.  It can display the CPU, memory,  network,  disks (mini  graphs  or  numbers), file systems, NFS, top processes, resources.
    - `yum install nmon`

- vnstat
    - `yum install vnstat`
    - Change the default interface in `/etc/vnstat.conf`
    - `vnstat --create -i ens33` -> Create DB to store the data for ens33 interface.
    - `systemctl status vnstat`
    - `vnstat --dumpdb` -> Show what it is into the DB.
    - `vnstat --live` -> Show the current load.

## Configure network traffic tunneling

- Local SSH Port Forwarding:

    Assuming you are behind a restrictive firewall, or blocked by an outgoing firewall from accessing an application running on port 3000 on your remote server. You can forward a local port (e.g 8080) which you can then use to access the application locally.

    ```bash
    $ ssh -L 8080:server1.example.com:3000 admin@server1.example.com
    ```
    `-N` -> flag means do not execute a remote command.

    `-f` -> flag instructs ssh to run in the background.

- Remote SSH Port Forwarding:

    Remote port forwarding allows you to connect from your remote machine to the local computer. By default, SSH does not permit remote port forwarding. 

    ```bash
    $ sudo vim /etc/ssh/sshd_config
    ```
    And setting: `GatewayPorts yes` and restart the SSH Daemon: `sudo systemctl restart sshd`

    ```bash
    ssh -fN -R 5000:localhost:3000 admin@server1.example.com
    ```

- Dynamic SSH Port Forwarding:

    This is the third type of port forwarding. Unlike local and remote port forwarding which allow communication with a single port, it makes possible, a full range of TCP communications across a range of ports.

    Dynamic port forwarding sets up your machine as a SOCKS proxy server which listens on port 1080, by default.

    ```bash
    $ ssh -f -N -D 1080 admin@server1.example.com
    ```

- For more info see: `man ssh_config` and `man sshd_config`


## Configure a system to perform Network Address Translation

- Network Address Translation (NAT) allows for multiple network hosts to share the same external IP address. There are two types of outbound NAT or source NAT:

    - MASQUERADE: Works with a dynamic source IP address. It is useful for servers with dynamic IP addresses.
    - SNAT: Works with a static source IP address. It is less complex than MASQUERADE.
    - There is also a form of inbound or destination NAT (DNAT). DNAT allows for services to be behind a bastion host and to be easily load-balanced to different hosts.

    To enable any of these types of NAT, the ip_forward kernel option must be set to 1.
    ```bash
    echo 1 > /proc/sys/net/ipv4/ip_forward
    ```
    Example of masquerade rule:
    ```bash
    iptables -t nat -A POSTROUTING -o eth1 ! -d 192.168.12.0/24 -j MASQUERADE
    ```

    - Enable or Disable IP Masquerading Using Firewalld:

        - `sudo firewall-cmd --zone=public –query-masquerade` -> Check if masquerade is enabled.

        - `sudo firewall-cmd --zone=public --add-masquerade` -> Enable Masquerading.

    Tasks:
    - disallow user charlie to get or maybe send at tcp/portxxx on computer xxxxx
        - `sudo firewall-cmd --permanent --add-rich-rule="rule family='ipv4' source address='192.168.2.50' reject"` -> TBD!
    - Route traffic from port xxx at internet interface to ip address x.x.x.x at port xx.
        - To forward inbound network packets from one port to an alternative port or address, first enable IP address masquerading for a zone and forwarding: `firewall-cmd --zone=external --add-forward`
        - Ensure that interfaces between which you want to enable intra-zone forwarding are into the same zone.
        - `firewall-cmd --zone=external --add-forward-port=port=22:proto=tcp:toport=3753` -> Packets intended for port 22 are now forwarded to port 3753.
        - `firewall-cmd --zone=external --add-forward-port=port=22:proto=tcp:toport=2055:toaddr=192.0.2.55` -> Packets intended for port 22 are now forwarded to port 2055 at the address given with the "toaddr".

- More info for Firewalld: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_and_managing_networking/using-and-configuring-firewalld_configuring-and-managing-networking

## Dynamically route IP traffic

- `yum install quagga`
- `cp /usr/share/doc/quagga-0.99.22.4/zebra.conf.sample  /etc/quagga/zebra.conf` - Copy sample config file.
- `cp /usr/share/doc/quagga-0.99.22.4/ospfd.conf.sample /etc/quagga/ospfd.conf`- Copy the ospfd config file.
- `semanage boolean -m zebra_write_config -1` -> Configure the SELinux to be able to use "write" and edit files.
- `echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf` & `sysctl -p` -> Enable IPv4 forwarding.
- `systemctl start zebra ospfd`
- ```bash
  # vtysh
  configure terminal
  log file /var/log/quagga/quagga.log
  router ospf
  router-id  10.10.10.1
  network 10.10.10.0/30 area 0
  do write
  # Do the same on the another side.
  ```
- `firewall-cmd --add-protocol=ospf --permanent` - Add the protocol to the firewalld.
- From the Quagga console type: `show ip ospf neighbor` to check OSPF neighbors.

## Implement advanced packet filtering

- TCP Wrappers:
    - The TCP Wrappers system is a host-based network firewall and ACL.
    - The configuration for tcpwrappers is handled by two files, ***/etc/hosts.allow*** and ***/etc/hosts.deny***. Both files have the same syntax:
    - The /etc/hosts.allow file lets you choose which computers can access your system. In the file, you can specify simple rules in plain text to tell your computer how to handle connections.
        ```bash
        <DAEMON>:<CLIENT>
        #hosts.allow
        vsftpd:ALL
        ALL:LOCAL
        ALL:10
        ALL:.example.com EXCEPT untrusted.example.com

        #hosts.deny
        ALL:ALL
        ```
- Netfilter
    - netfilter is a packet-filtering framework built into the Linux kernel.

    Use firewalld: [Firewall info](https://github.com/StenlyTU/LFCS-official/blob/main/stuff/Networking.md#implement-packet-filtering)


[Back to top of the page: ⬆️](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_Networking.md)
