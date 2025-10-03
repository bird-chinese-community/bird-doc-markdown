## OSPF

### Introduction

Open Shortest Path First (OSPF) is a quite complex interior gateway
protocol. The current IPv4 version (OSPFv2) is defined in <a href="https://datatracker.ietf.org/doc/rfc2328" class="rfc">RFC
2328</a> and
the current IPv6 version (OSPFv3) is defined in <a href="https://datatracker.ietf.org/doc/rfc5340" class="rfc">RFC
5340</a> It's a link
state (a.k.a. shortest path first) protocol -- each router maintains a database
describing the autonomous system's topology. Each participating router has an
identical copy of the database and all routers run the same algorithm
calculating a shortest path tree with themselves as a root. OSPF chooses the
least cost path as the best path.

In OSPF, the autonomous system can be split to several areas in order to
reduce the amount of resources consumed for exchanging the routing information
and to protect the other areas from incorrect routing data. Topology of the area
is hidden to the rest of the autonomous system.

Another very important feature of OSPF is that it can keep routing information
from other protocols (like Static or BGP) in its link state database as external
routes. Each external route can be tagged by the advertising router, making it
possible to pass additional information between routers on the boundary of the
autonomous system.

OSPF quickly detects topological changes in the autonomous system (such as
router interface failures) and calculates new loop-free routes after a short
period of convergence. Only a minimal amount of routing traffic is involved.

Each router participating in OSPF routing periodically sends Hello messages
to all its interfaces. This allows neighbors to be discovered dynamically. Then
the neighbors exchange theirs parts of the link state database and keep it
identical by flooding updates. The flooding process is reliable and ensures that
each router detects all changes.

### Configuration

First, the desired OSPF version can be specified by using `ospf v2` or
`ospf v3` as a protocol type. By default, OSPFv2 is used. In the main part of
configuration, there can be multiple definitions of OSPF areas, each with a
different id. These definitions includes many other switches and multiple
definitions of interfaces. Definition of interface may contain many switches and
constant definitions and list of neighbors on nonbroadcast networks.

OSPFv2 needs one IPv4 channel. OSPFv3 needs either one IPv6 channel, or one
IPv4 channel (<a href="https://datatracker.ietf.org/doc/rfc5838" class="rfc">RFC
5838</a>). Therefore, it is possible to use OSPFv3 for both
IPv4 and Pv6 routing, but it is necessary to have two protocol instances anyway.
If no channel is configured, appropriate channel is defined with default
parameters.


    protocol ospf [v2|v3] <name> {
        rfc1583compat <switch>;
        rfc5838 <switch>;
        instance id <number>;
        stub router <switch>;
        tick <number>;
        ecmp <switch> [limit <number>];
        merge external <switch>;
        graceful restart <switch>|aware;
        graceful restart time <number>;
        area <id> {
            stub;
            nssa;
            summary <switch>;
            default nssa <switch>;
            default cost <number>;
            default cost2 <number>;
            translator <switch>;
            translator stability <number>;

            networks {
                <prefix>;
                <prefix> hidden;
            };
            external {
                <prefix>;
                <prefix> hidden;
                <prefix> tag <number>;
            };
            stubnet <prefix>;
            stubnet <prefix> {
                hidden <switch>;
                summary <switch>;
                cost <number>;
            };
            interface <interface pattern> [instance <number>] {
                cost <number>;
                stub <switch>;
                hello <number>;
                poll <number>;
                retransmit <number>;
                priority <number>;
                wait <number>;
                dead count <number>;
                dead <number>;
                secondary <switch>;
                rx buffer [normal|large|<number>];
                tx length <number>;
                type [broadcast|bcast|pointopoint|ptp|
                    nonbroadcast|nbma|pointomultipoint|ptmp];
                link lsa suppression <switch>;
                strict nonbroadcast <switch>;
                real broadcast <switch>;
                ptp netmask <switch>;
                ptp address <switch>;
                check link <switch>;
                bfd <switch>;
                ecmp weight <number>;
                ttl security [<switch>; | tx only]
                tx class|dscp <number>;
                tx priority <number>;
                authentication none|simple|cryptographic;
                password "<text>";
                password "<text>" {
                    id <number>;
                    generate from "<date>";
                    generate to "<date>";
                    accept from "<date>";
                    accept to "<date>";
                    from "<date>";
                    to "<date>";
                    algorithm ( keyed md5 | keyed sha1 | hmac sha1 | hmac sha256 | hmac sha384 | hmac sha512 );
                };
                neighbors {
                    <ip>;
                    <ip> eligible;
                };
            };
            virtual link <id> [instance <number>] {
                hello <number>;
                retransmit <number>;
                wait <number>;
                dead count <number>;
                dead <number>;
                authentication none|simple|cryptographic;
                password "<text>";
                password "<text>" {
                    id <number>;
                    generate from "<date>";
                    generate to "<date>";
                    accept from "<date>";
                    accept to "<date>";
                    from "<date>";
                    to "<date>";
                    algorithm ( keyed md5 | keyed sha1 | hmac sha1 | hmac sha256 | hmac sha384 | hmac sha512 );
                };
            };
        };
    }

