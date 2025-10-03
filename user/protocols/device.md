## Device

The Device protocol is not a real routing protocol. It doesn't generate any
routes and it only serves as a module for getting information about network
interfaces from the kernel. This protocol supports no channel.

Except for very unusual circumstances, you probably should include this
protocol in the configuration since almost all other protocols require network
interfaces to be defined for them to work with.

### Configuration

<span id="device-scan-time" class="code">scan time *number*</span>  
Time in seconds between two scans of the network interface list. On
    systems where we are notified about interface status changes
    asynchronously (such as newer versions of Linux), we need to scan the
    list only in order to avoid confusion by lost notification messages,
    so the default time is set to a large value.

<span id="device-iface" class="code">interface *pattern* \[, *...*\]</span>  
By default, the Device protocol handles all interfaces without any
    configuration. Interface definitions allow to specify optional
    parameters for specific interfaces. See [interface](#proto-iface) common option for detailed description. Currently only
    one interface option is available:

<span id="device-preferred" class="code">preferred *ip*</span>  
If a network interface has more than one IP address, BIRD chooses one of
    them as a preferred one. Preferred IP address is used as source address
    for packets or announced next hop by routing protocols. Precisely, BIRD
    chooses one preferred IPv4 address, one preferred IPv6 address and one
    preferred link-local IPv6 address. By default, BIRD chooses the first
    found IP address as the preferred one.

This option allows to specify which IP address should be preferred. May
    be used multiple times for different address classes (IPv4, IPv6, IPv6
    link-local). In all cases, an address marked by operating system as
    secondary cannot be chosen as the primary one.

As the Device protocol doesn't generate any routes, it cannot have
any attributes. Example configuration looks like this:


    protocol device {
        scan time 10;       # Scan the interfaces often
        interface "eth0" {
            preferred 192.168.1.1;
            preferred 2001:db8:1:10::1;
        };
    }
