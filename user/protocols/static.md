## Static

The Static protocol doesn't communicate with other routers in the network,
but instead it allows you to define routes manually. This is often used for
specifying how to forward packets to parts of the network which don't use
dynamic routing at all and also for defining sink routes (i.e., those telling to
return packets as undeliverable if they are in your IP block, you don't have any
specific destination for them and you don't want to send them out through the
default route to prevent routing loops).

There are three classes of definitions in Static protocol configuration --
global options, static route definitions, and per-route options. Usually, the
definition of the protocol contains mainly a list of static routes. Static
routes have no specific attributes, but [igp_metric](#rta-igp-metric)
attribute is used to compare static routes with the same preference.

The list of static routes may contain multiple routes for the same network
(usually, but not necessary, distinquished by `preference` or `igp_metric`),
but only routes of the same network type are allowed, as the static protocol
has just one channel. E.g., to have both IPv4 and IPv6 static routes, define two
static protocols, each with appropriate routes and channel.

The Static protocol can be configured as MPLS-aware (by defining both the
primary channel and MPLS channel). In that case the Static protocol assigns
labels to IP routes and automatically announces corresponding MPLS route for
each labeled route.

Global options:

<span id="static-check-link" class="code">check link *switch*</span>  
If set, hardware link states of network interfaces are taken into
    consideration.  When link disappears (e.g. ethernet cable is unplugged),
    static routes directing to that interface are removed. It is possible
    that some hardware drivers or platforms do not implement this feature.
    Default: off.

<span id="static-igp-table" class="code">igp table *name*</span>  
Specifies a table that is used for route table lookups of recursive
    routes. Default: the same table as the protocol is connected to.

Route definitions (each may also contain a block of per-route options):

### Regular routes; MPLS switching rules

There exist several types of routes; keep in mind that *prefix* syntax is
[dependent on network type](#type-prefix).

<span id="static-route-regular" class="code">route *prefix* \[mpls *number*\] via *ip*\|*"interface"* \[*per-nexthop options*\] \[via ...\]</span>  
Regular routes may bear one or more [next
hops](#route-next-hop). Every next hop is preceded by `via` and configured as shown.

When the Static protocol is MPLS-aware, the optional `mpls` statement
    after *prefix* specifies a static label for the labeled route, instead
    of using dynamically allocated label.

<span id="static-route-recursive" class="code">route *prefix* \[mpls *number*\] recursive *ip* \[mpls *number*\[/*number*\[/*number*\[...\]\]\]\]</span>  
Recursive nexthop resolves the given IP in the configured IGP table and
    uses that route's next hop. The MPLS stacks are concatenated; on top is
    the IGP's nexthop stack and on bottom is this route's stack.

<span id="static-route-special" class="code">route *prefix* \[mpls *number*\] blackhole\|unreachable\|prohibit</span>  
Special routes specifying to silently drop the packet, return it as
    unreachable or return it as administratively prohibited. First two
    targets are also known as `drop` and `reject`.

When the particular destination is not available (the interface is down or
the next hop of the route is not a neighbor at the moment), Static just
uninstalls the route from the table it is connected to and adds it again as soon
as the destination becomes adjacent again.

#### Per-nexthop options

There are several options that in a case of multipath route are per-nexthop
(i.e., they can be used multiple times for a route, one time for each nexthop).
Syntactically, they are not separate options but just parts of `route`
statement after each `via` statement, not separated by semicolons. E.g.,
statement `route 10.0.0.0/8 via 192.0.2.1 bfd weight 1 via 192.0.2.2 weight
2;` describes a route with two nexthops, the first nexthop has two per-nexthop
options (`bfd` and `weight 1`), the second nexthop has just `weight 2`.

<span id="static-route-bfd" class="code">bfd *switch*</span>  
The Static protocol could use BFD protocol for next hop liveness
    detection. If enabled, a BFD session to the route next hop is created
    and the static route is BFD-controlled -- the static route is announced
    only if the next hop liveness is confirmed by BFD. If the BFD session
    fails, the static route (or just the affected nexthop from multiple
    ones) is removed. Note that this is a bit different compared to other
    protocols, which may use BFD as an advisory mechanism for fast failure
    detection but ignore it if a BFD session is not even established. Note
    that BFD protocol also has to be configured, see [BFD](#bfd)
    section for details. Default value is no.

<span id="static-route-dev" class="code">dev *text*</span>  
The outgoing interface associated with the nexthop. Useful for
    link-local nexthop addresses or when multiple interfaces use the same
    network prefix. By default, the outgoing interface is resolved from the
    nexthop address.

<span id="static-route-mpls" class="code">mpls *number*\[/*number*\[/*number*\[...\]\]\]</span>  
MPLS labels that should be pushed to packets forwarded by the route.
    The option could be used for both IP routes (on MPLS ingress routers)
    and MPLS switching rules (on MPLS transit routers). Default value is
    no labels.

<span id="static-route-onlink" class="code">onlink *switch*</span>  
Onlink flag means that the specified nexthop is accessible on the
    (specified) interface regardless of IP prefixes of the interface. The
    interface must be attached to nexthop IP address using link-local-scope
    format (e.g. `192.0.2.1%eth0`). Default value is no.

<span id="static-route-weight" class="code">weight *switch*</span>  
For multipath routes, this value specifies a relative weight of the
    nexthop. Allowed values are 1-256. Default value is 1.

### Route Origin Authorization

The ROA config is just `route `*prefix*` max `*int*` as `*int* with no nexthop.

### Autonomous System Provider Authorization

The ASPA config is `route aspa `*int*` providers `*int*` [, `*int*` ...]` with no nexthop.
  The first ASN is client and the following are a list of providers.
  For a transit, you can also write `route aspa `*int*` transit` to get
  the no-provider ASPA.

### Flowspec Network Type

The flow specification are rules for routers and firewalls for filtering
purpose. It is described by <a href="https://datatracker.ietf.org/doc/rfc8955" class="rfc">RFC
8955</a> and <a href="https://datatracker.ietf.org/doc/rfc8956" class="rfc">RFC
8956</a>. There are 3 types of arguments:
*inet4* or *inet6* prefixes, numeric matching expressions and bitmask
matching expressions.

Numeric matching is a matching sequence of numbers and ranges separeted by a
commas (`,`) (e.g. `10,20,30`). Ranges can be written using double dots
`..` notation (e.g. `80..90,120..124`). An alternative notation are
sequence of one or more pairs of relational operators and values separated by
logical operators `&&` or `||`. Allowed relational operators are `=`,
`!=`, `<`, `<=`, `>`, `>=`, `true` and `false`.

Bitmask matching is written using *value*`/`*mask* or
`!`*value*`/`*mask* pairs. It means that `(`*data* `&`
*mask*`)` is or is not equal to *value*. It is also possible to use
multiple value/mask pairs connected by logical operators `&&` or `||`.
Note that for negated matches, value must be either zero or equal to bitmask
(e.g. `!0x0/0xf` or `!0xf/0xf`, but not `!0x3/0xf`).

#### IPv4 Flowspec

<span id="flow-dst" class="code">dst *inet4*</span>  
Set a matching destination prefix (e.g. `dst 192.168.0.0/16`).
    Only this option is mandatory in IPv4 Flowspec.

<span id="flow-src" class="code">src *inet4*</span>  
Set a matching source prefix (e.g. `src 10.0.0.0/8`).

<span id="flow-proto" class="code">proto *numbers-match*</span>  
Set a matching IP protocol numbers (e.g.  `proto 6`).

<span id="flow-port" class="code">port *numbers-match*</span>  
Set a matching source or destination TCP/UDP port numbers (e.g.
    `port 1..1023,1194,3306`).

<span id="flow-dport" class="code">dport *numbers-match*</span>  
Set a matching destination port numbers (e.g. `dport 49151`).

<span id="flow-sport" class="code">sport *numbers-match*</span>  
Set a matching source port numbers (e.g. `sport = 0`).

<span id="flow-icmp-type" class="code">icmp type *numbers-match*</span>  
Set a matching type field number of an ICMP packet (e.g. `icmp type
    3`)

<span id="flow-icmp-code" class="code">icmp code *numbers-match*</span>  
Set a matching code field number of an ICMP packet (e.g. `icmp code
    1`)

<span id="flow-tcp-flags" class="code">tcp flags *bitmask-match*</span>  
Set a matching bitmask for TCP header flags (aka control bits) (e.g.
    `tcp flags 0x03/0x0f;`). The maximum length of mask is 12 bits
    (0xfff).

<span id="flow-length" class="code">length *numbers-match*</span>  
Set a matching packet length (e.g. `length > 1500`)

<span id="flow-dscp" class="code">dscp *numbers-match*</span>  
Set a matching DiffServ Code Point number (e.g. `dscp 8..15`).

<span id="flow-fragment" class="code">fragment *fragmentation-type*</span>  
Set a matching type of packet fragmentation. Allowed fragmentation
    types are `dont_fragment`, `is_fragment`, `first_fragment`,
    `last_fragment` (e.g. `fragment is_fragment &&
    !dont_fragment`).


    protocol static {
        flow4;

        route flow4 {
            dst 10.0.0.0/8;
            port > 24 && < 30 || 40..50,60..70,80 && >= 90;
            tcp flags 0x03/0x0f;
            length > 1024;
            dscp = 63;
            fragment dont_fragment, is_fragment || !first_fragment;
        };
    }

#### Differences for IPv6 Flowspec

Flowspec IPv6 are same as Flowspec IPv4 with a few exceptions.

- Prefixes 
  *inet6*
   can be specified not only with prefix length,
      but with prefix 
  `offset`
   
  *number*
   too (e.g.
      
  `::1234:5678:9800:0000/101 offset 64`
  ). Offset means to don't
      care of 
  *number*
   first bits.
- IPv6 Flowspec hasn't mandatory any flowspec component.
- In IPv6 packets, there is a matching the last next header value
      for a matching IP protocol number (e.g. 
  `next header 6`
  ).
- It is not possible to set 
  `dont_fragment`
   as a type of
      packet fragmentation.

<span id="flow6-dst" class="code">dst *inet6* \[offset *number*\]</span>  
Set a matching destination IPv6 prefix (e.g. `dst
    ::1c77:3769:27ad:a11a/128 offset 64`).

<span id="flow6-src" class="code">src *inet6* \[offset *number*\]</span>  
Set a matching source IPv6 prefix (e.g. `src fe80::/64`).

<span id="flow6-next-header" class="code">next header *numbers-match*</span>  
Set a matching IP protocol numbers (e.g. `next header != 6`).

<span id="flow6-label" class="code">label *numbers-match*</span>  
Set numbers for matching the 20-bit Flow Label field in IPv6 packets
    (e.g. `label != 1234`).


    protocol static {
        flow6 { table myflow6; };

        route flow6 {
            dst fec0:1122:3344:5566:7788:99aa:bbcc:ddee/128;
            src 0000:0000:0000:0001:1234:5678:9800:0000/101 offset 63;
            next header = 23;
            sport > 24 && < 30 || = 40 || 50,60,70..80;
            dport = 50;
            tcp flags 0x03/0x0f && !0/0xff || 0x33/0x33;
            fragment !is_fragment || !first_fragment;
            label > 1111 && != 1234;
        };
    }

### Per-route options

<span id="static-route-filter" class="code">*filter expression*</span>  
This is a special option that allows filter expressions to be configured
    on per-route basis. Can be used multiple times. These expressions are
    evaluated when the route is originated, similarly to the import filter
    of the static protocol. This is especially useful for configuring route
    attributes, e.g., `ospf_metric1 = 100;` for a route that will be
    exported to the OSPF protocol.

### Example static configs


    protocol static {
        ipv4 { table testable; };   # Connect to a non-default routing table
        check link;         # Advertise routes only if link is up
        route 0.0.0.0/0 via 198.51.100.130; # Default route
        route 10.0.0.0/8        # Multipath route
            via 198.51.100.10 weight 2
            via 198.51.100.20 bfd   # BFD-controlled next hop
            via 192.0.2.1;
        route 203.0.113.0/24 blackhole; # Sink route
        route 10.2.0.0/24 via "arc0";   # Direct route
        route 10.2.2.0/24 via 192.0.2.1 dev "eth0" onlink; # Route with both nexthop and iface
        route 192.168.10.0/24 via 198.51.100.100 {
            ospf_metric1 = 20;  # Set extended attribute
        };
        route 192.168.11.0/24 via 198.51.100.100 {
            ospf_metric2 = 100; # Set extended attribute
            ospf_tag = 2;       # Set extended attribute
        };
        route 192.168.12.0/24 via 198.51.100.100 {
            bgp_community.add((65535, 65281));  # Set extended BGP attribute
            bgp_large_community.add((64512, 1, 1)); # Set extended BGP attribute
        };
    }

    protocol static {
        ipv6;                       # Channel is mandatory
        route 2001:db8:10::/48 via 2001:db8:1::1;   # Route with global nexthop
        route 2001:db8:20::/48 via fe80::10%eth0;   # Route with link-local nexthop
        route 2001:db8:30::/48 via fe80::20%'eth1.60';  # Iface with non-alphanumeric characters
        route 2001:db8:40::/48 via fe80::30 dev "eth1"; # Another link-local nexthop
        route 2001:db8:50::/48 via "eth2";      # Direct route to eth2
        route 2001:db8::/32 unreachable;        # Unreachable route
        route ::/0 via 2001:db8:1::1 bfd;       # BFD-controlled default route
    }