<span id="ospf-rfc1583compat" class="code">rfc1583compat *switch*</span>  
This option controls compatibility of routing table calculation with
    <a href="https://datatracker.ietf.org/doc/rfc1583" class="rfc">RFC
1583</a>. Default value is no.

<span id="ospf-rfc5838" class="code">rfc5838 *switch*</span>  
Basic OSPFv3 is limited to IPv6 unicast routing. The <a href="https://datatracker.ietf.org/doc/rfc5838" class="rfc">RFC
5838</a>
    extension defines support for more address families (IPv4, IPv6, both
    unicast and multicast). The extension is enabled by default, but can be
    disabled if necessary, as it restricts the range of available instance
    IDs. Default value is yes.

<span id="ospf-instance-id" class="code">instance id *number*</span>  
When multiple OSPF protocol instances are active on the same links, they
    should use different instance IDs to distinguish their packets. Although
    it could be done on per-interface basis, it is often preferred to set
    one instance ID to whole OSPF domain/topology (e.g., when multiple
    instances are used to represent separate logical topologies on the same
    physical network). This option specifies the instance ID for all
    interfaces of the OSPF instance, but can be overridden by
    `interface` option. Default value is 0 unless OSPFv3-AF extended
    address families are used, see <a href="https://datatracker.ietf.org/doc/rfc5838" class="rfc">RFC
5838</a> for that case.

<span id="ospf-stub-router" class="code">stub router *switch*</span>  
This option configures the router to be a stub router, i.e., a router
    that participates in the OSPF topology but does not allow transit
    traffic. In OSPFv2, this is implemented by advertising maximum metric
    for outgoing links. In OSPFv3, the stub router behavior is announced by
    clearing the R-bit in the router LSA. See <a href="https://datatracker.ietf.org/doc/rfc6987" class="rfc">RFC
6987</a> for details.
    Default value is no.

<span id="ospf-tick" class="code">tick *number*</span>  
The routing table calculation and clean-up of areas' databases is not
    performed when a single link state change arrives. To lower the CPU
    utilization, it's processed later at periodical intervals of *number*
    seconds. The default value is 1.

<span id="ospf-ecmp" class="code">ecmp *switch* \[limit *number*\]</span>  
This option specifies whether OSPF is allowed to generate ECMP
    (equal-cost multipath) routes. Such routes are used when there are
    several directions to the destination, each with the same (computed)
    cost. This option also allows to specify a limit on maximum number of
    nexthops in one route. By default, ECMP is enabled if supported by
    Kernel. Default value of the limit is 16.

<span id="ospf-merge-external" class="code">merge external *switch*</span>  
This option specifies whether OSPF should merge external routes from
    different routers/LSAs for the same destination. When enabled together
    with `ecmp`, equal-cost external routes will be combined to multipath
    routes in the same way as regular routes. When disabled, external routes
    from different LSAs are treated as separate even if they represents the
    same destination. Default value is no.

<span id="ospf-graceful-restart" class="code">graceful restart *switch*\|aware</span>  
When an OSPF instance is restarted, neighbors break adjacencies and
    recalculate their routing tables, which disrupts packet forwarding even
    when the forwarding plane of the restarting router remains intact.
    <a href="https://datatracker.ietf.org/doc/rfc3623" class="rfc">RFC
3623</a> specifies a graceful restart mechanism to alleviate this
    issue. For OSPF graceful restart, restarting router originates
    Grace-LSAs, announcing intent to do graceful restart. Neighbors
    receiving these LSAs enter helper mode, in which they ignore breakdown
    of adjacencies, behave as if nothing is happening and keep old routes.
    When adjacencies are reestablished, the restarting router flushes
    Grace-LSAs and graceful restart is ended.

