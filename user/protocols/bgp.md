## BGP

The Border Gateway Protocol is the routing protocol used for backbone level
routing in the today's Internet. Contrary to other protocols, its convergence
does not rely on all routers following the same rules for route selection,
making it possible to implement any routing policy at any router in the network,
the only restriction being that if a router advertises a route, it must accept
and forward packets according to it.

BGP works in terms of autonomous systems (often abbreviated as AS). Each AS
is a part of the network with common management and common routing policy. It is
identified by a unique 16-bit number (ASN). Routers within each AS usually
exchange AS-internal routing information with each other using an interior
gateway protocol (IGP, such as OSPF or RIP). Boundary routers at the border of
the AS communicate global (inter-AS) network reachability information with their
neighbors in the neighboring AS'es via exterior BGP (eBGP) and redistribute
received information to other routers in the AS via interior BGP (iBGP).

Each BGP router sends to its neighbors updates of the parts of its routing
table it wishes to export along with complete path information (a list of AS'es
the packet will travel through if it uses the particular route) in order to
avoid routing loops.

### Supported standards

- <a href="https://datatracker.ietf.org/doc/rfc4271" class="rfc">RFC
  4271</a>
   – Border Gateway Protocol 4 (BGP)
- <a href="https://datatracker.ietf.org/doc/rfc1997" class="rfc">RFC
  1997</a>
   – BGP Communities Attribute
- <a href="https://datatracker.ietf.org/doc/rfc2385" class="rfc">RFC
  2385</a>
   – Protection of BGP Sessions via TCP MD5 Signature
- <a href="https://datatracker.ietf.org/doc/rfc2545" class="rfc">RFC
  2545</a>
   – Use of BGP Multiprotocol Extensions for IPv6
- <a href="https://datatracker.ietf.org/doc/rfc2918" class="rfc">RFC
  2918</a>
   – Route Refresh Capability
- <a href="https://datatracker.ietf.org/doc/rfc3107" class="rfc">RFC
  3107</a>
   – Carrying Label Information in BGP
- <a href="https://datatracker.ietf.org/doc/rfc4360" class="rfc">RFC
  4360</a>
   – BGP Extended Communities Attribute
- <a href="https://datatracker.ietf.org/doc/rfc4364" class="rfc">RFC
  4364</a>
   – BGP/MPLS IPv4 Virtual Private Networks
- <a href="https://datatracker.ietf.org/doc/rfc4456" class="rfc">RFC
  4456</a>
   – BGP Route Reflection
- <a href="https://datatracker.ietf.org/doc/rfc4486" class="rfc">RFC
  4486</a>
   – Subcodes for BGP Cease Notification Message
- <a href="https://datatracker.ietf.org/doc/rfc4659" class="rfc">RFC
  4659</a>
   – BGP/MPLS IPv6 Virtual Private Networks
- <a href="https://datatracker.ietf.org/doc/rfc4724" class="rfc">RFC
  4724</a>
   – Graceful Restart Mechanism for BGP
- <a href="https://datatracker.ietf.org/doc/rfc4760" class="rfc">RFC
  4760</a>
   – Multiprotocol extensions for BGP
- <a href="https://datatracker.ietf.org/doc/rfc4798" class="rfc">RFC
  4798</a>
   – Connecting IPv6 Islands over IPv4 MPLS
- <a href="https://datatracker.ietf.org/doc/rfc5065" class="rfc">RFC
  5065</a>
   – AS confederations for BGP
- <a href="https://datatracker.ietf.org/doc/rfc5082" class="rfc">RFC
  5082</a>
   – Generalized TTL Security Mechanism
- <a href="https://datatracker.ietf.org/doc/rfc5492" class="rfc">RFC
  5492</a>
   – Capabilities Advertisement with BGP
- <a href="https://datatracker.ietf.org/doc/rfc8955" class="rfc">RFC
  8955</a>
   – Dissemination of Flow Specification Rules for IPv4
- <a href="https://datatracker.ietf.org/doc/rfc8956" class="rfc">RFC
  8956</a>
   – Dissemination of Flow Specification Rules for IPv6
- <a href="https://datatracker.ietf.org/doc/rfc5668" class="rfc">RFC
  5668</a>
   – 4-Octet AS Specific BGP Extended Community
- <a href="https://datatracker.ietf.org/doc/rfc5925" class="rfc">RFC
  5925</a>
   – TCP Authentication Option
- <a href="https://datatracker.ietf.org/doc/rfc6286" class="rfc">RFC
  6286</a>
   – AS-Wide Unique BGP Identifier
- <a href="https://datatracker.ietf.org/doc/rfc6608" class="rfc">RFC
  6608</a>
   – Subcodes for BGP Finite State Machine Error
- <a href="https://datatracker.ietf.org/doc/rfc6793" class="rfc">RFC
  6793</a>
   – BGP Support for 4-Octet AS Numbers
- <a href="https://datatracker.ietf.org/doc/rfc7311" class="rfc">RFC
  7311</a>
   – Accumulated IGP Metric Attribute for BGP
- <a href="https://datatracker.ietf.org/doc/rfc7313" class="rfc">RFC
  7313</a>
   – Enhanced Route Refresh Capability for BGP
- <a href="https://datatracker.ietf.org/doc/rfc7606" class="rfc">RFC
  7606</a>
   – Revised Error Handling for BGP UPDATE Messages
- <a href="https://datatracker.ietf.org/doc/rfc7911" class="rfc">RFC
  7911</a>
   – Advertisement of Multiple Paths in BGP
- <a href="https://datatracker.ietf.org/doc/rfc7947" class="rfc">RFC
  7947</a>
   – Internet Exchange BGP Route Server
- <a href="https://datatracker.ietf.org/doc/rfc8092" class="rfc">RFC
  8092</a>
   – BGP Large Communities Attribute
- <a href="https://datatracker.ietf.org/doc/rfc8212" class="rfc">RFC
  8212</a>
   – Default EBGP Route Propagation Behavior without Policies
- <a href="https://datatracker.ietf.org/doc/rfc8654" class="rfc">RFC
  8654</a>
   – Extended Message Support for BGP
- <a href="https://datatracker.ietf.org/doc/rfc8950" class="rfc">RFC
  8950</a>
   – Advertising IPv4 NLRI with an IPv6 Next Hop
- <a href="https://datatracker.ietf.org/doc/rfc9003" class="rfc">RFC
  9003</a>
   – Extended BGP Administrative Shutdown Communication
- <a href="https://datatracker.ietf.org/doc/rfc9072" class="rfc">RFC
  9072</a>
   – Extended Optional Parameters Length for BGP OPEN Message
- <a href="https://datatracker.ietf.org/doc/rfc9117" class="rfc">RFC
  9117</a>
   – Revised Validation Procedure for BGP Flow Specifications
- <a href="https://datatracker.ietf.org/doc/rfc9234" class="rfc">RFC
  9234</a>
   – Route Leak Prevention and Detection Using Roles
- <a href="https://datatracker.ietf.org/doc/rfc9494" class="rfc">RFC
  9494</a>
   – Long-Lived Graceful Restart for BGP
- <a href="https://datatracker.ietf.org/doc/rfc9687" class="rfc">RFC
  9687</a>
   – Send Hold Timer

### Route selection rules

BGP doesn't have any simple metric, so the rules for selection of an optimal
route among multiple BGP routes with the same preference are a bit more complex
and they are implemented according to the following algorithm. It starts the
first rule, if there are more "best" routes, then it uses the second rule to
choose among them and so on.

- Prefer route with the highest Local Preference attribute.
- Prefer route with the shortest AS path.
- Prefer IGP origin over EGP and EGP origin over incomplete.
- Prefer the lowest value of the Multiple Exit Discriminator.
- Prefer routes received via eBGP over ones received via iBGP.
- Prefer routes with lower internal distance to a boundary router.
- Prefer the route with the lowest value of router ID of the
      advertising router.

### IGP routing table

BGP is mainly concerned with global network reachability and with routes to
other autonomous systems. When such routes are redistributed to routers in the
AS via BGP, they contain IP addresses of a boundary routers (in route attribute
NEXT_HOP). BGP depends on existing IGP routing table with AS-internal routes to
determine immediate next hops for routes and to know their internal distances to
boundary routers for the purpose of BGP route selection. In BIRD, there is
usually one routing table used for both IGP routes and BGP routes.

### Protocol configuration

