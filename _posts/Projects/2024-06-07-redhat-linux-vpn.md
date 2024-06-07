---
author: 
title: Red Hat Linux VPN 
date: 2024-06-07
categories: [Projects]
tags: [Networking, Security, Linux, Terminal, VI]
toc: true
math: true
mermaid: true

---
# Setting up a VPN through terminal using Red Hat Linux -

In today's digital world, protecting our online activity is paramount. Virtual Private Networks (VPNs) offer a secure tunnel for our internet traffic, encrypting data and masking our location. This project will guide you through setting up a secure connection using Red Hat Enterprise Linux (RHEL) within a virtual machine and configuring a WireGuard VPN for added protection.

# Virtualizing the OS
First, we'll need a virtual environment. You can use either VirtualBox or VMware for this step. Download and install your preferred software then, head over to the Red Hat website and grab the latest ISO file. Note: You must create a free account to gain access to a developer subscription. This subscription is for individual use, and as we are using the OS for a project, this is what we'll use. 

Next, it's time to create the virtual machine. Fire up your virtualization software and allocate resources like CPU, memory, and disk space according to your needs. When it comes to network settings, you can choose between "bridged" mode, where the virtual machine acts like a separate device on your network, or "NAT" mode, where it shares your host machine's connection.

Finally, boot the virtual machine from the downloaded Red Hat ISO file. Follow the on-screen instructions to complete the installation process.

# Wireguard
Once Red Hat is up and running, we will install WireGuard, our VPN solution using the Linux terminal. We'll achieve this by enabling specific repositories within Red Hat. Luckily, the dnf package manager makes this an easy task. Open a terminal window and enter the following command:
```
sudo dnf install wireguard-tools
```

# Key Generation
WireGuard utilizes public and private keys for secure communication. We want to generate our own key pair using the following command in the terminal:
```
wg genkey | tee /etc/wireguard/$HOSTNAME.private.key | wg pubkey > /etc/wireguard/$HOSTNAME.public.key
```

And then let us set permissions and security controls for these files using

```
chmod 600 /etc/wireguard/<HOSTNAME>.private.key
```

Make sure to have these keys on hand for the following steps. 

# Configuring Wireguard
There are a few ways to configure the Wireguard client. We can use nmtui, the RHEL web console, nm-connection-editor, wg-quick service, or we can keep using nmcli (the command which allows us to manage Network functions). In this tutorial we are using the lattermost option.

Use the following command to create a new WireGuard connection profile:
```
nmcli connection add type wireguard con-name server-wg0 ifname wg0 autoconnect no
```

server-wg0 is what we are naming this server. You can change it to whatever you'd like, but keep it in mind for the future steps. Next we'll need to configure both IPv4 and IPv6 addresses for the tunnel. Two separate commands are used for each method respectively: 
```
nmcli connection modify server-wg0 ipv4.method manual ipv4.addresses 192.0.2.1/24
```

```
nmcli connection modify server-wg0 ipv6.method manual ipv6.addresses 2001:db8:1::1/32
```

Next we need to securely add the server's private key to the connection profile. This key is essential for establishing a secure connection.

```
nmcli connection modify server-wg0 wireguard.private-key "PRIVATE KEY"
```

Where it says `PRIVATE KEY`, enter the private key you have generated. Finally, we need to set the port for incoming WireGuard connections. Defining a listening port for incoming WireGuard connections is crucial for two reasons.Firstly, it ensures consistency. By default, WireGuard picks a random port whenever the interface activates. This can be problematic if you expect other devices to connect to your server as they wouldn't know where to look. Setting a fixed listening port solves this by providing a predictable location for incoming connections. Secondly, it aids in managing firewall rules. Firewalls regulate incoming and outgoing traffic on a network. By specifying a listening port for WireGuard, you can create firewall rules that only allow connections on that specific port. This strengthens security by blocking any unexpected incoming traffic that doesn't utilize the designated WireGuard port. 

Penultimately in this step, we need to edit a file located in '/etc/NetworkManager/system-connections/server-wg0.nmconnection'. We can do this using the 'vi' command, entering:
```
vi /etc/NetworkManager/system-connections/server-wg0.nmconnection
```

We meed to add:
```
[wireguard-peer.<public_key>]
allowed-ips=192.0.2.2;2001:db8:1::2;
```

Where there is public key, enter the public key that was generated. This section defines peer connection, with 'allowed-ips' setting the parameters of the addresses that are allowed to send data to this server.  

Optionally, we can reload and activate with these respective commands:
```
nmcli connection load /etc/NetworkManager/system-connections/server-wg0.nmconnection
```
To reload,
```
nmcli connection modify server-wg0 autoconnect yes
```
To configure the connection to start automatically, and
```
nmcli connection up server-wg0
```
To reactivate the server.

# Configuring Firewalled
Configuring firewalld is essential for running a secure WireGuard server. By default, firewalld acts as a gatekeeper, blocking all incoming traffic to the server. To allow authorized connections for WireGuard, we need to create an exception in firewalld. This involves opening the specific WireGuard port (usually 51820/udp) within the "public" zone. This ensures only approved WireGuard traffic can access the server, significantly improving security. Without firewalld configuration, any incoming traffic could potentially exploit weaknesses on the server.

