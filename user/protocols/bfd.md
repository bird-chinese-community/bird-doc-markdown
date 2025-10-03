## BFD

### Introduction

Bidirectional Forwarding Detection (BFD) is not a routing protocol itself, it
is an independent tool providing liveness and failure detection. Routing
protocols like OSPF and BGP use integrated periodic "hello" messages to monitor
liveness of neighbors, but detection times of these mechanisms are high (e.g. 40
seconds by default in OSPF, could be set down to several seconds). BFD offers
universal, fast and low-overhead mechanism for failure detection, which could be
attached to any routing protocol in an advisory role.

BFD consists of mostly independent BFD sessions. Each session monitors an
unicast bidirectional path between two BFD-enabled routers. This is done by
periodically sending control packets in both directions. BFD does not handle
neighbor discovery, BFD sessions are created on demand by request of other
protocols (like OSPF or BGP), which supply appropriate information like IP
addresses and associated interfaces. When a session changes its state, these
protocols are notified and act accordingly (e.g. break an OSPF adjacency when
the BFD session went down).

BIRD implements basic BFD behavior as defined in <a href="https://datatracker.ietf.org/doc/rfc5880" class="rfc">RFC
5880</a> (some
advanced features like the echo mode are not implemented), IP transport for BFD
as defined in <a href="https://datatracker.ietf.org/doc/rfc5881" class="rfc">RFC
5881</a> and <a href="https://datatracker.ietf.org/doc/rfc5883" class="rfc">RFC
5883</a> and interaction with client
protocols as defined in <a href="https://datatracker.ietf.org/doc/rfc5882" class="rfc">RFC
5882</a>.

BFD packets are sent with a dynamic source port number. Linux systems use by
default a bit different dynamic port range than the IANA approved one
(49152-65535). If you experience problems with compatibility, please adjust
`/proc/sys/net/ipv4/ip_local_port_range`.

### Configuration

BFD configuration consists mainly of multiple definitions of interfaces.
Most BFD config options are session specific. When a new session is requested
and dynamically created, it is configured from one of these definitions. For
sessions to directly connected neighbors, `interface` definitions are chosen
based on the interface associated with the session, while `multihop`
definition is used for multihop sessions. If no definition is relevant, the
session is just created with the default configuration. Therefore, an empty BFD
configuration is often sufficient.

Note that to use BFD for other protocols like OSPF or BGP, these protocols
also have to be configured to request BFD sessions, usually by `bfd` option.
In BGP case, it is also possible to specify per-peer BFD session options (e.g.
rx/tx intervals) as a part of the `bfd` option.

A BFD instance not associated with any VRF handles session requests from all
other protocols, even ones associated with a VRF. Such setup would work for
single-hop BFD sessions if `net.ipv4.udp_l3mdev_accept` sysctl is enabled,
but does not currently work for multihop sessions. Another approach is to
configure multiple BFD instances, one for each VRF (including the default VRF).
Each BFD instance associated with a VRF (regular or default) only handles
session requests from protocols in the same VRF.

Some of BFD session options require *time* value, which has to be specified
with the appropriate unit: *number* `s`\|`ms`\|`us`. Although microseconds
are allowed as units, practical minimum values are usually in order of tens of
milliseconds.


    protocol bfd [<name>] {
        accept [ipv4|ipv6] [direct|multihop];
        strict bind <switch>;
        zero udp6 checksum rx <switch>;
        interface <interface pattern> {
            interval <time>;
            min rx interval <time>;
            min tx interval <time>;
            idle tx interval <time>;
            multiplier <number>;
            passive <switch>;
            authentication none;
            authentication simple;
            authentication [meticulous] keyed md5|sha1;
            password "<text>";
            password "<text>" {
                id <number>;
                generate from "<date>";
                generate to "<date>";
                accept from "<date>";
                accept to "<date>";
                from "<date>";
                to "<date>";
            };
        };
        multihop {
            interval <time>;
            min rx interval <time>;
            min tx interval <time>;
            idle tx interval <time>;
            multiplier <number>;
            passive <switch>;
        };
        neighbor <ip> [dev "<interface>"] [local <ip>] [multihop <switch>];
    }

<span id="bfd-accept" class="code">accept \[ipv4\|ipv6\] \[direct\|multihop\]</span>  
A BFD protocol instance accepts (by default) all BFD session requests
    (with regard to VRF restrictions, see above). This option controls
    whether IPv4 / IPv6 and direct / multihop session requests are accepted
    (and which listening sockets are opened). It can be used, for example,
    to configure separate BFD protocol instances for IPv4 and for IPv6
    sessions.

<span id="bfd-strict-bind" class="code">strict bind *switch*</span>  
Specify whether each BFD interface should use a separate listening
    socket bound to its local address, or just use a shared listening socket
    accepting all addresses. Binding to a specific address could be useful
    in cases like running multiple BIRD instances on a machine, each
    handling a different set of interfaces. Default: disabled.