Each instance of the BGP corresponds to one neighboring router. This allows
to set routing policy and all the other parameters differently for each neighbor
using the following configuration parameters:


    protocol bgp [<name>] {
        ipv4|ipv6|... {
            <channel-options>
            mandatory <switch>;
            next hop keep <switch>|ibgp|ebgp;
            next hop self <switch>|ibgp|ebgp;
            next hop address <ip>;
            next hop prefer global;
            link local next hop format native|single|double;
            gateway direct|recursive;
            igp table <name>;
            import table <switch>;
            export table <switch>;
            secondary <switch>;
            validate <switch>;
            base table <name>;
            extended next hop <switch>;
            require extended next hop <switch>;
            add paths <switch>|rx|tx;
            require add paths <switch>;
            aigp <switch>|originate;
            cost <number>;
            graceful restart <switch>;
            long lived graceful restart <switch>;
            long lived stale time <number>;
            min long lived stale time <number>;
            max long lived stale time <number>;
        };
        local [<ip>] [port <number>] [as <number>];
        neighbor [<ip> | range <prefix>] [onlink] [port <number>] [as <number>] [internal|external];
        interface "<text>";
        interface range <interface pattern>;
        onlink <switch>;
        direct;
        multihop [<number>];
        source address <ip>;
        dynamic name "<text>";
        dynamic name digits <number>;
        strict bind <switch>;
        free bind <switch>;
        check link <switch>;
        bfd <switch>|graceful| { <bfd-options> };
        ttl security <switch>;
        authentication none|md5|ao;
        password "<text>";
        keys {
            key {
                id <number>;
                send id <number>;
                recv id <number>;
                secret "<text>"|<bytestring>;
                algorithm ( hmac md5 | hmac sha1 | hmac sha224 | hmac sha256 |
                        hmac sha384 | hmac sha512 | cmac aes128 );
                preferred;
                deprecated;
            };
            ...
        };
        setkey <switch>;
        passive <switch>;
        confederation <number>;
        confederation member <switch>;
        rr client <switch>;
        rr cluster id <number>|<IPv4 address>;
        rs client <switch>;
        allow bgp_local_pref <switch>;
        allow bgp_med <switch>;
        allow local as [<number>];
        allow as sets <switch>;
        enforce first as <switch>;
        enable route refresh <switch>;
        require route refresh <switch>;
        enable enhanced route refresh <switch>;
        require enhanced route refresh <switch>;
        graceful restart <switch>|aware;
        graceful restart time <number>;
        min graceful restart time <number>;
        max graceful restart time <number>;
        require graceful restart <switch>;
        long lived graceful restart <switch>|aware;
        long lived stale time <number>;
        min long lived stale time <number>;
        max long lived stale time <number>;
        require long lived graceful restart <switch>;
        interpret communities <switch>;
        enable as4 <switch>;
        require as4 <switch>;
        enable extended messages <switch>;
        require extended messages <switch>;
        capabilities <switch>;
        advertise hostname <switch>;
        require hostname <switch>;
        disable after error <switch>;
        disable after cease <switch>|<set-of-flags>;
        hold time <number>;
        min hold time <number>;
        startup hold time <number>;
        keepalive time <number>;
        min keepalive time <number>;
        send hold time <number>;
        connect delay time <number>;
        connect retry time <number>;
        error wait time <number>, <number>;
        error forget time <number>;
        path metric <switch>;
        med metric <switch>;
        deterministic med <switch>;
        igp metric <switch>;
        prefer older <switch>;
        default bgp_med <number>;
        default bgp_local_pref <number>;
        local role <role-name>;
        require roles <switch>;
    }

<span id="bgp-local" class="code">local \[*ip*\] \[port *number*\] \[as *number*\]</span>  
Define which AS we are part of. (Note that contrary to other IP routers,
    BIRD is able to act as a router located in multiple AS'es simultaneously,
    but in such cases you need to tweak the BGP paths manually in the filters
    to get consistent behavior.) Optional `ip` argument specifies a source
    address, equivalent to the `source address` option (see below).
    Optional `port` argument specifies the local BGP port instead of
    standard port 179. The parameter may be used multiple times with
    different sub-options (e.g., both `local 10.0.0.1 as 65000;` and
    `local 10.0.0.1; local as 65000;` are valid). This parameter is
    mandatory.

<span id="bgp-neighbor" class="code">neighbor \[*ip* \| range *prefix*\] \[port *number*\] \[as *number*\] \[internal\|external\]</span>  
Define neighboring router this instance will be talking to and what AS
    it is located in. In case the neighbor is in the same AS as we are, we
    automatically switch to IBGP. Alternatively, it is possible to specify
    just `internal` or `external` instead of AS number, in that case
    either local AS number, or any external AS number is accepted.
    Optionally, the remote port may also be specified. Like `local`
    parameter, this parameter may also be used multiple times with different
    sub-options. This parameter is mandatory.

It is possible to specify network prefix (with `range` keyword)
    instead of explicit neighbor IP address. This enables dynamic BGP
    behavior, where the BGP instance listens on BGP port, but new BGP
    instances are spawned for incoming BGP connections (if source address
    matches the network prefix). It is possible to mix regular BGP instances
    with dynamic BGP instances and have multiple dynamic BGP instances with
        different ranges. These spawned dynamic BGP instances share the parent
        configuration, therefore by reconfiguring the parent protocol forces
        reconfiguration of the spawned protocols.

When the neighbor range is changed, all the spawned dynamic instances
        shut down. Reconfiguration clears all dynamic instances which were
        previously disabled by the `disable` CLI command. This may re-enable
    connection of some clients otherwise blocked by the disabled instance.

<span id="bgp-iface" class="code">interface "*text*"</span>  
Define interface we should use for link-local BGP IPv6 sessions.
    Interface can also be specified as a part of `neighbor address`
    (e.g., `neighbor fe80::1234%eth0 as 65000;`). The option may also be
    used for non link-local sessions when it is necessary to explicitly
    specify an interface, but only for direct (not multihop) sessions.

<span id="bgp-iface-range" class="code">interface range *interface pattern*</span>  
Set interface pattern to which the connection will be bound. This is
    mostly useful with the `neighbor range` option and either link-local
    addresses or with the `onlink` option where it's not known up front
    which interface the connection comes on but it needs to stay there.

This option requires `strict bind` to be on and creates a separate
    listening socket for every single interface matching the pattern. If the
        local address is set, it also requires this exact address to be set on
        that interface to create a listening socket.

Also see `strict bind` and `free bind`.

<span id="bgp-onlink" class="code">onlink *switch*</span>  
For a direct neighbor, the BGP session starts immediately without
    waiting for the neighbor's address to appear on any interface.
    This option requires an interface to be configured. Next hops
    of all routes from this session also have the `onlink` attribute.

This option may generally lead to weird behavior without other
    configuration in place. One may e.g. need to insert a working route
    for the given neighbor manually to allow for ACKs from the incoming
    connection to be routed back correctly. That route may also need to
    be announced via IGP, or `next hop self` in iBGP may be needed.

When trying setups with no neighbor route in containerized environments,
    we got some results with `strict bind`. As of Linux 6.12, we can't
    recommend running that setup though.

Onlink behavior may also be specified inside the `neighbor` option.
    Default: disabled.

