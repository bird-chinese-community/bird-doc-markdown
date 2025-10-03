## Babel

### Introduction

The Babel protocol
(<a href="https://datatracker.ietf.org/doc/rfc8966" class="rfc">RFC
8966</a>) is a loop-avoiding distance-vector routing protocol that is
robust and efficient both in ordinary wired networks and in wireless mesh
networks. Babel is conceptually very simple in its operation and "just works"
in its default configuration, though some configuration is possible and in some
cases desirable.

The Babel protocol is dual stack; i.e., it can carry both IPv4 and IPv6
routes over the same IPv6 transport. For sending and receiving Babel packets,
only a link-local IPv6 address is needed.

BIRD implements an extension for IPv6 source-specific routing (SSR or SADR),
but must be configured accordingly to use it. SADR-enabled Babel router can
interoperate with non-SADR Babel router, but the later would ignore routes
with specific (non-zero) source prefix.

### Configuration

The Babel protocol support both IPv4 and IPv6 channels; both can be
configured simultaneously. It can also be configured with [IPv6
SADR](#ip-sadr-routes) channel instead of regular IPv6
channel, in such case SADR support is enabled. Babel supports no global
configuration options apart from those common to all other protocols, but
supports the following per-interface configuration options:


    protocol babel [<name>] {
        ipv4 { <channel config> };
        ipv6 [sadr] { <channel config> };
        randomize router id <switch>;
        interface <interface pattern> {
            type wired|wireless|tunnel;
            rxcost <number>;
            limit <number>;
            hello interval <time>;
            update interval <time>;
            port <number>;
            tx class|dscp <number>;
            tx priority <number>;
            rx buffer <number>;
            tx length <number>;
            check link <switch>;
            next hop ipv4 <address>;
            next hop ipv6 <address>;
            next hop prefer native|ipv6;
            extended next hop <switch>;
            rtt cost <number>;
            rtt min <time>;
            rtt max <time>;
            rtt decay <number>;
            send timestamps <switch>;
            authentication none|mac [permissive];
            password "<text>";
            password "<text>" {
                id <number>;
                generate from "<date>";
                generate to "<date>";
                accept from "<date>";
                accept to "<date>";
                from "<date>";
                to "<date>";
                algorithm ( hmac sha1 | hmac sha256 | hmac sha384 |
        hmac sha512 | blake2s128 | blake2s256 | blake2b256 | blake2b512 );
            };
        };
    }

<span id="babel-channel" class="code">ipv4 \| ipv6 \[sadr\] *channel config*</span>  
The supported channels are IPv4, IPv6, and IPv6 SADR.

<span id="babel-random-router-id" class="code">randomize router id *switch*</span>  
If enabled, Bird will randomize the top 32 bits of its router ID whenever
      the protocol instance starts up. If a Babel node restarts, it loses its
      sequence number, which can cause its routes to be rejected by peers until
      the state is cleared out by other nodes in the network (which can take on
      the order of minutes). Enabling this option causes Bird to pick a random
      router ID every time it starts up, which avoids this problem at the cost
      of not having stable router IDs in the network. Default: no.

<span id="babel-type" class="code">type wired\|wireless\|tunnel</span>  
This option specifies the interface type: Wired, wireless or tunnel. On
      wired interfaces a neighbor is considered unreachable after a small number
      of Hello packets are lost, as described by `limit` option. On wireless
      interfaces the ETX link quality estimation technique is used to compute
      the metrics of routes discovered over this interface. This technique will
      gradually degrade the metric of routes when packets are lost rather than
      the more binary up/down mechanism of wired type links. A tunnel is like a
      wired interface, but turns on RTT-based metrics with a default cost of 96.
      Default: `wired`.

<span id="babel-rxcost" class="code">rxcost *number*</span>  
This option specifies the nominal RX cost of the interface. The effective
      neighbor costs for route metrics will be computed from this value with a
      mechanism determined by the interface `type`. Note that in contrast to
      other routing protocols like RIP or OSPF, the `rxcost` specifies the
      cost of RX instead of TX, so it affects primarily neighbors' route
      selection and not local route selection. Default: 96 for wired interfaces,
      256 for wireless.

<span id="babel-limit" class="code">limit *number*</span>  
BIRD keeps track of received Hello messages from each neighbor to
      establish neighbor reachability. For wired type interfaces, this option
      specifies how many of last 16 hellos have to be correctly received in
      order to neighbor is assumed to be up. The option is ignored on wireless
      type interfaces, where gradual cost degradation is used instead of sharp
      limit. Default: 12.

<span id="babel-hello" class="code">hello interval *time* s\|ms</span>  
Interval at which periodic Hello messages are sent on this interface,
      with time units. Default: 4 seconds.

<span id="babel-update" class="code">update interval *time* s\|ms</span>  
Interval at which periodic (full) updates are sent, with time
      units. Default: 4 times the hello interval.

<span id="babel-port" class="code">port *number*</span>  
This option selects an UDP port to operate on. The default is to operate
      on port 6696 as specified in the Babel RFC.

<span id="babel-tx-class" class="code">tx class\|dscp\|priority *number*</span>  
These options specify the ToS/DiffServ/Traffic class/Priority of the
      outgoing Babel packets. See [tx class](#proto-tx-class) common
      option for detailed description.

<span id="babel-rx-buffer" class="code">rx buffer *number*</span>  
This option specifies the size of buffers used for packet processing.
      The buffer size should be bigger than maximal size of received packets.
      The default value is the interface MTU, and the value will be clamped to a
      minimum of 512 bytes + IP packet overhead.

<span id="babel-tx-length" class="code">tx length *number*</span>  
This option specifies the maximum length of generated Babel packets. To
      avoid IP fragmentation, it should not exceed the interface MTU value.
      The default value is the interface MTU value, and the value will be
      clamped to a minimum of 512 bytes + IP packet overhead.

<span id="babel-check-link" class="code">check link *switch*</span>  
If set, the hardware link state (as reported by OS) is taken into
      consideration. When the link disappears (e.g. an ethernet cable is
      unplugged), neighbors are immediately considered unreachable and all
      routes received from them are withdrawn. It is possible that some
      hardware drivers or platforms do not implement this feature. Default:
      yes.

<span id="babel-next-hop-ipv4" class="code">next hop ipv4 *address*</span>  
Set the IPv4 next hop address advertised for (IPv4) routes advertised on
      this interface. Default: the preferred IPv4 address of the interface.

<span id="babel-next-hop-ipv6" class="code">next hop ipv6 *address*</span>  
Set the IPv6 next hop address advertised for routes advertised on this
      interface. If not set, the same link-local address that is used as the
      source for Babel packets will be used. In normal operation, it should not
      be necessary to set this option.

<span id="babel-next-hop-prefer" class="code">next hop prefer native\|ipv6</span>  
By default, BIRD prefers to advertise IPv4 routes with an IPv4 next hop
      address, using an IPv6 next hop address only when IPv4 addresses are
      absent from the interface. When set to `ipv6`, BIRD will advertise IPv4
      routes with an IPv6 next hop address even when IPv4 addresses are present
      on the interface (assuming the option [extended next
hop](#babel-extended-next-hop) is enabled). Default: native.

<span id="babel-extended-next-hop" class="code">extended next hop *switch*</span>  
Specify whether BIRD should allow IPv4 routes with an IPv6 next hop, as
      described in <a href="https://datatracker.ietf.org/doc/rfc9229" class="rfc">RFC
9229</a>. Note that when both IPv4 and IPv6 next hops
      are available, the option [next hop
prefer](#babel-next-hop-prefer) controls which one is advertised. Default: yes.

<span id="babel-rtt-cost" class="code">rtt cost *number*</span>  
The RTT-based cost that will be applied to all routes from each neighbour
      based on the measured RTT to that neighbour. If this value is set,
      timestamps will be included in generated Babel Hello and IHU messages, and
      (if the neighbours also have timestamps enabled), the RTT to each
      neighbour will be computed. An additional cost is added to a neighbour if
      its RTT is above the [rtt min](#babel-rtt-min) value
      configured on the interface. The added cost scales linearly from 0 up to
      the RTT cost configured in this option; the full cost is applied if the
      neighbour RTT reaches the RTT configured in the [rtt
max](#babel-rtt-max) option (and for all RTTs above this value). Default: 0
      (disabled), except for tunnel interfaces, where it is 96.

<span id="babel-rtt-min" class="code">rtt min *time* s\|ms</span>  
The minimum RTT above which the RTT cost will start to be applied (scaling
      linearly from zero up to the full cost). Default: 10 ms

<span id="babel-rtt-max" class="code">rtt max *time* s\|ms</span>  
The maximum RTT above which the full RTT cost will start be applied.
      Default: 120 ms

<span id="babel-rtt-decay" class="code">rtt decay *number*</span>  
The decay factor used for the exponentional moving average of the RTT
      samples from each neighbour, in units of 1/256. Higher values discards old
      RTT samples faster. Must be between 1 and 256. Default: 42

<span id="babel-send-timestamps" class="code">send timestamps *switch*</span>  
Whether to send the timestamps used for RTT calculation on this interface.
      Sending the timestamps enables peers to calculate an RTT to this node,
      even if no RTT cost is applied to the route metrics. Default: yes.

<span id="babel-authentication" class="code">authentication none\|mac \[permissive\]</span>  
Selects authentication method to be used. `none` means that packets
      are not authenticated at all, `mac` means MAC authentication is
      performed as described in <a href="https://datatracker.ietf.org/doc/rfc8967" class="rfc">RFC
8967</a>. If MAC authentication is
      selected, the `permissive` suffix can be used to select an operation
      mode where outgoing packets are signed, but incoming packets will be
      accepted even if they fail authentication. This can be useful for
      incremental deployment of MAC authentication across a network. If MAC
      authentication is selected, a key must be specified with the
      `password` configuration option. Default: none.

<span id="babel-password" class="code">password "*text*"</span>  
Specifies a password used for authentication. See the [password](#proto-pass) common option for a detailed description. The Babel
      protocol will only accept HMAC-based algorithms or one of the Blake
      algorithms, and the length of the supplied password string must match the
      key size used by the selected algorithm.

### Attributes

Babel defines just one attribute: the internal babel metric of the route. It
is exposed as the `babel_metric` attribute and has range from 1 to infinity
(65535).

### Example


    protocol babel {
        interface "eth*" {
            type wired;
        };
        interface "wlan0", "wlan1" {
            type wireless;
            hello interval 1;
            rxcost 512;
        };
        interface "tap0";

        # This matches the default of babeld: redistribute all addresses
        # configured on local interfaces, plus re-distribute all routes received
        # from other babel peers.

        ipv4 {
            export where (source = RTS_DEVICE) || (source = RTS_BABEL);
        };
        ipv6 {
            export where (source = RTS_DEVICE) || (source = RTS_BABEL);
        };
    }

### Known issues

When retracting a route, Babel generates an unreachable route for a little
while (according to RFC). The interaction of this behavior with other protocols
is not well tested and strange things may happen.
