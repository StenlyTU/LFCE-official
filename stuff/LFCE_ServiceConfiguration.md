# LFCE: Service Configuration

1. [Implement and configure an HTTP server](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_ServiceConfiguration.md#Implement-and-configure-an-HTTP-server)
2. [Implement and configure time synchronization server](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_ServiceConfiguration.md#Implement-and-configure-time-synchronization-server)
3. [Implement and configure network logging server](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_ServiceConfiguration.md#Implement-and-configure-network-logging-server)
4. [Configure a DHCP server](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_ServiceConfiguration.md#Configure-a-DHCP-server)
5. [Implement and configure an SMTP service](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_ServiceConfiguration.md#Implement-and-configure-an-SMTP-service)
6. [Implement and configure the HTTP proxy server](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_ServiceConfiguration.md#Implement-and-configure-the-HTTP-proxy-server)
7. [Configure host-based and user-based security for a service](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_ServiceConfiguration.md#Configure-host-based-and-user-based-security-for-a-service)
8. [Implement and configure a centralized authentication server](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_ServiceConfiguration.md#Implement-and-configure-a-centralized-authentication-server)
9. [Implement and configure a PXE Boot server](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_ServiceConfiguration.md#Implement-and-configure-a-PXE-Boot-server)
10. [Implement and configure an authoritative DNS server](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_ServiceConfiguration.md#implement-and-configure-an-authoritative-DNS-server)


## Implement and configure an HTTP server

-  Check LFCS repo: https://github.com/StenlyTU/LFCS-official/blob/main/stuff/ServiceConfiguration.md#configure-an-http-server

## Implement and configure time synchronization server

- Check LFCS repo: https://github.com/StenlyTU/LFCS-official/blob/main/stuff/Networking.md#synchronize-time-using-other-network-peers

## Implement and configure network logging server

## Configure a DHCP server

- ` yum install dhcp` -> Install it.

- `vim /etc/dhcp/dhcpd.conf` -> The main DHCP config file.

- `cp /usr/share/doc/dhcp-4.2.5/dhcpd.conf.example /etc/dhcp/dhcpd.conf` -> Take example config file and comment most of it.

- It will look like this:
  ```bash
  # A slightly different configuration for an internal subnet.
  subnet 192.168.0.0 netmask 255.255.255.0 {
    range 192.168.0.180 192.168.0.190;
    #option domain-name-servers ns1.internal.example.org;
    #option domain-name "internal.example.org";
    option routers 192.168.0.1;
    option broadcast-address 192.168.0.255;
    default-lease-time 600;
    max-lease-time 7200;
  }
  ```

- Add the DHCP service to the firewall and start the daemon.


## Implement and configure an SMTP service

- Enable the Postfix SMTP server for external access:
    - `yum install postfix` -> Install it first.
    - `/etc/postfix/main.cf` -> Main config file.
    - `postconf -d "inet_interfaces"` -> Verify postfix is listening on the network interfaces.
    - `postconf -e "inet_interfaces = all"` -> If required, enable listening on the network interfaces.
    - `postconf -e "mynetworks_style=subnet"` -> Enable trusted subnets means: trust all hosts in the same subnet as any interface on your host.
    - `systemctl restart postfix` -> And restart the service.

    - Try to send an email:
        ```bash
        $ telnet localhost 25
        Trying ::1...
        Connected to localhost.
        Escape character is '^]'.
        220 osboxes.localdomain ESMTP Postfix
        mail from:root
        250 2.1.0 Ok
        rcpt to:root
        250 2.1.5 Ok
        data
        354 End data with <CR><LF>.<CR><LF>
        THIS IS SPARTAAA
        .
        250 2.0.0 Ok: queued as BD9F1180D0629
        quit
        ```
        - Verify the mail was received using the ***mutt*** command or the ***mail*** command.

    - The default Postfix configuration is only marginally secure:
        - There is no SSL/TLS encryption.
        - No spam filtering is enabled by default.
        - The mynetworks_style directive allows for all hosts in the subnet to forward. Luckily, the default setting of the inet_interfaces directive allows only localhosts to connect.

- Enable dovecot as IMAP server:

    Dovecot is an open-source IMAP and POP3 server for Unix-like operating systems, written primarily with security in mind.

    The primary purpose of Dovecot is to act as mail storage server.

    - Install it: `yum install dovecot mutt`
    - Edit the following in `/etc/dovecot/dovecot.conf`:
        ```bash
        protocols = imap pop3 lmtp
        listen = *, ::
        ```
    - Ensure dovecot is using the proper storage location for email:
        - Edit `/etc/dovecot/conf.d/10-mail.conf`:
        ```bash
        mail_location = mbox: ̃/mail:INBOX=/var/spool/mail/%u
        ```
    - Restart the service: `systemctl restart dovecot`
    - Test it: `mutt -f imap://osboxes@192.168.0.181 `

- Enable/Disable TLS/SSL for IMAP in Dovecot:
    - Edit `/etc/dovecot/conf.d/10-ssl.conf` and set ***ssl*** to the desired state: yes|no|required

- Sending email using SMTP Auth (SASL-Simple Authentication and Security Layer) - not sure if needed
- Using both SASL and TLS in Postfix (SASL type = dovecot) - not sure if needed

## Implement and configure the HTTP proxy server

## Configure host-based and user-based security for a service

## Implement and configure a centralized authentication server

## Implement and configure a PXE Boot server

## Implement and configure an authoritative DNS server

- Check what is written in LFCS repo - https://github.com/StenlyTU/LFCS-official/blob/main/stuff/ServiceConfiguration.md#configure-a-caching-dns-server

[Back to top of the page: ⬆️](https://github.com/StenlyTU/LFCE-official/blob/main/stuff/LFCE_ServiceConfiguration.md#Implement-and-configure-an-HTTP-server)