This option controls the graceful restart mechanism. It has three
    states: Disabled, when no support is provided. Aware, when graceful
    restart helper mode is supported, but no local graceful restart is
    allowed (i.e. helper-only role). Enabled, when the full graceful restart
    support is provided (i.e. both restarting and helper role). Note that
    proper support for local graceful restart requires also configuration of
    other protocols. Default: aware.

<span id="ospf-graceful-restart-time" class="code">graceful restart time *number*</span>  
The restart time is announced in the Grace-LSA and specifies how long
    neighbors should wait for proper end of the graceful restart before
    exiting helper mode prematurely. Default: 120 seconds.

<span id="ospf-area" class="code">area *id*</span>  
This defines an OSPF area with given area ID (an integer or an IPv4
    address, similarly to a router ID). The most important area is the
    backbone (ID 0) to which every other area must be connected.

<span id="ospf-stub" class="code">stub</span>  
This option configures the area to be a stub area. External routes are
    not flooded into stub areas. Also summary LSAs can be limited in stub
    areas (see option `summary`). By default, the area is not a stub
    area.

<span id="ospf-nssa" class="code">nssa</span>  
This option configures the area to be a NSSA (Not-So-Stubby Area). NSSA
    is a variant of a stub area which allows a limited way of external route
    propagation. Global external routes are not propagated into a NSSA, but
    an external route can be imported into NSSA as a (area-wide) NSSA-LSA
    (and possibly translated and/or aggregated on area boundary). By
    default, the area is not NSSA.

<span id="ospf-summary" class="code">summary *switch*</span>  
This option controls propagation of summary LSAs into stub or NSSA
    areas. If enabled, summary LSAs are propagated as usual, otherwise just
    the default summary route (0.0.0.0/0) is propagated (this is sometimes
    called totally stubby area). If a stub area has more area boundary
    routers, propagating summary LSAs could lead to more efficient routing
    at the cost of larger link state database. Default value is no.

<span id="ospf-default-nssa" class="code">default nssa *switch*</span>  
When `summary` option is enabled, default summary route is no longer
    propagated to the NSSA. In that case, this option allows to originate
    default route as NSSA-LSA to the NSSA. Default value is no.

<span id="ospf-default-cost" class="code">default cost *number*</span>  
This option controls the cost of a default route propagated to stub and
    NSSA areas. Default value is 1000.

<span id="ospf-default-cost2" class="code">default cost2 *number*</span>  
When a default route is originated as NSSA-LSA, its cost can use either
    type 1 or type 2 metric. This option allows to specify the cost of a
    default route in type 2 metric. By default, type 1 metric (option
    `default cost`) is used.

<span id="ospf-translator" class="code">translator *switch*</span>  
This option controls translation of NSSA-LSAs into external LSAs. By
    default, one translator per NSSA is automatically elected from area
    boundary routers. If enabled, this area boundary router would
    unconditionally translate all NSSA-LSAs regardless of translator
    election. Default value is no.

<span id="ospf-translator-stability" class="code">translator stability *number*</span>  
This option controls the translator stability interval (in seconds).
    When the new translator is elected, the old one keeps translating until
    the interval is over. Default value is 40.

<span id="ospf-networks" class="code">networks { *set* }</span>  
Definition of area IP ranges. This is used in summary LSA origination.
    Hidden networks are not propagated into other areas.

<span id="ospf-external" class="code">external { *set* }</span>  
Definition of external area IP ranges for NSSAs. This is used for
    NSSA-LSA translation. Hidden networks are not translated into external
    LSAs. Networks can have configured route tag.

<span id="ospf-stubnet" class="code">stubnet *prefix* { *options* }</span>  
Stub networks are networks that are not transit networks between OSPF
    routers. They are also propagated through an OSPF area as a part of a
    link state database. By default, BIRD generates a stub network record
    for each primary network address on each OSPF interface that does not
    have any OSPF neighbors, and also for each non-primary network address
    on each OSPF interface. This option allows to alter a set of stub
    networks propagated by this router.

Each instance of this option adds a stub network with given network
    prefix to the set of propagated stub network, unless option `hidden`
    is used. It also suppresses default stub networks for given network
    prefix. When option `summary` is used, also default stub networks
    that are subnetworks of given stub network are suppressed. This might be
    used, for example, to aggregate generated stub networks.

