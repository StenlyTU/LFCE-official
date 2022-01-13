# LFCE Networking

<img src="https://image.flaticon.com/icons/png/512/36/36181.png" width="150" align="right"/></a>

1. [Monitor, tune and troubleshoot network performance](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_Networking.md#monitor,-tune-and-troubleshoot-network-performance)

2. [Configure network traffic tunneling](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_Networking.md#configure-network-traffic-tunneling)

3. [Configure a system to perform Network Address Translation](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_Networking.md#Configure-a-system-to-perform-Network-Address-Translation)

4. [Dynamically route IP traffic](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_Networking.md#Dynamically-route-IP-traffic)

5. [Implement advanced packet filtering](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_Networking.md#Implement-advanced-packet-filtering)


## Monitor, tune and troubleshoot network performance

nmon, vnstat

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


## Configure a system to perform Network Address Translation

## Dynamically route IP traffic

## Implement advanced packet filtering

Use firewalld: [Firewall info](https://github.com/StenlyTU/LFCS-official/blob/main/stuff/Networking.md#implement-packet-filtering)


[Back to top of the page: ⬆️](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_Networking.md)
