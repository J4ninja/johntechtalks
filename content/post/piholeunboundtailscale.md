---
author: ["John Nguyen"]
title: "Blocking Ads Anywhere using Pi Hole, Unbound, and Tailscale"
description: "A tutorial on installing Pi Hole to block ads with a local recursive DNS server with unbound and permitting remote access through tailscale."
date: "2025-09-18"
type: post
translationKey: pagefind
draft: false
tags:  ["privacy", "raspberry pi", "self-hosting"]
categories: ["tutorials"]
cover: '/images/squarepihole.png'
---

![alt text](/images/piholeunboundlogo.png)

# Pi Hole
Pi-hole is a service that blocks ads at the network level. This even blocks ads in non-traditional ways including mobile apps and smart TVs. With Pi-hole, you protect your network from unwanted content without needing to install an individual ad blocker on each of your browsers and devices. Pi-hole doesn't have to just block ads, it can block tracking, metrics, telemetry, phishing, scams, malware, cryptojacking and whatever else you specify!

![alt text](/images/pihole.png)

# Unbound, a fast private DNS resolver
Unbound is a recursive, caching DNS resolver. Caching lets Unbound resolve domains faster than cloud nameservers like google and Cloudfare. Using Unbound also increases online privacy by keeping DNS traffic local, eliminating your worry about cloud providers selling your DNS data.

# Tailscale
Tailscale is a self-hosted VPN service. Using tailscale allows us to connect our devices anywhere to our home network and make use of our Pi-hole adblocking capabilities. This means even if we are away from home, on a different network or cellular data, we can continue to block ads without interruption!

# The Benefits Combination
With Pi-hole equipped with protective block lists, I can block as many ads and data tracking domains on all my devices with DNS set to my Pi. Unbound keeps my DNS traffic local and fast and Tailscale lets me use this protection over VPN from anywhere.

# Limitations
Pi-hole can't block every ad. If ads are served on the same domain or http, like most providers (Youtube) do, then there won't be an effect. You will still need to combine another Ad blocker extension. Block lists can break websites if they are too restrictive, and you will have to manually allow the domain or update the block lists. If your home network or Pi goes down, then you won't be able to connect to this setup via tailscale. If your Pi goes offline but the network is still on, then you may need to change the DNS server to the cloud instead of the Pi-hole to restore internet functionality. 

# Installing Pi-hole
As a pre-requisite, you will need a Raspberry Pi device that is imaged with a Linux operating system. For my setup, I have Ubuntu server installed on my Pi with no GUI, and I do everything over SSH on my Windows machine. The other pre-requisite is your Raspberry Pi will need a static internet protocol (IP) address. 

## Setting a static IP
The easiest way to do this is if you have network admin access, where you can manually set the static IP of your Pi or reserve the IP on DCHP. You can still do this without interfacing with your network portal as I did. The only drawback is that we will later have to manually set each device's DNS server to point to our Pi's IP instead of automating it through the admin portal. Here I will show you how to setup a static IP on Ubuntu.

### Setting a static IP on Ubuntu
1. sudo vi /etc/netplan/99-disable-network-config.cfg
2. Write in the file:

    ```
    network: {config: disabled}
    ```
3. cp /etc/netplan/99-disable-network-config.cfg /etc/cloud/cloud.cfg.d/
3. sudo vi /etc/netplan/01-static-ip.yaml
4. Write in the file (set the address section to desired static ip). Note YAML spacing matters

    ```
    network:
    version: 2
    renderer: networkd
    wifis:
        wlan0:
        dhcp4: no
        addresses:
            - 192.168.1.60/24
        routes:
            - to: default
            via: 192.168.1.1
        nameservers:
            addresses:
            - 192.168.1.1
            - 1.1.1.1
        access-points:
            "YourWifiSSID":
            password: "YourWifiPassword"

    ```