<span id="bgp-direct" class="code">direct</span>  
Specify that the neighbor is directly connected. The IP address of the
    neighbor must be from a directly reachable IP range (i.e. associated
    with one of your router's interfaces), otherwise the BGP session
    wouldn't start but it would wait for such interface to appear. The
    alternative is the `multihop` option. Default: enabled for eBGP.

<span id="bgp-multihop" class="code">multihop \[*number*\]</span>  
Configure multihop BGP session to a neighbor that isn't directly
    connected. Accurately, this option should be used if the configured
    neighbor IP address does not match with any local network subnets. Such
    IP address have to be reachable through system routing table. The
    alternative is the `direct` option. For multihop BGP it is
    recommended to explicitly configure the source address to have it
    stable. Optional `number` argument can be used to specify the number
    of hops (used for TTL). Note that the number of networks (edges) in a
    path is counted; i.e., if two BGP speakers are separated by one router,
    the number of hops is 2. Default: enabled for iBGP.

<span id="bgp-source-address" class="code">source address *ip*</span>  
Define local address we should use as a source address for the BGP
    session. Default: the address of the local end of the interface our
    neighbor is connected to.

<span id="bgp-dynamic-name" class="code">dynamic name "*text*"</span>  
Define common prefix of names used for new BGP instances spawned when
    dynamic BGP behavior is active. Actual names also contain numeric
    index to distinguish individual instances.  Default: "dynbgp".

<span id="bgp-dynamic-name-digits" class="code">dynamic name digits *number*</span>  
Define minimum number of digits for index in names of spawned dynamic
    BGP instances. E.g., if set to 2, then the first name would be
    "dynbgp01". Default: 0.

<span id="bgp-strict-bind" class="code">strict bind *switch*</span>  
Specify whether BGP listening socket should be bound to a specific local
    address (the same as the `source address`) and associated interface
    or to all addresses. Binding to a specific address could be useful in
    cases like running multiple BIRD instances on a machine, each using its
    IP address.

Note that listening sockets bound to a specific address and
    to all addresses collide. Also listening sockets bound
    to a specific interface and to all interfaces may behave weirdly.
    Therefore, all BGP protocols (of the same address family and using
    the same local port) should have set `strict bind`, or none of them,
    and in the same way, all should have a `interface` or `interface range`
    or none of them.

Default: disabled.

<span id="bgp-free-bind" class="code">free bind *switch*</span>  
Use IP\_FREEBIND socket option for the listening socket, which allows
    binding to an IP address not (yet) assigned to an interface. Note that
    all BGP instances that share a listening socket should have the same
    value of the `free bind` option.

If `interface range` is set together with a local address
    and `free bind` as well, it creates a free-bind listening socket
    for every interface regardless of the assigned address. This is
    an experimental feature.

Default: disabled.

<span id="bgp-check-link" class="code">check link *switch*</span>  
BGP could use hardware link state into consideration.  If enabled,
    BIRD tracks the link state of the associated interface and when link
    disappears (e.g. an ethernet cable is unplugged), the BGP session is
    immediately shut down. Note that this option cannot be used with
    multihop BGP. Default: enabled for direct BGP, disabled otherwise.

<span id="bgp-bfd" class="code">bfd *switch*\|graceful\| { *options* }</span>  
BGP could use BFD protocol as an advisory mechanism for neighbor
    liveness and failure detection. If enabled, BIRD setups a BFD session
    for the BGP neighbor and tracks its liveness by it. This has an
    advantage of an order of magnitude lower detection times in case of
    failure. When a neighbor failure is detected, the BGP session is
    restarted. Optionally, it can be configured (by `graceful` argument)
    to trigger graceful restart instead of regular restart. It is also
    possible to specify section with per-peer BFD session options instead of
    just the switch argument. All BFD session-specific options are allowed
    here. Note that BFD protocol also has to be configured, see
    [BFD](#bfd) section for details. Default: disabled.

<span id="bgp-ttl-security" class="code">ttl security *switch*</span>  
Use GTSM (<a href="https://datatracker.ietf.org/doc/rfc5082" class="rfc">RFC
5082</a> - the generalized TTL security mechanism). GTSM
    protects against spoofed packets by ignoring received packets with a
    smaller than expected TTL. To work properly, GTSM have to be enabled on
    both sides of a BGP session. If both `ttl security` and
    `multihop` options are enabled, `multihop` option should specify
    proper hop value to compute expected TTL. Kernel support required:
    Linux: 2.6.34+ (IPv4), 2.6.35+ (IPv6), BSD: since long ago, IPv4 only.
    Note that full (ICMP protection, for example) <a href="https://datatracker.ietf.org/doc/rfc5082" class="rfc">RFC
5082</a> support is
    provided by Linux only. Default: disabled.

<span id="bgp-authentication" class="code">authentication none\|md5\|ao</span>  
Selects authentication method to be used. `none` means that the BGP
    session is not authenticated at all. `md5` means that the TCP MD5
    authentication of BGP sessions (<a href="https://datatracker.ietf.org/doc/rfc2385" class="rfc">RFC
2385</a>) is used, in that case
    the option [password](#bgp-password) is used to specify
    the (single) password. Finally, `ao` means to use TCP Authentication
    Option (TCP-AO, <a href="https://datatracker.ietf.org/doc/rfc5925" class="rfc">RFC
5925</a>), allowing multiple keys and different
    cryptographic algorithms. These are specified using the option
    [keys](#bgp-keys). Note that TCP-AO authentication is not
    supported on dynamic BGP sessions. Default: none.

<span id="bgp-password" class="code">password "*text*"</span>  
Use this password for MD5 authentication of BGP sessions (<a href="https://datatracker.ietf.org/doc/rfc2385" class="rfc">RFC
2385</a>). When
    used on BSD systems, see also `setkey` option below. Default: no
    authentication.

<span id="bgp-setkey" class="code">setkey *switch*</span>  
On BSD systems, keys for TCP MD5 authentication are stored in the global
    SA/SP database, which can be accessed by external utilities (e.g.
    setkey(8)). BIRD configures security associations in the SA/SP database
    automatically based on `password` options (see above), this option
    allows to disable automatic updates by BIRD when manual configuration by
    external utilities is preferred. Note that automatic SA/SP database
    updates are currently implemented only for FreeBSD. Passwords have to be
    set manually by an external utility on NetBSD and OpenBSD. Default:
    enabled (ignored on non-FreeBSD).

<span id="bgp-keys" class="code">keys { key { \[*...*\] }; \[*...*\] }</span>  
Define a set of cryptographic keys that are used for TCP-AO
    authentication of BGP sessions (<a href="https://datatracker.ietf.org/doc/rfc5925" class="rfc">RFC
5925</a>). Each key has a
    configuration block with its own sub-options (\[`send` \| `recv`\]
    `id`, `secret`, `algorithm`, `preferred`, `deprecated`).

TCP-AO key has two IDs - for outgoing and incoming direction (Send /
    Recv ID). Among keys on one protocol all Send IDs must be unique and all
    Recv IDs must be unique. They must be in range 0-255 and they can be set
    independently with key options `send id` and `recv id`, or
    together with option `id`. Note that specifying these IDs is
    mandatory.

Of course, TCP-AO key contains a shared secret key. It is specified by
    the option `secret` as a text string or as a sequence of hexadecimal
    digit pairs ([bytestring](#type-bytestring)).

Used cryptographic algorithm can be specified for each key with the
    option `algorithm`. Possible values are: `hmac md5`, `hmac sha1`,
    `hmac sha224`, `hmac sha256`, `hmac sha384`, `hmac sha512`,
    and `cmac aes128`. Default value is `hmac sha1`.

When multiple keys are available, BIRD selects one to advertise as RNext
    key (the key it prefers to be used to sign incoming traffic). Keys
    marked as `preferred` are selected before unmarked keys, while keys
    marked as `deprecated` are never selected (but still could be used
    when the other side asks for them). Therefore, there must be always at
    least one non-deprecated key.

Currently, only the selected key is used during the initial handshake of
    session establishment (and therefore must be known by the other side).
    This may change in the future.

It is possible to add, remove, or modify keys during reconfiguration
    without breaking the BGP session. The recommended way is to refrain from
    removing a key that is in active use (as reported by `Current key`
    and `RNext key` in `show protocols all`), instead marking the key
    as `deprecated` on both sides of the session. It is possible to
    remove an active key directly, BIRD would forcibly switch to another key
    (as long as there is a non-deprecated key that is not added, removed or
    modified during this reconfiguration). This is not recommended as it
    skips the proper key change mechanism and may switch to a key that is
    not available to the other side.

Modification of existing keys (except of marking them `preferred` or
    `deprecated`) is equivalent to removing and then adding them, with
    the same issues related to removing of active keys.

<span id="bgp-passive" class="code">passive *switch*</span>  
Standard BGP behavior is both initiating outgoing connections and
    accepting incoming connections. In passive mode, outgoing connections
    are not initiated. Default: off.

<span id="bgp-confederation" class="code">confederation *number*</span>  
BGP confederations (<a href="https://datatracker.ietf.org/doc/rfc5065" class="rfc">RFC
5065</a>) are collections of autonomous
    systems that act as one entity to external systems, represented by one
    confederation identifier (instead of AS numbers). This option allows to
    enable BGP confederation behavior and to specify the local confederation
    identifier. When BGP confederations are used, all BGP speakers that are
    members of the BGP confederation should have the same confederation
    identifier configured. Default: 0 (no confederation).

<span id="bgp-confederation-member" class="code">confederation member *switch*</span>  
When BGP confederations are used, this option allows to specify whether
    the BGP neighbor is a member of the same confederation as the local BGP
    speaker. The option is unnecessary (and ignored) for IBGP sessions, as
    the same AS number implies the same confederation. Default: no.

<span id="bgp-rr-client" class="code">rr client *switch*</span>  
Be a route reflector and treat the neighbor as a route reflection
    client. Default: disabled.

<span id="bgp-rr-cluster-id" class="code">rr cluster id *number* \| *IPv4 address*</span>  
Route reflectors use cluster id to avoid route reflection loops. When
    there is one route reflector in a cluster it usually uses its router id
    as a cluster id, but when there are more route reflectors in a cluster,
    these need to be configured (using this option) to use a common cluster
    id. Clients in a cluster need not know their cluster id and this option
    is not allowed for them. Default: the same as router id.

<span id="bgp-rs-client" class="code">rs client *switch*</span>  
Be a route server and treat the neighbor as a route server client.
    A route server is used as a replacement for full mesh EBGP routing in
    Internet exchange points in a similar way to route reflectors used in
    IBGP routing. BIRD does not implement obsoleted <a href="https://datatracker.ietf.org/doc/rfc1863" class="rfc">RFC
1863</a>, but
    uses ad-hoc implementation, which behaves like plain EBGP but reduces
    modifications to advertised route attributes to be transparent (for
    example does not prepend its AS number to AS PATH attribute and
    keeps MED attribute). Default: disabled.

<span id="bgp-allow-local-pref" class="code">allow bgp_local_pref *switch*</span>  
Standard BGP implementations do not send the Local Preference attribute
    to EBGP neighbors and ignore this attribute if received from EBGP
    neighbors, as per <a href="https://datatracker.ietf.org/doc/rfc4271" class="rfc">RFC
4271</a>.  When this option is enabled on an
    EBGP session, this attribute will be sent to and accepted from the peer,
    which is useful for example if you have a setup like in <a href="https://datatracker.ietf.org/doc/rfc7938" class="rfc">RFC
7938</a>.
    The option does not affect IBGP sessions. Default: off.

<span id="bgp-allow-med" class="code">allow bgp_med *switch*</span>  
Standard BGP implementations do not propagate the MULTI\_EXIT\_DESC
    attribute unless it is configured locally. When this option is enabled
    on an EBGP session, this attribute will be sent to the peer regardless,
    which is useful for example if you have a setup like in <a href="https://datatracker.ietf.org/doc/rfc7938" class="rfc">RFC
7938</a>.
    The option does not affect IBGP sessions. Default: off.

<span id="bgp-allow-local-as" class="code">allow local as \[*number*\]</span>  
BGP prevents routing loops by rejecting received routes with the local
    AS number in the AS path. This option allows to loose or disable the
    check. Optional `number` argument can be used to specify the maximum
    number of local ASNs in the AS path that is allowed for received
    routes. When the option is used without the argument, the check is
    completely disabled and you should ensure loop-free behavior by some
    other means. Default: 0 (no local AS number allowed).

<span id="bgp-allow-as-sets" class="code">allow as sets *switch*</span>  
Historically, AS path attribute received with BGP routes may have
    contained not only sequences of AS numbers, but also sets of AS numbers.
    These rarely used artifacts were results of inter-AS route aggregation.
    AS sets are deprecated (<a href="https://datatracker.ietf.org/doc/rfc6472" class="rfc">RFC
6472</a>, <a href="https://datatracker.ietf.org/doc/rfc9774" class="rfc">RFC
9774</a>) and AS paths
    containing them (or AS confed sets) are considered malformed.
    Corresponding BGP updates are therefore treated as withdraws.
        When this option is enabled, then received AS paths containing these
        deprecated AS sets or AS confed sets are accepted.
    Default: off (since 2.18 and 3.2).

<span id="bgp-enforce-first-as" class="code">enforce first as *switch*</span>  
Routes received from an EBGP neighbor are generally expected to have the
    first (leftmost) AS number in their AS path equal to the neighbor AS
    number. This is not enforced by default as there are legitimate cases
    where it is not true, e.g. connections to route servers. When this
    option is enabled, routes with non-matching first AS number are rejected
    and corresponding updates are treated as withdraws. The option is valid
    on EBGP sessions only. Default: off.

<span id="bgp-enable-route-refresh" class="code">enable route refresh *switch*</span>  
After the initial route exchange, BGP protocol uses incremental updates
    to keep BGP speakers synchronized. Sometimes (e.g., if BGP speaker
    changes its import filter, or if there is suspicion of inconsistency) it
    is necessary to do a new complete route exchange. BGP protocol extension
    Route Refresh (<a href="https://datatracker.ietf.org/doc/rfc2918" class="rfc">RFC
2918</a>) allows BGP speaker to request
    re-advertisement of all routes from its neighbor. This option
    specifies whether BIRD advertises this capability and supports
    related procedures. Note that even when disabled, BIRD can send route
    refresh requests. Disabling Route Refresh also disables Enhanced Route Refresh.
    Default: on.

<span id="bgp-require-route-refresh" class="code">require route refresh *switch*</span>  
If enabled, the BGP Route Refresh capability (<a href="https://datatracker.ietf.org/doc/rfc2918" class="rfc">RFC
2918</a>) must be
    announced by the BGP neighbor, otherwise the BGP session will not be
    established. Default: off.

<span id="bgp-enable-enhanced-route-refresh" class="code">enable enhanced route refresh *switch*</span>  
BGP protocol extension Enhanced Route Refresh (<a href="https://datatracker.ietf.org/doc/rfc7313" class="rfc">RFC
7313</a>)
    specifies explicit begin and end for Route Refresh (see previous
    option), therefore the receiver can remove stale routes that were not
    advertised during the exchange. This option specifies whether BIRD
    advertises this capability and supports related procedures. Default: on.

<span id="bgp-require-enhanced-route-refresh" class="code">require enhanced route refresh *switch*</span>  
If enabled, the BGP Enhanced Route Refresh capability (<a href="https://datatracker.ietf.org/doc/rfc7313" class="rfc">RFC
7313</a>)
    must be announced by the BGP neighbor, otherwise the BGP session
    will not be established. Default: off.

<span id="bgp-graceful-restart" class="code">graceful restart *switch*\|aware</span>  
When a BGP speaker restarts or crashes, neighbors will discard all
    received paths from the speaker, which disrupts packet forwarding even
    when the forwarding plane of the speaker remains intact. <a href="https://datatracker.ietf.org/doc/rfc4724" class="rfc">RFC
4724</a>
    specifies an optional graceful restart mechanism to alleviate this
    issue. This option controls the mechanism. It has three states:
    Disabled, when no support is provided. Aware, when the graceful restart
    support is announced and the support for restarting neighbors is
    provided, but no local graceful restart is allowed (i.e. receiving-only
    role). Enabled, when the full graceful restart support is provided
    (i.e. both restarting and receiving role). Restarting role could be also
    configured per-channel. Note that proper support for local graceful
    restart requires also configuration of other protocols. Default: aware.

<span id="bgp-graceful-restart-time" class="code">graceful restart time *number*</span>  
The restart time is announced in the BGP Graceful Restart capability
    and specifies how long the neighbor would wait for the BGP session to
    re-establish after a restart before deleting stale routes. Default:
    120 seconds.

<span id="bgp-min-graceful-restart-time" class="code">min graceful restart time *number*</span>  
The lower bound for the graceful restart time to override the value
    received in the BGP Graceful Restart capability announced by the
    neighbor. Default: no lower bound.

<span id="bgp-max-graceful-restart-time" class="code">max graceful restart time *number*</span>  
The upper bound for the graceful restart time to override the value
    received in the BGP Graceful Restart capability announced by the
    neighbor. Default: no upper bound.

<span id="bgp-require-graceful-restart" class="code">require graceful restart *switch*</span>  
If enabled, the BGP Graceful Restart capability (<a href="https://datatracker.ietf.org/doc/rfc4724" class="rfc">RFC
4724</a>)
    must be announced by the BGP neighbor, otherwise the BGP session
    will not be established. Default: off.

<span id="bgp-long-lived-graceful-restart" class="code">long lived graceful restart *switch*\|aware</span>  
The long-lived graceful restart is an extension of the traditional
    [BGP graceful restart](#bgp-graceful-restart), where stale
    routes are kept even after the [restart
time](#bgp-graceful-restart-time) expires for additional long-lived stale time, but
    they are marked with the LLGR\_STALE community, depreferenced, and
    withdrawn from routers not supporting LLGR. Like traditional BGP
    graceful restart, it has three states: disabled, aware (receiving-only),
    and enabled. Note that long-lived graceful restart requires at least
    aware level of traditional BGP graceful restart. Default: aware, unless
    graceful restart is disabled.

<span id="bgp-long-lived-stale-time" class="code">long lived stale time *number*</span>  
The long-lived stale time is announced in the BGP Long-lived Graceful
    Restart capability and specifies how long the neighbor would keep stale
    routes depreferenced during long-lived graceful restart until either the
    session is re-stablished and synchronized or the stale time expires and
    routes are removed. Default: 3600 seconds.

<span id="bgp-min-long-lived-stale-time" class="code">min long lived stale time *number*</span>  
The lower bound for the long-lived stale time to override the value
    received in the BGP Long-lived Graceful Restart capability announced
    by the neighbor. Default: no lower bound.

<span id="bgp-max-long-lived-stale-time" class="code">max long lived stale time *number*</span>  
The upper bound for the long-lived stale time to override the value
    received in the BGP Long-lived Graceful Restart capability announced
    by the neighbor. Default: no upper bound.

<span id="bgp-require-long-lived-graceful-restart" class="code">require long lived graceful restart *switch*</span>  
If enabled, the BGP Long-lived Graceful Restart capability (<a href="https://datatracker.ietf.org/doc/rfc9494" class="rfc">RFC
9494</a>)
    must be announced by the BGP neighbor, otherwise the BGP session
    will not be established. Default: off.

<span id="bgp-interpret-communities" class="code">interpret communities *switch*</span>  
<a href="https://datatracker.ietf.org/doc/rfc1997" class="rfc">RFC
1997</a> demands that BGP speaker should process well-known
    communities like no-export (65535, 65281) or no-advertise (65535,
    65282). For example, received route carrying a no-advertise community
    should not be advertised to any of its neighbors. If this option is
    enabled (which is by default), BIRD has such behavior automatically (it
    is evaluated when a route is exported to the BGP protocol just before
    the export filter).  Otherwise, this integrated processing of
    well-known communities is disabled. In that case, similar behavior can
    be implemented in the export filter.  Default: on.

<span id="bgp-enable-as4" class="code">enable as4 *switch*</span>  
BGP protocol was designed to use 2B AS numbers and was extended later to
    allow 4B AS number. BIRD supports 4B AS extension, but by disabling this
    option it can be persuaded not to advertise it and to maintain old-style
    sessions with its neighbors. This might be useful for circumventing bugs
    in neighbor's implementation of 4B AS extension. Even when disabled
    (off), BIRD behaves internally as AS4-aware BGP router. Default: on.

<span id="bgp-require-as4" class="code">require as4 *switch*</span>  
If enabled, the BGP 4B AS number capability (<a href="https://datatracker.ietf.org/doc/rfc6793" class="rfc">RFC
6793</a>) must be
    announced by the BGP neighbor, otherwise the BGP session will not be
    established. Default: off.

<span id="bgp-enable-extended-messages" class="code">enable extended messages *switch*</span>  
The BGP protocol uses maximum message length of 4096 bytes. This option
    provides an extension (<a href="https://datatracker.ietf.org/doc/rfc8654" class="rfc">RFC
8654</a>) to allow extended messages with
    length up to 65535 bytes. Default: off.

<span id="bgp-require-extended-messages" class="code">require extended messages *switch*</span>  
If enabled, the BGP Extended Message capability (<a href="https://datatracker.ietf.org/doc/rfc8654" class="rfc">RFC
8654</a>) must
    be announced by the BGP neighbor, otherwise the BGP session will not be
    established. Default: off.

<span id="bgp-capabilities" class="code">capabilities *switch*</span>  
Use capability advertisement to advertise optional capabilities. This is
    standard behavior for newer BGP implementations, but there might be some
    older BGP implementations that reject such connection attempts. When
    disabled (off), features that request it (4B AS support) are also
    disabled. Default: on, with automatic fallback to off when received
    capability-related error.

<span id="bgp-advertise-hostname" class="code">advertise hostname *switch*</span>  
Advertise the hostname capability along with the hostname. Default: off.

<span id="bgp-require-hostname" class="code">require hostname *switch*</span>  
If enabled, the hostname capability must be announced by the BGP
    neighbor, otherwise the BGP session negotiation fails. Default: off.

<span id="bgp-disable-after-error" class="code">disable after error *switch*</span>  
When an error is encountered (either locally or by the other side),
    disable the instance automatically and wait for an administrator to fix
    the problem manually. Default: off.

<span id="bgp-disable-after-cease" class="code">disable after cease *switch*\|*set-of-flags*</span>  
When a Cease notification is received, disable the instance
    automatically and wait for an administrator to fix the problem manually.
    When used with *switch* argument, it means handle every Cease subtype
    with the exception of `connection collision`. Default: off.

The *set-of-flags* allows to narrow down relevant Cease subtypes. The
    syntax is `{`*flag*` [, `*...*`] }`, where flags are: `cease`,
    `prefix limit hit`, `administrative shutdown`,
    `peer deconfigured`, `administrative reset`,
    `connection rejected`, `configuration change`,
    `connection collision`, `out of resources`.

<span id="bgp-hold-time" class="code">hold time *number*</span>  
Time in seconds to wait for a Keepalive message from the other side
    before considering the connection stale. The effective value is
    negotiated during session establishment and it is a minimum of this
    configured value and the value proposed by the peer. The zero value has
    a special meaning, signifying that no keepalives are used. Default: 240
    seconds.

<span id="bgp-min-hold-time" class="code">min hold time *number*</span>  
Minimum value of the hold time that is accepted during session negotiation.
    If the peer proposes a lower value, the session is rejected with error.
    Default: none.

<span id="bgp-startup-hold-time" class="code">startup hold time *number*</span>  
Value of the hold timer used before the routers have a chance to exchange
    open messages and agree on the real value. Default: 240 seconds.

<span id="bgp-keepalive-time" class="code">keepalive time *number*</span>  
Delay in seconds between sending of two consecutive Keepalive messages.
    The effective value depends on the negotiated hold time, as it is scaled
    to maintain proportion between the keepalive time and the hold time.
    Default: One third of the hold time.

<span id="bgp-min-keepalive-time" class="code">min keepalive time *number*</span>  
Minimum value of the keepalive time that is accepted during session
    negotiation. If the proposed hold time would lead to a lower value of
    the keepalive time, the session is rejected with error. Default: none.

<span id="bgp-send-hold-time" class="code">send hold time *number*</span>  
Maximum time in seconds betweeen successfull transmissions of BGP messages.
    Send hold timer drops the session if the neighbor is sending keepalives,
    but does not receive our messages, causing the TCP connection to stall.
    This may happen due to malfunctioning or overwhelmed neighbor. See
    <a href="https://datatracker.ietf.org/doc/rfc9687" class="rfc">RFC
9687</a> for more details.

Like the option `keepalive time`, the effective value depends on the
    negotiated hold time, as it is scaled to maintain proportion between the
    send hold time and the keepalive time. If it is set to zero, the timer
    is disabled. Default: double of the hold timer limit.

The option `disable rx` is intended only for testing this feature and
    should not be used anywhere else. It discards received messages and
    disables the hold timer.

<span id="bgp-connect-delay-time" class="code">connect delay time *number*</span>  
Delay in seconds between protocol startup and the first attempt to
    connect. Default: 5 seconds.

<span id="bgp-connect-retry-time" class="code">connect retry time *number*</span>  
Time in seconds to wait before retrying a failed attempt to connect.
    Default: 120 seconds.

<span id="bgp-error-wait-time" class="code">error wait time *number*, *number*</span>  
Minimum and maximum delay in seconds between a protocol failure (either
    local or reported by the peer) and automatic restart. Does not apply
    when `disable after error` is configured. If consecutive errors
    happen, the delay is increased exponentially until it reaches the
    maximum. Default: 60, 300.

<span id="bgp-error-forget-time" class="code">error forget time *number*</span>  
Maximum time in seconds between two protocol failures to treat them as a
    error sequence which makes `error wait time` increase exponentially.
    Default: 300 seconds.

<span id="bgp-path-metric" class="code">path metric *switch*</span>  
Enable comparison of path lengths when deciding which BGP route is the
    best one. Default: on.

<span id="bgp-med-metric" class="code">med metric *switch*</span>  
Enable comparison of MED attributes (during best route selection) even
    between routes received from different ASes. This may be useful if all
    MED attributes contain some consistent metric, perhaps enforced in
    import filters of AS boundary routers. If this option is disabled, MED
    attributes are compared only if routes are received from the same AS
    (which is the standard behavior). Default: off.

<span id="bgp-deterministic-med" class="code">deterministic med *switch*</span>  
BGP route selection algorithm is often viewed as a comparison between
    individual routes (e.g. if a new route appears and is better than the
    current best one, it is chosen as the new best one). But the proper
    route selection, as specified by <a href="https://datatracker.ietf.org/doc/rfc4271" class="rfc">RFC
4271</a>, cannot be fully
    implemented in that way. The problem is mainly in handling the MED
    attribute. BIRD, by default, uses an simplification based on individual
    route comparison, which in some cases may lead to temporally dependent
    behavior (i.e. the selection is dependent on the order in which routes
    appeared). This option enables a different (and slower) algorithm
    implementing proper <a href="https://datatracker.ietf.org/doc/rfc4271" class="rfc">RFC
4271</a> route selection, which is
    deterministic. Alternative way how to get deterministic behavior is to
    use `med metric` option. This option is incompatible with [sorted
tables](#dsc-table-sorted).  Default: off.

<span id="bgp-igp-metric" class="code">igp metric *switch*</span>  
Enable comparison of internal distances to boundary routers during best
    route selection. Default: on.

<span id="bgp-prefer-older" class="code">prefer older *switch*</span>  
Standard route selection algorithm breaks ties by comparing router IDs.
    This changes the behavior to prefer older routes (when both are external
    and from different peer). For details, see <a href="https://datatracker.ietf.org/doc/rfc5004" class="rfc">RFC
5004</a>. Default: off.

<span id="bgp-default-med" class="code">default bgp_med *number*</span>  
Value of the Multiple Exit Discriminator to be used during route
    selection when the MED attribute is missing. Default: 0.

<span id="bgp-default-local-pref" class="code">default bgp_local_pref *number*</span>  
A default value for the Local Preference attribute. It is used when
    a new Local Preference attribute is attached to a route by the BGP
    protocol itself (for example, if a route is received through eBGP and
    therefore does not have such attribute). Default: 100 (0 in pre-1.2.0
    versions of BIRD).

<span id="bgp-local-role" class="code">local role *role-name*</span>  
BGP roles are a mechanism for route leak prevention and automatic route
    filtering based on common BGP topology relationships. They are defined
    in <a href="https://datatracker.ietf.org/doc/rfc9234" class="rfc">RFC
9234</a>. Instead of manually configuring filters and
    communities, automatic filtering is done with the help of the OTC
    attribute - a flag for routes that should be sent only to customers.
    The same attribute is also used to automatically detect and filter route
    leaks created by third parties.

This option is valid for EBGP sessions, but it is not recommended to be
    used within AS confederations (which would require manual filtering of
    `bgp_otc` attribute on confederation boundaries).

Possible *role-name* values are: `provider`,
    `rs_server`, `rs_client`, `customer` and `peer`.
    Default: No local role assigned.

<span id="bgp-require-roles" class="code">require roles *switch*</span>  
If this option is set, the BGP roles must be defined on both sides,
    otherwise the session will not be established. This behavior is defined
    in <a href="https://datatracker.ietf.org/doc/rfc9234" class="rfc">RFC
9234</a> as "strict mode" and is used to enforce corresponding
    configuration at your conterpart side. Default: disabled.

### Channel configuration

BGP supports several AFIs and SAFIs over one connection. Every AFI/SAFI
announced to the peer corresponds to one channel. The table of supported AFI/SAFIs
together with their appropriate channels follows.

| Channel name     | Table nettype | IGP table allowed | AFI | SAFI |
|:-----------------|:--------------|:------------------|----:|-----:|
| `ipv4`           | `ipv4`        | `ipv4` and `ipv6` |   1 |    1 |
| `ipv6`           | `ipv6`        | `ipv4` and `ipv6` |   2 |    1 |
| `ipv4 multicast` | `ipv4`        | `ipv4` and `ipv6` |   1 |    2 |
| `ipv6 multicast` | `ipv6`        | `ipv4` and `ipv6` |   2 |    2 |
| `ipv4 mpls`      | `ipv4`        | `ipv4` and `ipv6` |   1 |    4 |
| `ipv6 mpls`      | `ipv6`        | `ipv4` and `ipv6` |   2 |    4 |
| `vpn4 mpls`      | `vpn4`        | `ipv4` and `ipv6` |   1 |  128 |
| `vpn6 mpls`      | `vpn6`        | `ipv4` and `ipv6` |   2 |  128 |
| `vpn4 multicast` | `vpn4`        | `ipv4` and `ipv6` |   1 |  129 |
| `vpn6 multicast` | `vpn6`        | `ipv4` and `ipv6` |   2 |  129 |
| `flow4`          | `flow4`       | \---              |   1 |  133 |
| `flow6`          | `flow6`       | \---              |   2 |  133 |

BGP channel variants

The BGP protocol can be configured as MPLS-aware (by defining both AFI/SAFI
channels and the MPLS channel). In such case the BGP protocol assigns labels to
routes imported from MPLS-aware SAFIs (i.e. `ipvX mpls` and `vpnX mpls`)
and automatically announces corresponding MPLS route for each labeled route. As
BGP generally processes a large amount of routes, it is suggested to set MPLS
label policy to `aggregate`.

Note that even BGP instances without MPLS channel and without local MPLS
configuration can still propagate third-party MPLS labels, e.g. as route
reflectors, they just will not assign local labels to imported routes and will
not announce MPLS routes for local MPLS forwarding.

Due to <a href="https://datatracker.ietf.org/doc/rfc8212" class="rfc">RFC
8212</a>, external BGP protocol requires explicit configuration
of import and export policies (in contrast to other protocols, where default
policies of `import all` and `export none` are used in absence of explicit
configuration). Note that blanket policies like `all` or `none` can still
be used in explicit configuration.

BGP channels have additional config options (together with the common ones):

<span id="bgp-mandatory" class="code">mandatory *switch*</span>  
When local and neighbor sets of configured AFI/SAFI pairs differ,
    capability negotiation ensures that a common subset is used. For
    mandatory channels their associated AFI/SAFI must be negotiated
    (i.e., also announced by the neighbor), otherwise BGP session
    negotiation fails with *'Required capability missing'* error.
    Regardless, at least one AFI/SAFI must be negotiated in order to BGP
    session be successfully established. Default: off.

<span id="bgp-next-hop-keep" class="code">next hop keep *switch*\|ibgp\|ebgp</span>  
Do not modify the Next Hop attribute and advertise the current one
    unchanged even in cases where our own local address should be used
    instead. This is necessary when the BGP speaker does not forward network
    traffic (route servers and some route reflectors) and also can be useful
    in some other cases (e.g. multihop EBGP sessions). Can be enabled for
    all routes, or just for routes received from IBGP / EBGP neighbors.
    Default: disabled for regular BGP, enabled for route servers,
    `ibgp` for route reflectors.

<span id="bgp-next-hop-self" class="code">next hop self *switch*\|ibgp\|ebgp</span>  
Always advertise our own local address as a next hop, even in cases
    where the current Next Hop attribute should be used unchanged. This is
    sometimes used for routes propagated from EBGP to IBGP when IGP routing
    does not cover inter-AS links, therefore IP addreses of EBGP neighbors
    are not resolvable through IGP. Can be enabled for all routes, or just
    for routes received from IBGP / EBGP neighbors. Default: disabled.

<span id="bgp-next-hop-address" class="code">next hop address *ip*</span>  
Specify which address to use when our own local address should be
    announced in the Next Hop attribute. Default: the source address of the
    BGP session (if acceptable), or the preferred address of an associated
    interface.

<span id="bgp-next-hop-prefer" class="code">next hop prefer global\|local</span>  
For IPv6 routes, the Next Hop attribute may contain both a global IP
    address and a link-local IP address. For IBGP sessions, the global IP
    address is resolved ([gateway recursive](#bgp-gateway))
    through an IGP routing table ([igp table](#bgp-igp-table))
    to get an immediate next hop. If the resulting IGP route is a direct
    route (i.e., the next hop is a direct neighbor), then the link-local
    IP address from the Next Hop attribute is used as the immediate next hop.
    This option changes it to prefer global or link-local IP address,
    respectively. Note that even with this option set to global, route may
    end with a link-local immediate next hop when the IGP route has one.

When set to `local`, prefer link-local IPv6 addresses over global
    addresses for immediate next hops of received routes. When set to
    `global`, prefer global IPv6 address to link-local IPv6 address for
    immediate next hops of received routes. Default: global in direct mode
    (EBGP), local in recursive mode (IBGP).

<span id="bgp-link-local-next-hop-format" class="code">link local next hop format native\|single\|double</span>  
For IPv6 routes, BGP assumes that the Next Hop attribute contains a
    global IPv6 address (in the first position) and an optional link-local
    IPv6 address (in the second position): \[*global*, *link-local*\].
    When a BGP session is established using just link-local addresses, there
    may be no global IPv6 address for the next hop. BGP implementations
    differ on how to encode such next hops. BIRD `native` format is to
    send \[zero, *link-local*\], `single` format is \[*link-local*\],
    `double` format is \[*link-local*, *link-local*\]. BIRD accepts all
    these variants when decoding received routes, but this option controls
    which one it uses to encode such next hops. Default: native.

<span id="bgp-gateway" class="code">gateway direct\|recursive</span>  
For received routes, their `gw` (immediate next hop) attribute is
    computed from received `bgp_next_hop` attribute. This option
    specifies how it is computed. Direct mode means that the IP address from
    `bgp_next_hop` is used and must be directly reachable. Recursive mode
    means that the gateway is computed by an IGP routing table lookup for
    the IP address from `bgp_next_hop`. Note that there is just one level
    of indirection in recursive mode - the route obtained by the lookup must
    not be recursive itself, to prevent mutually recursive routes.

Recursive mode is the behavior specified by the BGP
    standard. Direct mode is simpler, does not require any routes in a
    routing table, and was used in older versions of BIRD, but does not
    handle well nontrivial iBGP setups and multihop. Recursive mode is
    incompatible with [sorted tables](#dsc-table-sorted). Default:
    `direct` for direct sessions, `recursive` for multihop sessions.

<span id="bgp-igp-table" class="code">igp table *name*</span>  
Specifies a table that is used as an IGP routing table. The type of this
    table must be as allowed in the table above. This option is allowed once
    for every allowed table type. Default: the same as the main table
    the channel is connected to (if eligible).

<span id="bgp-import-table" class="code">import table *switch*</span>  
A BGP import table contains all received routes from given BGP neighbor,
    before application of import filters. It is also called **Adj-RIB-In**
    in BGP terminology. BIRD BGP by default operates without import tables,
    in which case received routes are just processed by import filters,
    accepted ones are stored in the master table, and the rest is forgotten.
    Enabling `import table` allows to store unprocessed routes, which can
    be examined later by `show route`, and can be used to reconfigure
    import filters without full route refresh. Default: off.

Note that currently the import table breaks routes with recursive
    nexthops (e.g. ones from IBGP, see [gateway
recursive](#bgp-gateway)), they are not properly updated after next hop change. For
    the same reason, it also breaks re-evaluation of flowspec routes with
    [flowspec validation](#bgp-validate) option enabled on
    flowspec channels.

<span id="bgp-export-table" class="code">export table *switch*</span>  
A BGP export table contains all routes sent to given BGP neighbor, after
    application of export filters. It is also called **Adj-RIB-Out** in BGP
    terminology. BIRD BGP by default operates without export tables, in
    which case routes from master table are just processed by export filters
    and then announced by BGP. Enabling `export table` allows to store
    routes after export filter processing, so they can be examined later by
    `show route`, and can be used to eliminate unnecessary updates or
    withdraws. Default: off.

<span id="bgp-secondary" class="code">secondary *switch*</span>  
Usually, if an export filter rejects a selected route, no other route is
    propagated for that network. This option allows to try the next route in
    order until one that is accepted is found or all routes for that network
    are rejected. This can be used for route servers that need to propagate
    different tables to each client but do not want to have these tables
    explicitly (to conserve memory). This option requires that the connected
    routing table is [sorted](#dsc-table-sorted). Default: off.

<span id="bgp-validate" class="code">validate *switch*</span>  
Apply flowspec validation procedure as described in <a href="https://datatracker.ietf.org/doc/rfc8955" class="rfc">RFC
8955</a>
    section 6 and <a href="https://datatracker.ietf.org/doc/rfc9117" class="rfc">RFC
9117</a>. The Validation procedure enforces that
    only routers in the forwarding path for a network can originate flowspec
    rules for that network. The validation procedure should be used for EBGP
    to prevent injection of malicious flowspec rules from outside, but it
    should also be used for IBGP to ensure that selected flowspec rules are
    consistent with selected IP routes. The validation procedure uses an IP
    routing table ([base table](#bgp-base-table), see below)
    against which flowspec rules are validated. This option is limited to
    flowspec channels. Default: off (for compatibility reasons).

Note that currently the flowspec validation does not work reliably
    together with [import table](#bgp-import-table) option
    enabled on flowspec channels.

<span id="bgp-base-table" class="code">base table *name*</span>  
Specifies an IP table used for the flowspec validation procedure. The
    table must have enabled `trie` option, otherwise the validation
    procedure would not work. The type of the table must be `ipv4` for
    `flow4` channels and `ipv6` for `flow6` channels. This option
    is limited to flowspec channels. Default: the main table of the
    `ipv4` / `ipv6` channel of the same BGP instance, or the
    `master4` / `master6` table if there is no such channel.

<span id="bgp-extended-next-hop" class="code">extended next hop *switch*</span>  
BGP expects that announced next hops have the same address family as
    associated network prefixes. This option provides an extension to use
    IPv4 next hops with IPv6 prefixes and vice versa. For IPv4 / VPNv4
    channels, the behavior is controlled by the Extended Next Hop Encoding
    capability, as described in <a href="https://datatracker.ietf.org/doc/rfc8950" class="rfc">RFC
8950</a>. For IPv6 / VPNv6 channels,
    just IPv4-mapped IPv6 addresses are used, as described in
    <a href="https://datatracker.ietf.org/doc/rfc4798" class="rfc">RFC
4798</a> and <a href="https://datatracker.ietf.org/doc/rfc4659" class="rfc">RFC
4659</a>. Default: off.

<span id="bgp-require-extended-next-hop" class="code">require extended next hop *switch*</span>  
If enabled, the BGP Extended Next Hop Encoding capability (<a href="https://datatracker.ietf.org/doc/rfc8950" class="rfc">RFC
8950</a>)
    must be announced by the BGP neighbor, otherwise the BGP session will
    not be established. Note that this option is relevant just for IPv4 /
    VPNv4 channels, as IPv6 / VPNv6 channels use a different mechanism not
    signalled by a capability. Default: off.

<span id="bgp-add-paths" class="code">add paths *switch*\|rx\|tx</span>  
Standard BGP can propagate only one path (route) per destination network
    (usually the selected one). This option controls the ADD-PATH protocol
    extension, which allows to advertise any number of paths to a
    destination. Note that to be active, ADD-PATH has to be enabled on both
    sides of the BGP session, but it could be enabled separately for RX and
    TX direction. When active, all available routes accepted by the export
    filter are advertised to the neighbor. Default: off.

<span id="bgp-require-add-paths" class="code">require add paths *switch*</span>  
If enabled, the BGP ADD-PATH capability (<a href="https://datatracker.ietf.org/doc/rfc7911" class="rfc">RFC
7911</a>) must be
    announced by the BGP neighbor, otherwise the BGP session will not be
    established. Announced directions in the capability must be compatible
    with locally configured directions. E.g., If `add path tx` is
    configured locally, then the neighbor capability must announce RX.
    Default: off.

<span id="bgp-aigp" class="code">aigp *switch*\|originate</span>  
The BGP protocol does not use a common metric like other routing
    protocols, instead it uses a set of criteria for route selection
    consisting both overall AS path length and a distance to the nearest AS
    boundary router. Assuming that metrics of different autonomous systems
    are incomparable, once a route is propagated from an AS to a next one,
    the distance in the old AS does not matter.

The AIGP extension (<a href="https://datatracker.ietf.org/doc/rfc7311" class="rfc">RFC
7311</a>) allows to propagate accumulated
    IGP metric (in the AIGP attribute) through both IBGP and EBGP links,
    computing total distance through multiple autonomous systems (assuming
    they use comparable IGP metric). The total AIGP metric is compared in
    the route selection process just after Local Preference comparison (and
    before AS path length comparison).

This option controls whether AIGP attribute propagation is allowed on
    the session. Optionally, it can be set to `originate`, which not only
    allows AIGP attribute propagation, but also new AIGP attributes are
    automatically attached to non-BGP routes with valid IGP metric (e.g.
    `ospf_metric1`) as they are exported to the BGP session. Default:
    enabled for IBGP (and intra-confederation EBGP), disabled for regular
    EBGP.

<span id="bgp-cost" class="code">cost *number*</span>  
When BGP [gateway mode](#bgp-gateway) is `recursive`
    (mainly multihop IBGP sessions), then the distance to BGP next hop is
    based on underlying IGP metric. This option specifies the distance to
    BGP next hop for BGP sessions in direct gateway mode (mainly direct
    EBGP sessions).

<span id="bgp-graceful-restart-c" class="code">graceful restart *switch*</span>  
Although BGP graceful restart is configured mainly by protocol-wide
    [options](#bgp-graceful-restart), it is possible to
    configure restarting role per AFI/SAFI pair by this channel option.
    The option is ignored if graceful restart is disabled by protocol-wide
    option. Default: off in aware mode, on in full mode.

<span id="bgp-long-lived-graceful-restart-c" class="code">long lived graceful restart *switch*</span>  
BGP long-lived graceful restart is configured mainly by protocol-wide
    [options](#bgp-long-lived-graceful-restart), but the
    restarting role can be set per AFI/SAFI pair by this channel option.
    The option is ignored if long-lived graceful restart is disabled by
    protocol-wide option. Default: off in aware mode, on in full mode.

<span id="bgp-long-lived-stale-time-c" class="code">long lived stale time *number*</span>  
Like previous graceful restart channel options, this option allows to
    set [long lived stale time](#bgp-long-lived-stale-time)
    per AFI/SAFI pair instead of per protocol. Default: set by protocol-wide
    option.

<span id="bgp-min-long-lived-stale-time-c" class="code">min long lived stale time *number*</span>  
Like previous graceful restart channel options, this option allows to
    set [min long lived stale time](#bgp-min-long-lived-stale-time)
    per AFI/SAFI pair instead of per protocol. Default: set by protocol-wide
    option.

<span id="bgp-max-long-lived-stale-time-c" class="code">max long lived stale time *number*</span>  
Like previous graceful restart channel options, this option allows to
    set [max long lived stale time](#bgp-max-long-lived-stale-time)
    per AFI/SAFI pair instead of per protocol. Default: set by protocol-wide
    option.

### Reconfiguration

Running the `configure` command with a changed configuration file will
trigger a reconfiguration. This may cause a reload of the affected channel or
a restart of the BGP protocol. A change in options that just affect route
processing generally causes a reload, while a change in setting of BGP
capabilities or other properties negotiated during session establishment always
leads to a restart.

With the `configure soft` command, configuration changes do not trigger
a reload, but instead just log a message about the change, suggesting manual
reload of the channel. However, changes demanding restart still trigger a
restart of the protocol. See [configure](#cli-configure) and
[reload](#cli-reload) commands for more details. In the rest of
the section, we assume the usage of the `configure` command without the
`soft` option.

Changes in these channel options cause a reload of the channel in the import
direction (re-import of routes from the BGP neighbor):

- `import`
- `preference`
- `gateway`
- `next hop prefer`
- `aigp`
- `cost`

Note: Reload in the import direction requires either the `route refresh`
capability negotiated or the `import table` option enabled. If neither one is
available, the reconfiguration will trigger a restart instead of a reload. This
does not apply for export direction.

Changes in these channel options cause a reload of the channel in the export
direction (re-export of routes to the BGP neighbor):

- `export`
- `next hop self`
- `next hop keep`
- `link local next hop format`
- `aigp`
- `aigp originate`

Protocol options that cause a restart when changed:

- `router id`
- `hostname`
- `vrf`

**All** BGP-specific protocol options cause a restart when changed **except** for these:

- `bfd`
- `check link`
- `require route refresh`
- `require enhanced route refresh`
- `require AS4`
- `require extended messages`
- `require hostname`
- `require graceful restart`
- `require long lived graceful restart`
- `interface range`
   if the already existing option is updated to another value.

Channel options that cause a restart when changed:

- `table`
- `secondary`
- `validate`
- `graceful restart`
- `long lived graceful restart`
- `long lived stale time`
- `next hop address`
- `extended next hop`
- `add paths`
- `import table`
- `export table`
- `igp table`
- `base table`

Channel options that may cause a restart (when changed to a value
incompatible with the current protocol state):

- `mandatory`
- `min long lived stale time`
- `max long lived stale time`

All these configuration changes apply also to the spawned dynamic BGP sessions
and therefore reconfiguring the parent protocol may lead to shutdown of some or all
of the dynamic BGP sessions.

### Attributes

BGP defines several route attributes. Some of them (those marked with
\``I`' in the table below) are available on internal BGP connections only,
some of them (marked with \``O`') are optional.

<span id="rta-bgp-path" class="code">bgppath bgp_path</span>  
Sequence of AS numbers describing the AS path the packet will travel
    through when forwarded according to the particular route. In case of
    internal BGP it doesn't contain the number of the local AS.

<span id="rta-bgp-local-pref" class="code">int bgp_local_pref \[I\]</span>  
Local preference value used for selection among multiple BGP routes (see
    the selection rules above). It's used as an additional metric which is
    propagated through the whole local AS.

<span id="rta-bgp-med" class="code">int bgp_med \[O\]</span>  
The Multiple Exit Discriminator of the route is an optional attribute
    which is used on external (inter-AS) links to convey to an adjacent AS
    the optimal entry point into the local AS. The received attribute is
    also propagated over internal BGP links. The attribute value is zeroed
    when a route is exported to an external BGP instance to ensure that the
    attribute received from a neighboring AS is not propagated to other
    neighboring ASes. A new value might be set in the export filter of an
    external BGP instance. See <a href="https://datatracker.ietf.org/doc/rfc4451" class="rfc">RFC
4451</a> for further discussion of
    BGP MED attribute.

<span id="rta-bgp-origin" class="code">enum bgp_origin</span>  
Origin of the route: either `ORIGIN_IGP` if the route has originated
    in an interior routing protocol or `ORIGIN_EGP` if it's been imported
    from the `EGP` protocol (nowadays it seems to be obsolete) or
    `ORIGIN_INCOMPLETE` if the origin is unknown.

<span id="rta-bgp-next-hop" class="code">ip bgp_next_hop</span>  
Next hop to be used for forwarding of packets to this destination. On
    internal BGP connections, it's an address of the originating router if
    it's inside the local AS or a boundary router the packet will leave the
    AS through if it's an exterior route, so each BGP speaker within the AS
    has a chance to use the shortest interior path possible to this point.

<span id="rta-bgp-atomic-aggr" class="code">void bgp_atomic_aggr \[O\]</span>  
This is an optional attribute which carries no value, but the sole
    presence of which indicates that the route has been aggregated from
    multiple routes by some router on the path from the originator.

<span id="rta-bgp-aggregator" class="code">void bgp_aggregator \[O\]</span>  
This is an optional attribute specifying AS number and IP address of the
    BGP router that created the route by aggregating multiple BGP routes.
    Currently, the attribute is not accessible from filters.

<span id="rta-bgp-community" class="code">clist bgp_community \[O\]</span>  
List of community values associated with the route. Each such value is a
    pair (represented as a `pair` data type inside the filters) of 16-bit
    integers, the first of them containing the number of the AS which
    defines the community and the second one being a per-AS identifier.
    There are lots of uses of the community mechanism, but generally they
    are used to carry policy information like "don't export to USA peers".
    As each AS can define its own routing policy, it also has a complete
    freedom about which community attributes it defines and what will their
    semantics be.

<span id="rta-bgp-ext-community" class="code">eclist bgp_ext_community \[O\]</span>  
List of extended community values associated with the route. Extended
    communities have similar usage as plain communities, but they have an
    extended range (to allow 4B ASNs) and a nontrivial structure with a type
    field. Individual community values are represented using an `ec` data
    type inside the filters.

<span id="rta-bgp-large-community" class="code">lclist bgp_large_community \[O\]</span>  
List of large community values associated with the route. Large BGP
    communities is another variant of communities, but contrary to extended
    communities they behave very much the same way as regular communities,
    just larger -- they are uniform untyped triplets of 32bit numbers.
    Individual community values are represented using an `lc` data type
    inside the filters.

<span id="rta-bgp-originator-id" class="code">quad bgp_originator_id \[I, O\]</span>  
This attribute is created by the route reflector when reflecting the
    route and contains the router ID of the originator of the route in the
    local AS.

<span id="rta-bgp-cluster-list" class="code">clist bgp_cluster_list \[I, O\]</span>  
This attribute contains a list of cluster IDs of route reflectors. Each
    route reflector prepends its cluster ID when reflecting the route.

<span id="rta-bgp-aigp" class="code">void bgp_aigp \[O\]</span>  
This attribute contains accumulated IGP metric, which is a total
    distance to the destination through multiple autonomous systems.
    Currently, the attribute is not accessible from filters.

<span id="bgp-otc" class="code">int bgp_otc \[O\]</span>  
This attribute is defined in <a href="https://datatracker.ietf.org/doc/rfc9234" class="rfc">RFC
9234</a>. OTC is a flag that marks
    routes that should be sent only to customers. If [local
role](#bgp-local-role) is configured it set automatically.

For attributes unknown by BIRD, the user can assign a name (on top level) to
an attribute by its number. This defined name can be used then to get, set (as a
bytestring, transitive) or unset the given attribute even though BIRD knows
nothing about it.

Note that it is not possible to define an attribute with the same number
as one known by BIRD, therefore use of this statement carries a risk of
incompatibility with future BIRD versions.

<span id="bgp-attribute-custom"></span>`attribute bgp `*number*` bytestring `*name*`;`

### Example


    protocol bgp {
        local 198.51.100.14 as 65000;        # Use a private AS number
        neighbor 198.51.100.130 as 64496;    # Our neighbor ...
        multihop;                # ... which is connected indirectly
        authentication ao;           # We use TCP-AO authentication
        keys {
            key {
                id 0;
                secret "hello321";
                algorithm hmac sha256;
                preferred;
            };
            key {
                send id 2;
                recv id 1;
                secret "bye123";
                algorithm cmac aes128;
            };
        };
        ipv4 {
            export filter {              # We use non-trivial export rules
                if source = RTS_STATIC then { # Export only static routes
                    # Assign our community
                    bgp_community.add((65000,64501));
                    # Artificially increase path length
                    # by advertising local AS number twice
                    if bgp_path ~ [= 65000 =] then
                        bgp_path.prepend(65000);
                    accept;
                }
                reject;
            };
            import all;
            next hop self; # advertise this router as next hop
            igp table myigptable4; # IGP table for routes with IPv4 nexthops
            igp table myigptable6; # IGP table for routes with IPv6 nexthops
        };
        ipv6 {
            export filter mylargefilter; # We use a named filter
            import all;
            missing lladdr self;
            igp table myigptable4; # IGP table for routes with IPv4 nexthops
            igp table myigptable6; # IGP table for routes with IPv6 nexthops
        };
        ipv4 multicast {
            import all;
            export filter someotherfilter;
            table mymulticasttable4; # Another IPv4 table, dedicated for multicast
            igp table myigptable4;
        };
    }
