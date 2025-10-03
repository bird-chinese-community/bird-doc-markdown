## RIP

### Introduction

The RIP protocol (also sometimes called Rest In Pieces) is a simple protocol,
where each router broadcasts (to all its neighbors) distances to all networks it
can reach. When a router hears distance to another network, it increments it and
broadcasts it back. Broadcasts are done in regular intervals. Therefore, if some
network goes unreachable, routers keep telling each other that its distance is
the original distance plus 1 (actually, plus interface metric, which is usually
one). After some time, the distance reaches infinity (that's 15 in RIP) and all
routers know that network is unreachable. RIP tries to minimize situations where
counting to infinity is necessary, because it is slow. Due to infinity being 16,
you can't use RIP on networks where maximal distance is higher than 15
hosts.

BIRD supports RIPv1 (<a href="https://datatracker.ietf.org/doc/rfc1058" class="rfc">RFC
1058</a>), RIPv2 (<a href="https://datatracker.ietf.org/doc/rfc2453" class="rfc">RFC
2453</a>), RIPng (<a href="https://datatracker.ietf.org/doc/rfc2080" class="rfc">RFC
2080</a>), Triggered RIP for demand circuits (<a href="https://datatracker.ietf.org/doc/rfc2091" class="rfc">RFC
2091</a>), and RIP
cryptographic authentication (<a href="https://datatracker.ietf.org/doc/rfc4822" class="rfc">RFC
4822</a>).

RIP is a very simple protocol, and it has a lot of shortcomings. Slow
convergence, big network load and inability to handle larger networks makes it
pretty much obsolete. It is still usable on very small networks.

### Configuration

RIP configuration consists mainly of common protocol options and interface
definitions, most RIP options are interface specific. RIPng (RIP for IPv6)
protocol instance can be configured by using `rip ng` instead of just
`rip` as a protocol type.

RIP needs one IPv4 channel. RIPng needs one IPv6 channel. If no channel is
configured, appropriate channel is defined with default parameters.


    protocol rip [ng] [<name>] {
        infinity <number>;
        ecmp <switch> [limit <number>];
        interface <interface pattern> {
            metric <number>;
            mode multicast|broadcast;
            passive <switch>;
            address <ip>;
            port <number>;
            version 1|2;
            split horizon <switch>;
            poison reverse <switch>;
            demand circuit <switch>;
            check zero <switch>;
            update time <number>;
            timeout time <number>;
            garbage time <number>;
            ecmp weight <number>;
            ttl security <switch>; | tx only;
            tx class|dscp <number>;
            tx priority <number>;
            rx buffer <number>;
            tx length <number>;
            check link <switch>;
            authentication none|plaintext|cryptographic;
            password "<text>";
            password "<text>" {
                id <number>;
                generate from "<date>";
                generate to "<date>";
                accept from "<date>";
                accept to "<date>";
                from "<date>";
                to "<date>";
                algorithm ( keyed md5 | keyed sha1 | hmac sha1 |
                    hmac sha256 | hmac sha384 | hmac sha512 );
            };
        };
    }

<span id="rip-infinity" class="code">infinity *number*</span>  
Selects the distance of infinity. Bigger values will make
    protocol convergence even slower. The default value is 16.

<span id="rip-ecmp" class="code">ecmp *switch* \[limit *number*\]</span>  
This option specifies whether RIP is allowed to generate ECMP
    (equal-cost multipath) routes. Such routes are used when there are
    several directions to the destination, each with the same (computed)
    cost. This option also allows to specify a limit on maximum number of
    nexthops in one route. By default, ECMP is enabled if supported by
    Kernel. Default value of the limit is 16.

<span id="rip-iface" class="code">interface *pattern* \[, *...*\] { *options* }</span>  
Interface definitions specify a set of interfaces on which the
    protocol is activated and contain interface specific options.
    See [interface](#proto-iface) common options for
    detailed description.

Interface specific options:

<span id="rip-iface-metric" class="code">metric *number*</span>  
This option specifies the metric of the interface. When a route is
    received from the interface, its metric is increased by this value
    before further processing. Valid values are 1-255, but values higher
    than infinity has no further meaning. Default: 1.

<span id="rip-iface-mode" class="code">mode multicast\|broadcast</span>  
This option selects the mode for RIP to use on the interface. The
    default is multicast mode for RIPv2 and broadcast mode for RIPv1.
    RIPng always uses the multicast mode.

<span id="rip-iface-passive" class="code">passive *switch*</span>  
Passive interfaces receive routing updates but do not transmit any
    messages. Default: no.

<span id="rip-iface-address" class="code">address *ip*</span>  
This option specifies a destination address used for multicast or
    broadcast messages, the default is the official RIP (224.0.0.9) or RIPng
    (ff02::9) multicast address, or an appropriate broadcast address in the
    broadcast mode.

<span id="rip-iface-port" class="code">port *number*</span>  
This option selects an UDP port to operate on, the default is the
    official RIP (520) or RIPng (521) port.

<span id="rip-iface-version" class="code">version 1\|2</span>  
This option selects the version of RIP used on the interface. For RIPv1,
    automatic subnet aggregation is not implemented, only classful network
    routes and host routes are propagated. Note that BIRD allows RIPv1 to be
    configured with features that are defined for RIPv2 only, like
    authentication or using multicast sockets. The default is RIPv2 for IPv4
    RIP, the option is not supported for RIPng, as no further versions are
    defined.

<span id="rip-iface-version-only" class="code">version only *switch*</span>  
Regardless of RIP version configured for the interface, BIRD accepts
    incoming packets of any RIP version. This option restrict accepted
    packets to the configured version. Default: no.

<span id="rip-iface-split-horizon" class="code">split horizon *switch*</span>  
Split horizon is a scheme for preventing routing loops. When split
    horizon is active, routes are not regularly propagated back to the
    interface from which they were received. They are either not propagated
    back at all (plain split horizon) or propagated back with an infinity
    metric (split horizon with poisoned reverse). Therefore, other routers
    on the interface will not consider the router as a part of an
    independent path to the destination of the route. Default: yes.

<span id="rip-iface-poison-reverse" class="code">poison reverse *switch*</span>  
When split horizon is active, this option specifies whether the poisoned
    reverse variant (propagating routes back with an infinity metric) is
    used. The poisoned reverse has some advantages in faster convergence,
    but uses more network traffic. Default: yes.

<span id="rip-iface-demand-circuit" class="code">demand circuit *switch*</span>  
Regular RIP sends periodic full updates on an interface. There is the
    Triggered RIP extension for demand circuits (<a href="https://datatracker.ietf.org/doc/rfc2091" class="rfc">RFC
2091</a>), which
    removes periodic updates and introduces update acknowledgments. When
    enabled, there is no RIP communication in steady-state network. Note
    that in order to work, it must be enabled on both sides. As there are
    no hello packets, it depends on hardware link state to detect neighbor
    failures. Also, it is designed for PtP links and it does not work
    properly with multiple RIP neighbors on an interface. Default: no.

<span id="rip-iface-check-zero" class="code">check zero *switch*</span>  
Received RIPv1 packets with non-zero values in reserved fields should
    be discarded. This option specifies whether the check is performed or
    such packets are just processed as usual. Default: yes.

<span id="rip-iface-update-time" class="code">update time *number*</span>  
Specifies the number of seconds between periodic updates. A lower number
    will mean faster convergence but bigger network load. Default: 30.

<span id="rip-iface-timeout-time" class="code">timeout time *number*</span>  
Specifies the time interval (in seconds) between the last received route
    announcement and the route expiration. After that, the network is
    considered unreachable, but still is propagated with infinity distance.
    Default: 180.

<span id="rip-iface-garbage-time" class="code">garbage time *number*</span>  
Specifies the time interval (in seconds) between the route expiration
    and the removal of the unreachable network entry. The garbage interval,
    when a route with infinity metric is propagated, is used for both
    internal (after expiration) and external (after withdrawal) routes.
    Default: 120.

<span id="rip-iface-ecmp-weight" class="code">ecmp weight *number*</span>  
When ECMP (multipath) routes are allowed, this value specifies a
    relative weight used for nexthops going through the iface. Valid
    values are 1-256. Default value is 1.

<span id="rip-iface-auth" class="code">authentication none\|plaintext\|cryptographic</span>  
Selects authentication method to be used. `none` means that packets
    are not authenticated at all, `plaintext` means that a plaintext
    password is embedded into each packet, and `cryptographic` means that
    packets are authenticated using some cryptographic hash function
    selected by option `algorithm` for each key. The default
    cryptographic algorithm for RIP keys is Keyed-MD5. If you set
    authentication to not-none, it is a good idea to add `password`
    section. Default: none.

<span id="rip-iface-pass" class="code">password "*text*"</span>  
Specifies a password used for authentication. See [password](#proto-pass) common option for detailed description.

<span id="rip-iface-ttl-security" class="code">ttl security \[*switch* \| tx only\]</span>  
TTL security is a feature that protects routing protocols from remote
    spoofed packets by using TTL 255 instead of TTL 1 for protocol packets
    destined to neighbors. Because TTL is decremented when packets are
    forwarded, it is non-trivial to spoof packets with TTL 255 from remote
    locations.

If this option is enabled, the router will send RIP packets with TTL 255
    and drop received packets with TTL less than 255. If this option si set
    to `tx only`, TTL 255 is used for sent packets, but is not checked
    for received packets. Such setting does not offer protection, but offers
    compatibility with neighbors regardless of whether they use ttl
    security.

For RIPng, TTL security is a standard behavior (required by <a href="https://datatracker.ietf.org/doc/rfc2080" class="rfc">RFC
2080</a>) and therefore default value is yes. For IPv4 RIP, default
    value is no.

<span id="rip-iface-tx-class" class="code">tx class\|dscp\|priority *number*</span>  
These options specify the ToS/DiffServ/Traffic class/Priority of the
    outgoing RIP packets. See [tx class](#proto-tx-class) common
    option for detailed description.

<span id="rip-iface-rx-buffer" class="code">rx buffer *number*</span>  
This option specifies the size of buffers used for packet processing.
    The buffer size should be bigger than maximal size of received packets.
    The default value is 532 for IPv4 RIP and interface MTU value for RIPng.

<span id="rip-iface-tx-length" class="code">tx length *number*</span>  
This option specifies the maximum length of generated RIP packets. To
    avoid IP fragmentation, it should not exceed the interface MTU value.
    The default value is 532 for IPv4 RIP and interface MTU value for RIPng.

<span id="rip-iface-check-link" class="code">check link *switch*</span>  
If set, the hardware link state (as reported by OS) is taken into
    consideration. When the link disappears (e.g. an ethernet cable is
    unplugged), neighbors are immediately considered unreachable and all
    routes received from them are withdrawn. It is possible that some
    hardware drivers or platforms do not implement this feature.
    Default: yes.

### Attributes

RIP defines two route attributes:

<span id="rta-rip-metric" class="code">int rip_metric</span>  
RIP metric of the route (ranging from 0 to `infinity`). When routes
    from different RIP instances are available and all of them have the same
    preference, BIRD prefers the route with lowest `rip_metric`. When a
    non-RIP route is exported to RIP, the default metric is 1.

<span id="rta-rip-tag" class="code">int rip_tag</span>  
RIP route tag: a 16-bit number which can be used to carry additional
    information with the route (for example, an originating AS number in
    case of external routes). When a non-RIP route is exported to RIP, the
    default tag is 0.

### Example


    protocol rip {
        ipv4 {
            import all;
            export all;
        };
        interface "eth*" {
            metric 2;
            port 1520;
            mode multicast;
            update time 12;
            timeout time 60;
            authentication cryptographic;
            password "secret" { algorithm hmac sha256; };
        };
    }