<span id="bfd-zero-udp6-checksum-rx" class="code">zero udp6 checksum rx *switch*</span>  
UDP checksum computation is optional in IPv4 while it is mandatory in
    IPv6. Some BFD implementations send UDP datagrams with zero (blank)
    checksum even in IPv6 case. This option configures BFD listening sockets
    to accept such datagrams. It is available only on platforms that support
    the relevant socket option (e.g. `UDP_NO_CHECK6_RX` on Linux).
    Default: disabled.

<span id="bfd-iface" class="code">interface *pattern* \[, *...*\] { *options* }</span>  
Interface definitions allow to specify options for sessions associated
    with such interfaces and also may contain interface specific options.
    See [interface](#proto-iface) common option for a detailed
    description of interface patterns. Note that contrary to the behavior of
    `interface` definitions of other protocols, BFD protocol would accept
    sessions (in default configuration) even on interfaces not covered by
    such definitions.

<span id="bfd-multihop" class="code">multihop { *options* }</span>  
Multihop definitions allow to specify options for multihop BFD sessions,
    in the same manner as `interface` definitions are used for directly
    connected sessions. Currently only one such definition (for all multihop
    sessions) could be used.

<span id="bfd-neighbor" class="code">neighbor *ip* \[dev "*interface*"\] \[local *ip*\] \[multihop *switch*\]</span>  
BFD sessions are usually created on demand as requested by other
    protocols (like OSPF or BGP). This option allows to explicitly add
    a BFD session to the specified neighbor regardless of such requests.

The session is identified by the IP address of the neighbor, with
    optional specification of used interface and local IP. By default
    the neighbor must be directly connected, unless the session is
    configured as multihop. Note that local IP must be specified for
    multihop sessions.

Session specific options (part of `interface` and `multihop` definitions):

<span id="bfd-interval" class="code">interval *time*</span>  
BFD ensures availability of the forwarding path associated with the
    session by periodically sending BFD control packets in both
    directions. The rate of such packets is controlled by two options,
    `min rx interval` and `min tx interval` (see below). This option
    is just a shorthand to set both of these options together.

<span id="bfd-min-rx-interval" class="code">min rx interval *time*</span>  
This option specifies the minimum RX interval, which is announced to the
    neighbor and used there to limit the neighbor's rate of generated BFD
    control packets. Default: 10 ms.

<span id="bfd-min-tx-interval" class="code">min tx interval *time*</span>  
This option specifies the desired TX interval, which controls the rate
    of generated BFD control packets (together with `min rx interval`
    announced by the neighbor). Note that this value is used only if the BFD
    session is up, otherwise the value of `idle tx interval` is used
    instead. Default: 100 ms.

<span id="bfd-idle-tx-interval" class="code">idle tx interval *time*</span>  
In order to limit unnecessary traffic in cases where a neighbor is not
    available or not running BFD, the rate of generated BFD control packets
    is lower when the BFD session is not up. This option specifies the
    desired TX interval in such cases instead of `min tx interval`.
    Default: 1 s.

<span id="bfd-multiplier" class="code">multiplier *number*</span>  
Failure detection time for BFD sessions is based on established rate of
    BFD control packets (`min rx/tx interval`) multiplied by this
    multiplier, which is essentially (ignoring jitter) a number of missed
    packets after which the session is declared down. Note that rates and
    multipliers could be different in each direction of a BFD session.
    Default: 5.

<span id="bfd-passive" class="code">passive *switch*</span>  
Generally, both BFD session endpoints try to establish the session by
    sending control packets to the other side. This option allows to enable
    passive mode, which means that the router does not send BFD packets
    until it has received one from the other side. Default: disabled.

<span id="bfd-authentication-none" class="code">authentication none</span>  
No passwords are sent in BFD packets. This is the default value.

<span id="bfd-authentication-simple" class="code">authentication simple</span>  
Every packet carries 16 bytes of password. Received packets lacking this
    password are ignored. This authentication mechanism is very weak.

<span id="bfd-authentication-keyed" class="code">authentication \[meticulous\] keyed md5\|sha1</span>  
An authentication code is appended to each packet. The cryptographic
    algorithm is keyed MD5 or keyed SHA-1. Note that the algorithm is common
    for all keys (on one interface), in contrast to OSPF or RIP, where it
    is a per-key option. Passwords (keys) are not sent open via network.

The `meticulous` variant means that cryptographic sequence numbers
    are increased for each sent packet, while in the basic variant they are
    increased about once per second. Generally, the `meticulous` variant
    offers better resistance to replay attacks but may require more
    computation.

<span id="bfd-password" class="code">password "*text*"</span>  
Specifies a password used for authentication. See [password](#proto-pass) common option for detailed description. Note that
    password option `algorithm` is not available in BFD protocol. The
    algorithm is selected by `authentication` option for all passwords.

### Example


    protocol bfd {
        interface "eth*" {
            min rx interval 20 ms;
            min tx interval 50 ms;
            idle tx interval 300 ms;
        };
        interface "gre*" {
            interval 200 ms;
            multiplier 10;
            passive;
        };
        multihop {
            interval 200 ms;
            multiplier 10;
        };

        neighbor 192.168.1.10;
        neighbor 192.168.2.2 dev "eth2";
        neighbor 192.168.10.1 local 192.168.1.1 multihop;
    }