Additionally, firewalld can be used to manage how client devices route their internet traffic. In some WireGuard setups, you might want all client traffic to flow through the VPN tunnel. Firewalld's masquerading function enables this behavior within the "public" zone. Masquerading essentially masks the client's IP address by replacing it with the server's IP address before sending traffic out to the internet. This allows the server to act as an intermediary, potentially anonymizing the client's origin and offering additional control over traffic flow through firewall rules. However, enabling masquerading is not always necessary and depends on your specific VPN use case.

For WireGuard clients to connect to the server, you'll need to adjust firewall settings using the firewalld service. This involves opening the designated WireGuard port (usually 51820/udp) for incoming connections in the "public" zone. Additionally, if you want clients to route all traffic through the VPN tunnel (using the server as the default gateway), you'll need to enable masquerading for the public zone.

First we need to open the Wireguard port. 
```
firewall-cmd --permanent --add-port=51820/udp --zone=public
```

Next, If you want clients to route all traffic through the tunnel, use `firewall-cmd` with the `--permanent` flag to enable masquerading for the public zone. This allows the server to act as an intermediary for client traffic.
```
firewall-cmd --permanent --zone=public --add-masquerade
```

After making changes, use `firewall-cmd` with the `--reload` flag to activate the new firewall rules.
```
firewall-cmd --reload
```

For verification, use firewall-cmd with the --list-all flag to view the current firewall configuration. This will confirm if the WireGuard port is open and masquerading is enabled (if applicable) for the public zone.
```
firewall-cmd --list-all
```

# Configuring a Wireguard client
Finally we are on the last third of the project. First we need to create a connection profile. 
```
nmcli connection add type wireguard con-name client-wg0 ifname wg0 autoconnect no
```

This command creates a profile named `client-wg0` and assigns the virtual interface `wg0` to it. Automatic connection is disabled (autoconnect no) for now.

Next we need to configure the client's tunnel IP addresses (IPv4 and IPv6):
```
nmcli connection modify client-wg0 ipv4.method manual ipv4.addresses 192.0.2.2/24
```
```
nmcli connection modify client-wg0 ipv6.method manual ipv6.addresses 2001:db8:1::2/32
```

Add the client's (you) private key to the connection profile:
```
nmcli connection modify client-wg0 wireguard.private-key "INSERT PRIVATE KEY HERE"
```

Including the client's private key in the WireGuard connection profile is an essential step for establishing a secure connection with the server. This key plays a critical role in ensuring both verification and encryption within the VPN tunnel. During the connection process, the client's private key acts as a digital signature, authenticating the client's identity to the server. By including this key in the profile, the server can verify the client's legitimacy during the initial handshake. Furthermore, the private key works in tandem with the server's public key to encrypt data transmitted through the tunnel. This encryption process essentially creates a secure, scrambled channel. The private key unlocks a specific encryption method that only the corresponding server's public key can understand. This ensures that only authorized devices can decrypt the data flowing through the tunnel, safeguarding the confidentiality of your communication.

Since nmcli doesn't directly support setting peer configurations, you'll need to manually edit the connection profile file (/etc/NetworkManager/system-connections/client-wg0.nmconnection). Here, you'll define a section for the server, including its public key, endpoint (hostname or IP and port), allowed IP addresses it can send data to the client, and an optional `keepalive` parameter.

Use the vi command from earlier to edit the file and add the second section below:
```
vi /etc/NetworkManager/system-connections/client-wg0.nmconnection
```

```
[wireguard-peer.UtjqCJ57DeAscYKRfp7cFGiQqdONRn69u249Fa4O6BE=]
endpoint=server.example.com:51820
allowed-ips=192.0.2.1;2001:db8:1::1;
persistent-keepalive=20
```

Next reload the `client-wg0` connection using:
```
nmcli connection up client-wg0
```

Finally, verify by pinging the addresses of the server:
```
ping 192.0.2.1
ping6 2001:db8:1::1
```

Display the interface config of wg0 using:
```
wg show wg0
```

And display the IP config of wg0 using:
```
ip address show wg0
```

# Conclusion
In this project, we configured a secure connection using Red Hat Enterprise Linux (RHEL) running in a virtual machine.

This virtual environment lets me test and manage the VPN setup more easily. I installed RHEL on the virtual machine and then set up WireGuard, a user-friendly and secure VPN solution. To manage the WireGuard connection, I used NetworkManager, a built-in tool in RHEL. I used commands to configure the connection profile, including IP addresses, server information, and my client's private key.

This setup is useful for situations where I need a secure and encrypted connection. For instance, I could use it to securely access my company network while working remotely. It would also be helpful on public Wi-Fi networks to protect my online activity and data. Additionally, this setup can enhance online privacy by masking IP address and location. In some regions, it could even be used to bypass internet censorship restrictions.

By following these steps, you can create your own secure and private connection using a virtualized RHEL environment with WireGuard, improving your online security and privacy.