<span id="ospf-iface" class="code">interface *pattern* \[instance *number*\]</span>  
Defines that the specified interfaces belong to the area being defined.
    See [interface](#proto-iface) common option for detailed
    description. In OSPFv2, extended interface clauses are used, because
    each network prefix is handled as a separate virtual interface.

You can specify alternative instance ID for the interface definition,
    therefore it is possible to have several instances of that interface
    with different options or even in different areas. For OSPFv2, instance
    ID support is an extension (<a href="https://datatracker.ietf.org/doc/rfc6549" class="rfc">RFC
6549</a>) and is supposed to be set
    per-protocol. For OSPFv3, it is an integral feature.

<span id="ospf-virtual-link" class="code">virtual link *id* \[instance *number*\]</span>  
Virtual link to router with the router id. Virtual link acts as a
    point-to-point interface belonging to backbone. The actual area is used
    as a transport area. This item cannot be in the backbone. Like with
    `interface` option, you could also use several virtual links to one
    destination with different instance IDs.

<span id="ospf-cost" class="code">cost *number*</span>  
Specifies output cost (metric) of an interface. Default value is 10.

<span id="ospf-stub-iface" class="code">stub *switch*</span>  
If set to interface it does not listen to any packet and does not send
    any hello. Default value is no.

<span id="ospf-hello" class="code">hello *number*</span>  
Specifies interval in seconds between sending of Hello messages. Beware,
    all routers on the same network need to have the same hello interval.
    Default value is 10.

<span id="ospf-poll" class="code">poll *number*</span>  
Specifies interval in seconds between sending of Hello messages for some
    neighbors on NBMA network. Default value is 20.

<span id="ospf-retransmit" class="code">retransmit *number*</span>  
Specifies interval in seconds between retransmissions of unacknowledged
    updates. Default value is 5.

<span id="ospf-transmit-delay" class="code">transmit delay *number*</span>  
Specifies estimated transmission delay of link state updates send over
    the interface. The value is added to LSA age of LSAs propagated through
    it. Default value is 1.

<span id="ospf-priority" class="code">priority *number*</span>  
On every multiple access network (e.g., the Ethernet) Designated Router
    and Backup Designated router are elected. These routers have some special
    functions in the flooding process. Higher priority increases preferences
    in this election. Routers with priority 0 are not eligible. Default
    value is 1.

<span id="ospf-wait" class="code">wait *number*</span>  
After start, router waits for the specified number of seconds between
    starting election and building adjacency. Default value is 4\**hello*.

<span id="ospf-dead-count" class="code">dead count *number*</span>  
When the router does not receive any messages from a neighbor in
    *dead count*\**hello* seconds, it will consider the neighbor down.

<span id="ospf-dead" class="code">dead *number*</span>  
When the router does not receive any messages from a neighbor in
    *dead* seconds, it will consider the neighbor down. If both directives
    `dead count` and `dead` are used, `dead` has precedence.

<span id="ospf-rx-buffer" class="code">rx buffer *number*</span>  
This option allows to specify the size of buffers used for packet
    processing. The buffer size should be bigger than maximal size of any
    packets. By default, buffers are dynamically resized as needed, but a
    fixed value could be specified. Value `large` means maximal allowed
    packet size - 65535.

<span id="ospf-tx-length" class="code">tx length *number*</span>  
Transmitted OSPF messages that contain large amount of information are
    segmented to separate OSPF packets to avoid IP fragmentation. This
    option specifies the soft ceiling for the length of generated OSPF
    packets. Default value is the MTU of the network interface. Note that
    larger OSPF packets may still be generated if underlying OSPF messages
    cannot be splitted (e.g. when one large LSA is propagated).

<span id="ospf-type-bcast" class="code">type broadcast\|bcast</span>  
BIRD detects a type of a connected network automatically, but sometimes
    it's convenient to force use of a different type manually. On broadcast
    networks (like ethernet), flooding and Hello messages are sent using
    multicasts (a single packet for all the neighbors). A designated router
    is elected and it is responsible for synchronizing the link-state
    databases and originating network LSAs. This network type cannot be used
    on physically NBMA networks and on unnumbered networks (networks without
    proper IP prefix).

<span id="ospf-type-ptp" class="code">type pointopoint\|ptp</span>  
Point-to-point networks connect just 2 routers together. No election is
    performed and no network LSA is originated, which makes it simpler and
    faster to establish. This network type is useful not only for physically
    PtP ifaces (like PPP or tunnels), but also for broadcast networks used
    as PtP links. This network type cannot be used on physically NBMA
    networks.

<span id="ospf-type-nbma" class="code">type nonbroadcast\|nbma</span>  
On NBMA networks, the packets are sent to each neighbor separately
    because of lack of multicast capabilities. Like on broadcast networks,
    a designated router is elected, which plays a central role in propagation
    of LSAs. This network type cannot be used on unnumbered networks.

<span id="ospf-type-ptmp" class="code">type pointomultipoint\|ptmp</span>  
This is another network type designed to handle NBMA networks. In this
    case the NBMA network is treated as a collection of PtP links. This is
    useful if not every pair of routers on the NBMA network has direct
    communication, or if the NBMA network is used as an (possibly
    unnumbered) PtP link.

<span id="ospf-link-lsa-suppression" class="code">link lsa suppression *switch*</span>  
In OSPFv3, link LSAs are generated for each link, announcing link-local
    IPv6 address of the router to its local neighbors. These are useless on
    PtP or PtMP networks and this option allows to suppress the link LSA
    origination for such interfaces. The option is ignored on other than PtP
    or PtMP interfaces. Default value is no.

<span id="ospf-strict-nonbroadcast" class="code">strict nonbroadcast *switch*</span>  
If set, don't send hello to any undefined neighbor. This switch is
    ignored on other than NBMA or PtMP interfaces. Default value is no.

<span id="ospf-real-broadcast" class="code">real broadcast *switch*</span>  
In `type broadcast` or `type ptp` network configuration, OSPF
    packets are sent as IP multicast packets. This option changes the
    behavior to using old-fashioned IP broadcast packets. This may be useful
    as a workaround if IP multicast for some reason does not work or does
    not work reliably. This is a non-standard option and probably is not
    interoperable with other OSPF implementations. Default value is no.

<span id="ospf-ptp-netmask" class="code">ptp netmask *switch*</span>  
In `type ptp` network configurations, OSPFv2 implementations should
    ignore received netmask field in hello packets and should send hello
    packets with zero netmask field on unnumbered PtP links. But some OSPFv2
    implementations perform netmask checking even for PtP links.

This option specifies whether real netmask will be used in hello packets
    on `type ptp` interfaces. You should ignore this option unless you
    meet some compatibility problems related to this issue. Default value is
    no for unnumbered PtP links, yes otherwise.

<span id="ospf-ptp-address" class="code">ptp address *switch*</span>  
In `type ptp` network configurations, OSPFv2 implementations should
    use IP address for regular PtP links and interface id for unnumbered PtP
    links in data field of link description records in router LSA. This data
    field has only local meaning for PtP links, but some broken OSPFv2
    implementations assume there is an IP address and use it as a next hop
    in SPF calculations. Note that interface id for unnumbered PtP links is
    necessary when graceful restart is enabled to distinguish PtP links with
    the same local IP address.

This option specifies whether an IP address will be used in data field
    for `type ptp` interfaces, it is ignored for other interfaces. You
    should ignore this option unless you meet some compatibility problems
    related to this issue. Default value is no for unnumbered PtP links when
    graceful restart is enabled, yes otherwise.

<span id="ospf-check-link" class="code">check link *switch*</span>  
If set, a hardware link state (reported by OS) is taken into consideration.
    When a link disappears (e.g. an ethernet cable is unplugged), neighbors
    are immediately considered unreachable and only the address of the iface
    (instead of whole network prefix) is propagated. It is possible that
    some hardware drivers or platforms do not implement this feature.
    Default value is yes.

<span id="ospf-bfd" class="code">bfd *switch*</span>  
OSPF could use BFD protocol as an advisory mechanism for neighbor
    liveness and failure detection. If enabled, BIRD setups a BFD session
    for each OSPF neighbor and tracks its liveness by it. This has an
    advantage of an order of magnitude lower detection times in case of
    failure. Note that BFD protocol also has to be configured, see
    [BFD](#bfd) section for details. Default value is no.

<span id="ospf-ttl-security" class="code">ttl security \[*switch* \| tx only\]</span>  
TTL security is a feature that protects routing protocols from remote
    spoofed packets by using TTL 255 instead of TTL 1 for protocol packets
    destined to neighbors. Because TTL is decremented when packets are
    forwarded, it is non-trivial to spoof packets with TTL 255 from remote
    locations. Note that this option would interfere with OSPF virtual
    links.

If this option is enabled, the router will send OSPF packets with TTL
    255 and drop received packets with TTL less than 255. If this option si
    set to `tx only`, TTL 255 is used for sent packets, but is not
    checked for received packets. Default value is no.

<span id="ospf-tx-class" class="code">tx class\|dscp\|priority *number*</span>  
These options specify the ToS/DiffServ/Traffic class/Priority of the
    outgoing OSPF packets. See [tx class](#proto-tx-class) common
    option for detailed description.

<span id="ospf-ecmp-weight" class="code">ecmp weight *number*</span>  
When ECMP (multipath) routes are allowed, this value specifies a
    relative weight used for nexthops going through the iface. Allowed
    values are 1-256. Default value is 1.

<span id="ospf-auth-none" class="code">authentication none</span>  
No passwords are sent in OSPF packets. This is the default value.

<span id="ospf-auth-simple" class="code">authentication simple</span>  
Every packet carries 8 bytes of password. Received packets lacking this
    password are ignored. This authentication mechanism is very weak.
    This option is not available in OSPFv3.

<span id="ospf-auth-cryptographic" class="code">authentication cryptographic</span>  
An authentication code is appended to every packet. The specific
    cryptographic algorithm is selected by option `algorithm` for each
    key. The default cryptographic algorithm for OSPFv2 keys is Keyed-MD5
    and for OSPFv3 keys is HMAC-SHA-256. Passwords are not sent open via
    network, so this mechanism is quite secure. Packets can still be read by
    an attacker.

<span id="ospf-pass" class="code">password "*text*"</span>  
Specifies a password used for authentication. See
    [password](#proto-pass) common option for detailed
    description.

<span id="ospf-neighbors" class="code">neighbors { *set* }</span>  
A set of neighbors to which Hello messages on NBMA or PtMP networks are
    to be sent. For NBMA networks, some of them could be marked as eligible.
    In OSPFv3, link-local addresses should be used, using global ones is
    possible, but it is nonstandard and might be problematic. And definitely,
    link-local and global addresses should not be mixed.

### Attributes

OSPF defines four route attributes. Each internal route has a `metric`.

Metric is ranging from 1 to infinity (65535). External routes use
`metric type 1` or `metric type 2`. A `metric of type 1` is comparable
with internal `metric`, a `metric of type 2` is always longer than any
`metric of type 1` or any `internal metric`. `Internal metric` or
`metric of type 1` is stored in attribute `ospf_metric1`, `metric type
2` is stored in attribute `ospf_metric2`.

When both metrics are specified then `metric of type 2` is used. This is
relevant e.g. when a type 2 external route is propagated from one OSPF domain to
another and `ospf_metric1` is an internal distance to the original ASBR,
while `ospf_metric2` stores the type 2 metric. Note that in such cases if
`ospf_metric1` is non-zero then `ospf_metric2` is increased by one to
ensure monotonicity of metric, as internal distance is reset to zero when an
external route is announced.

Each external route can also carry attribute `ospf_tag` which is a 32-bit
integer which is used when exporting routes to other protocols; otherwise, it
doesn't affect routing inside the OSPF domain at all. The fourth attribute
`ospf_router_id` is a router ID of the router advertising that route /
network. This attribute is read-only. Default is `ospf_metric2 = 10000` and
`ospf_tag = 0`.

### Example


    protocol ospf MyOSPF {
        ipv4 {
            export filter {
                if source = RTS_BGP then {
                    ospf_metric1 = 100;
                    accept;
                }
                reject;
            };
        };
        area 0.0.0.0 {
            interface "eth*" {
                cost 11;
                hello 15;
                priority 100;
                retransmit 7;
                authentication simple;
                password "aaa";
            };
            interface "ppp*" {
                cost 100;
                authentication cryptographic;
                password "abc" {
                    id 1;
                    generate to "2023-04-22 11:00:06";
                    accept from "2021-01-17 12:01:05";
                    algorithm hmac sha384;
                };
                password "def" {
                    id 2;
                    generate to "2025-07-22";
                    accept from "2021-02-22";
                    algorithm hmac sha512;
                };
            };
            interface "arc0" {
                cost 10;
                stub yes;
            };
            interface "arc1";
        };
        area 120 {
            stub yes;
            networks {
                172.16.1.0/24;
                172.16.2.0/24 hidden;
            };
            interface "-arc0" , "arc*" {
                type nonbroadcast;
                authentication none;
                strict nonbroadcast yes;
                wait 120;
                poll 40;
                dead count 8;
                neighbors {
                    192.168.120.1 eligible;
                    192.168.120.2;
                    192.168.120.10;
                };
            };
        };
    }
