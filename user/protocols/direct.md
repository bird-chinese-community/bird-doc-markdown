## Direct

The Direct protocol is a simple generator of device routes for all the
directly connected networks according to the list of interfaces provided by the
kernel via the Device protocol. The Direct protocol supports both IPv4 and IPv6
channels; both can be configured simultaneously. It can also be configured with
[IPv6 SADR](#ip-sadr-routes) channel instead of regular IPv6
channel in order to be used together with SADR-enabled Babel protocol.

The question is whether it is a good idea to have such device routes in BIRD
routing table. OS kernel usually handles device routes for directly connected
networks by itself so we don't need (and don't want) to export these routes to
the kernel protocol. OSPF protocol creates device routes for its interfaces
itself and BGP protocol is usually used for exporting aggregate routes. But the
Direct protocol is necessary for distance-vector protocols like RIP or Babel to
announce local networks.

There are just few configuration options for the Direct protocol:

<span id="direct-iface" class="code">interface *pattern* \[, *...*\]</span>  
By default, the Direct protocol will generate device routes for all the
    interfaces available. If you want to restrict it to some subset of
    interfaces or addresses (e.g. if you're using multiple routing tables
    for policy routing and some of the policy domains don't contain all
    interfaces), just use this clause. See [interface](#proto-iface)
    common option for detailed description. The Direct protocol uses
    extended interface clauses.

<span id="direct-check-link" class="code">check link *switch*</span>  
If enabled, a hardware link state (reported by OS) is taken into
    consideration. Routes for directly connected networks are generated only
    if link up is reported and they are withdrawn when link disappears
    (e.g., an ethernet cable is unplugged). Default value is no.

Direct device routes don't contain any specific attributes.

Example config might look like this:


    protocol direct {
        ipv4;
        ipv6;
        interface -"arc*", "*";     # Exclude the ARCnets
    }