5. sudo netplan try (this temporarily lets you try the new settings and reverts if you don't click enter)
6. sudo netplan apply
7. sudo chmod 600 /etc/netplan/01-static-ip.yaml
8. Delete any other files under /etc/netplan that we didn't create

## Pi-hole install script
1. ssh or open a terminal on your Pi
2. update your packages 

    ```bash
    sudo apt update -y
    ```
2. Run the official install script from pi-hole in your terminal

    ```bash
    curl -sSL https://install.pi-hole.net | bash
    ```

3. This opens a GUI in your terminal. Assuming you setup the static IP, click Continue

4. Select a DNS provider. I suggest Google or Cloudfare. (Unbound will override this later)
5. Select 'Yes' to add StevenBlack's Unified Hosts list (This gives you starting block list)
8. Select no for query logging.
7. A link to the admin portal and a default password will be listed. Click the link to open it in a browser.
8. (Optional) Change your admin password 

    ```bash
    sudo pihole setpassword
    ```

## Setting DHCP server to your Pi-hole
This can be done on your Network admin portal. If you set the static IP manually on the Pi, then you'll want to change the Wifi settings DNS to point that static IP you set for every device. Below I show you how to do this on Windows.

### Windows Manually set DHCP server
1. Open Control Panel
2. Select Network and Internet -> Network and Sharing Center
3. Select your Wifi Network
4. Open Properties
5. Select Internet Protocol Version 4
6. Manually set the DNS Server to your static IP
7. Apply and unselect Internet Protocol Version 6 


# Installing Unbound

1. Install unbound package

    ```bash
    sudo apt install -y unbound
    ```

2. Get DNS root hints

    ```bash
    sudo curl -o /var/lib/unbound/root.hints https://www.internic.net/domain/named.root
    ```

3.  Create a configuration file

    ```bash
    sudo vi /etc/unbound/unbound.conf.d/pi-hole.conf
    ```

4. On another window copy the following and paste into the pi-hole.conf

    ```
    # If no logfile is specified, syslog is used
    # logfile: "/var/log/unbound/unbound.log"
    verbosity: 0

    interface: 127.0.0.1
    port: 5335
    do-ip4: yes
    do-udp: yes
    do-tcp: yes

    # May be set to no if you don't have IPv6 connectivity
    do-ip6: yes

    # You want to leave this to no unless you have *native* IPv6. With 6to4 and
    # Terredo tunnels your web browser should favor IPv4 for the same reasons
    prefer-ip6: no

    # Use this only when you downloaded the list of primary root servers!
    # If you use the default dns-root-data package, unbound will find it automatically
    #root-hints: "/var/lib/unbound/root.hints"

    # Trust glue only if it is within the server's authority
    harden-glue: yes

    # Require DNSSEC data for trust-anchored zones, if such data is absent, the zone becomes BOGUS
    harden-dnssec-stripped: yes

    # Don't use Capitalization randomization as it known to cause DNSSEC issues sometimes
    # see https://discourse.pi-hole.net/t/unbound-stubby-or-dnscrypt-proxy/9378 for further details
    use-caps-for-id: no

    # Reduce EDNS reassembly buffer size.
    # IP fragmentation is unreliable on the Internet today, and can cause
    # transmission failures when large DNS messages are sent via UDP. Even
    # when fragmentation does work, it may not be secure; it is theoretically
    # possible to spoof parts of a fragmented DNS message, without easy
    # detection at the receiving end. Recently, there was an excellent study
    # >>> Defragmenting DNS - Determining the optimal maximum UDP response size for DNS <<<
    # by Axel Koolhaas, and Tjeerd Slokker (https://indico.dns-oarc.net/event/36/contributions/776/)
    # in collaboration with NLnet Labs explored DNS using real world data from the
    # the RIPE Atlas probes and the researchers suggested different values for
    # IPv4 and IPv6 and in different scenarios. They advise that servers should
    # be configured to limit DNS messages sent over UDP to a size that will not
    # trigger fragmentation on typical network links. DNS servers can switch
    # from UDP to TCP when a DNS response is too big to fit in this limited
    # buffer size. This value has also been suggested in DNS Flag Day 2020.
    edns-buffer-size: 1232

    # Perform prefetching of close to expired message cache entries
    # This only applies to domains that have been frequently queried
    prefetch: yes

    num-threads: 2

    # Ensure kernel buffer is large enough to not lose messages in traffic spikes
    so-rcvbuf: 1m

    # Ensure privacy of local IP ranges
    private-address: 192.168.0.0/16
    private-address: 169.254.0.0/16
    private-address: 172.16.0.0/12
    private-address: 10.0.0.0/8
    private-address: fd00::/8
    private-address: fe80::/10

    # Ensure no reverse queries to non-public IP ranges (RFC6303 4.2)
    private-address: 192.0.2.0/24
    private-address: 198.51.100.0/24
    private-address: 203.0.113.0/24
    private-address: 255.255.255.255/32
    private-address: 2001:db8::/32
    ```

5. Go to the Pi-hole admin portal -> Settings -> DNS -> Upstream DNS Servers
6. Uncheck any public upstreams
7. Add a custom upstream

    ```
    127.0.0.1#5335
    ```
8. Save

# Installing Tailscale
1. Install tailscale on your Pi

    ```bash
    curl -fsSL https://tailscale.com/install.sh | sh
    ```

2. Spawn up the service

    ```bash
    sudo tailscale up
    ```

3. Create an account
4. On the Web UI, copy the address listed for your pi-hole
5. Click on the DNS setting and paste the address under Global DNS nameserver.
6. Select Override DNS Servers
7. Go back to the Machine tab, click the three dots next to your Pi and disable key expiry
8. On your other devices (laptop/tablet/PC/phone), install tailscale application (App store or Web download)
9. Login to the same account
10. Turn on the connection and you should be connected through VPN to your Pi-hole!

# Further Steps and Resources
Occasionally, login to your pi and perform an apt update. Then restart your pi, unbound and tailscale services. 
1. pihole -up
2. sudo apt update
3. sudo systemctl restart unbound
4. sudo tailscale update

You will likely want to modify the block and allow list on your Pi-hole. Checkout this github containing updated block lists you can add to your pi-hole.

https://github.com/hagezi/dns-blocklists?tab=readme-ov-file#proplus

