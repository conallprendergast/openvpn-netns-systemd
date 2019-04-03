# openvpn-netns-systemd
Systemd configuration files for setting up an openvpn tunnel in a network namespace (without veth pairs)


## Description
This project contains the relevant files used for establishing an openvpn tunnel in a new network namespace.
VPNs established in a namespace do not affect the global network stack, so that the main network connection can remain unaffected.


It does *not* use veth pairs, but instead moves the _tun_ device into the new namespace.
It is possible to run multiple VPN namespaces seperatly (in seperate terminals), and not pollute the local network configuration.

It is even possible to nest VPN connections (Your PC -> VPN 1 -> VPN 2 -> public internet).

## Usage
### Shell Usage

    /bin/bash-vpn <VPN conf>

Where _VPN_ is the name of a script in /etc/openvpn/
eg

    bash-vpn /etc/openvpn/France.conf

This will create a vpn connection to France and put you in a root shell in the French VPN namespace. 

The _bash-vpn_ script can be executed multiple times in different terminals, effectively putting each terminal in a different VPN.


To nest VPN connections, simply execute the _bash-vpn_ script again, while in a _bash-vpn_ session.

### Systemd Usage

The VPN scripts are implemented as systemd units.

The example __daily-backup.timer__ and __daily-backup.service__ show how to leverage this to implement a timed backup script through VPN without affecting the global network namespace.
Given an openvpn configuration file /etc/openvpn/office.conf

    systemctl start openvpn-ns@office.service
    

Using the new network namespaces from above:

    sudo ip netns exec vpn-office <command>
    
eg.

    sudo ip netns exec vpn-office /bin/bash
    su - <user>
    firefox

## Stopping the VPN namespace

    systemctl stop openvpn-ns@<VPN>.service

or to stop all:

    systemctl stop 'openvpn-ns@*'

    

