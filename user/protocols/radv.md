## RAdv

### Introduction

The RAdv protocol is an implementation of Router Advertisements, which are
used in the IPv6 stateless autoconfiguration. IPv6 routers send (in irregular
time intervals or as an answer to a request) advertisement packets to connected
networks. These packets contain basic information about a local network (e.g. a
list of network prefixes), which allows network hosts to autoconfigure network
addresses and choose a default route. BIRD implements router behavior as defined
in <a href="https://datatracker.ietf.org/doc/rfc4861" class="rfc">RFC
4861</a>, router preferences and specific routes (<a href="https://datatracker.ietf.org/doc/rfc4191" class="rfc">RFC
4191</a>),
and DNS extensions (<a href="https://datatracker.ietf.org/doc/rfc6106" class="rfc">RFC
6106</a>).

The RAdv protocols supports just IPv6 channel.

### Configuration

There are several classes of definitions in RAdv configuration -- interface
definitions, prefix definitions and DNS definitions:

<span id="radv-iface" class="code">interface *pattern* \[, *...*\] { *options* }</span>  
Interface definitions specify a set of interfaces on which the
    protocol is activated and contain interface specific options.
    See [interface](#proto-iface) common options for
    detailed description.

<span id="radv-prefix" class="code">prefix *prefix* { *options* }</span>  
Prefix definitions allow to modify a list of advertised prefixes. By
    default, the advertised prefixes are the same as the network prefixes
    assigned to the interface. For each network prefix, the matching prefix
    definition is found and its options are used. If no matching prefix
    definition is found, the prefix is used with default options.

Prefix definitions can be either global or interface-specific. The
    second ones are part of interface options. The prefix definition
    matching is done in the first-match style, when interface-specific
    definitions are processed before global definitions. As expected, the
    prefix definition is matching if the network prefix is a subnet of the
    prefix in prefix definition.

<span id="radv-rdnss" class="code">rdnss { *options* }</span>  
RDNSS definitions allow to specify a list of advertised recursive DNS
    servers together with their options. As options are seldom necessary,
    there is also a short variant `rdnss `*address* that just
    specifies one DNS server. Multiple definitions are cumulative. RDNSS
    definitions may also be interface-specific when used inside interface
    options. By default, interface uses both global and interface-specific
    options, but that can be changed by `rdnss local` option.

<span id="radv-dnssl" class="code">dnssl { *options* }</span>  
DNSSL definitions allow to specify a list of advertised DNS search
    domains together with their options. Like `rdnss` above, multiple
    definitions are cumulative, they can be used also as interface-specific
    options and there is a short variant `dnssl `*domain* that just
    specifies one DNS search domain.

<span id="radv-custom-option" class="code">custom option type *number* value *bytestring*</span>  
Custom option definitions allow to define an arbitrary option to
    advertise. You need to specify the option type number and the binary
    payload of the option. The length field is calculated automatically.
    Like `rdnss` above, multiple definitions are cumulative, they can
    be used also as interface-specific options.

The following example advertises PREF64 option (<a href="https://datatracker.ietf.org/doc/rfc8781" class="rfc">RFC
8781</a>) with
    prefix `2001:db8:a:b::/96` and the lifetime of `1 hour`:

<span id="radv-custom-option-exam"></span>


    custom option type 38 value hex:0e:10:20:01:0d:b8:00:0a:00:0b:00:00:00:00;

<span id="radv-trigger" class="code">trigger *prefix*</span>  
RAdv protocol could be configured to change its behavior based on
    availability of routes. When this option is used, the protocol waits in
    suppressed state until a *trigger route* (for the specified network)
    is exported to the protocol, the protocol also returns to suppressed
    state if the *trigger route* disappears. Note that route export
    depends on specified export filter, as usual. This option could be used,
    e.g., for handling failover in multihoming scenarios.

During suppressed state, router advertisements are generated, but with
    some fields zeroed. Exact behavior depends on which fields are zeroed,
    this can be configured by `sensitive` option for appropriate
    fields. By default, just `default lifetime` (also called `router
    lifetime`) is zeroed, which means hosts cannot use the router as a
    default router. `preferred lifetime` and `valid lifetime` could
    also be configured as `sensitive` for a prefix, which would cause
    autoconfigured IPs to be deprecated or even removed.

<span id="radv-propagate-routes" class="code">propagate routes *switch*</span>  
This option controls propagation of more specific routes, as defined in
    <a href="https://datatracker.ietf.org/doc/rfc4191" class="rfc">RFC
4191</a>. If enabled, all routes exported to the RAdv protocol,
    with the exception of the trigger prefix, are added to advertisments as
    additional options. The lifetime and preference of advertised routes can
    be set individually by `ra_lifetime` and `ra_preference` route
    attributes, or per interface by `route lifetime` and
    `route preference` options. Default: disabled.

Note that the RFC discourages from sending more than 17 routes and
    recommends the routes to be configured manually.

Interface specific options:

<span id="radv-iface-max-ra-interval" class="code">max ra interval *expr*</span>  
Unsolicited router advertisements are sent in irregular time intervals.
    This option specifies the maximum length of these intervals, in seconds.
    Valid values are 4-1800. Default: 600

<span id="radv-iface-min-ra-interval" class="code">min ra interval *expr*</span>  
This option specifies the minimum length of that intervals, in seconds.
    Must be at least 3 and at most 3/4 \* `max ra interval`. Default:
    about 1/3 \* `max ra interval`.

<span id="radv-iface-min-delay" class="code">min delay *expr*</span>  
The minimum delay between two consecutive router advertisements, in
    seconds. Default: 3

<span id="radv-solicited-ra-unicast" class="code">solicited ra unicast *switch*</span>  
Solicited router advertisements are usually sent to all-nodes multicast
    group like unsolicited ones, but the router can be configured to send
    them as unicast directly to soliciting nodes instead. This is especially
    useful on wireless networks (see <a href="https://datatracker.ietf.org/doc/rfc7772" class="rfc">RFC
7772</a>). Default: no

<span id="radv-iface-managed" class="code">managed *switch*</span>  
This option specifies whether hosts should use DHCPv6 for IP address
    configuration. Default: no

<span id="radv-iface-other-config" class="code">other config *switch*</span>  
This option specifies whether hosts should use DHCPv6 to receive other
    configuration information. Default: no

<span id="radv-iface-link-mtu" class="code">link mtu *expr*</span>  
This option specifies which value of MTU should be used by hosts. 0
    means unspecified. Default: 0

<span id="radv-iface-reachable-time" class="code">reachable time *expr*</span>  
This option specifies the time (in milliseconds) how long hosts should
    assume a neighbor is reachable (from the last confirmation). Maximum is
    3600000, 0 means unspecified. Default 0.

<span id="radv-iface-retrans-timer" class="code">retrans timer *expr*</span>  
This option specifies the time (in milliseconds) how long hosts should
    wait before retransmitting Neighbor Solicitation messages. 0 means
    unspecified. Default 0.

<span id="radv-iface-current-hop-limit" class="code">current hop limit *expr*</span>  
This option specifies which value of Hop Limit should be used by
    hosts. Valid values are 0-255, 0 means unspecified. Default: 64

<span id="radv-iface-default-lifetime" class="code">default lifetime *expr* \[sensitive *switch*\]</span>  
This option specifies the time (in seconds) how long (since the receipt
    of RA) hosts may use the router as a default router. 0 means do not use
    as a default router. For `sensitive` option, see [trigger](#radv-trigger).
    Default: 3 \* `max ra interval`, `sensitive` yes.

<span id="radv-iface-default-preference" class="code">default preference low\|medium\|high</span>  
This option specifies the Default Router Preference value to advertise
    to hosts. Default: medium.

<span id="radv-iface-route-lifetime" class="code">route lifetime *expr* \[sensitive *switch*\]</span>  
This option specifies the default value of advertised lifetime for
    specific routes; i.e., the time (in seconds) for how long (since the
    receipt of RA) hosts should consider these routes valid. A special value
    0xffffffff represents infinity. The lifetime can be overriden on a per
    route basis by the [ra_lifetime](#rta-ra-lifetime) route
    attribute. Default: 3 \* `max ra interval`, `sensitive` no.

For the `sensitive` option, see [trigger](#radv-trigger).
    If `sensitive` is enabled, even the routes with the `ra_lifetime`
    attribute become sensitive to the trigger.

<span id="radv-iface-route-preference" class="code">route preference low\|medium\|high</span>  
This option specifies the default value of advertised route preference
    for specific routes. The value can be overriden on a per route basis by
    the [ra_preference](#rta-ra-preference) route attribute.
    Default: medium.

<span id="radv-prefix-linger-time" class="code">prefix linger time *expr*</span>  
When a prefix or a route disappears, it is advertised for some time with
    zero lifetime, to inform clients it is no longer valid. This option
    specifies the time (in seconds) for how long prefixes are advertised
    that way. Default: 3 \* `max ra interval`.

<span id="radv-route-linger-time" class="code">route linger time *expr*</span>  
When a prefix or a route disappears, it is advertised for some time with
    zero lifetime, to inform clients it is no longer valid. This option
    specifies the time (in seconds) for how long routes are advertised
    that way. Default: 3 \* `max ra interval`.

<span id="radv-iface-rdnss-local" class="code">rdnss local *switch*</span>  
Use only local (interface-specific) RDNSS definitions for this
    interface. Otherwise, both global and local definitions are used. Could
    also be used to disable RDNSS for given interface if no local definitons
    are specified. Default: no.

<span id="radv-iface-dnssl-local" class="code">dnssl local *switch*</span>  
Use only local DNSSL definitions for this interface. See `rdnss local`
    option above. Default: no.

<span id="radv-iface-custom-local" class="code">custom option local *switch*</span>  
Use only local custom option definitions for this interface. See `rdnss local`
    option above. Default: no.

Prefix specific options

<span id="radv-prefix-skip" class="code">skip *switch*</span>  
This option allows to specify that given prefix should not be
    advertised. This is useful for making exceptions from a default policy
    of advertising all prefixes. Note that for withdrawing an already
    advertised prefix it is more useful to advertise it with zero valid
    lifetime. Default: no

<span id="radv-prefix-onlink" class="code">onlink *switch*</span>  
This option specifies whether hosts may use the advertised prefix for
    onlink determination. Default: yes

<span id="radv-prefix-autonomous" class="code">autonomous *switch*</span>  
This option specifies whether hosts may use the advertised prefix for
    stateless autoconfiguration. Default: yes

<span id="radv-prefix-pd-preferred" class="code">pd preferred*switch*</span>  
This option specifies whether hosts should use prefix delegation in DHCPv6
    instead of using stateless address autoconfiguration (SLAAC).
    Default: no.

<span id="radv-prefix-valid-lifetime" class="code">valid lifetime *expr* \[sensitive *switch*\]</span>  
This option specifies the time (in seconds) how long (after the
    receipt of RA) the prefix information is valid, i.e., autoconfigured
    IP addresses can be assigned and hosts with that IP addresses are
    considered directly reachable. 0 means the prefix is no longer
    valid. For `sensitive` option, see [trigger](#radv-trigger).
    Default: 86400 (1 day), `sensitive` no.

<span id="radv-prefix-preferred-lifetime" class="code">preferred lifetime *expr* \[sensitive *switch*\]</span>  
This option specifies the time (in seconds) how long (after the
    receipt of RA) IP addresses generated from the prefix using stateless
    autoconfiguration remain preferred. For `sensitive` option,
    see [trigger](#radv-trigger). Default: 14400 (4 hours),
    `sensitive` no.

RDNSS specific options:

<span id="radv-rdnss-ns" class="code">ns *address*</span>  
This option specifies one recursive DNS server. Can be used multiple
    times for multiple servers. It is mandatory to have at least one
    `ns` option in `rdnss` definition.

<span id="radv-rdnss-lifetime" class="code">lifetime \[mult\] *expr*</span>  
This option specifies the time how long the RDNSS information may be
    used by clients after the receipt of RA. It is expressed either in
    seconds or (when `mult` is used) in multiples of `max ra
    interval`. Note that RDNSS information is also invalidated when
    `default lifetime` expires. 0 means these addresses are no longer
    valid DNS servers. Default: 3 \* `max ra interval`.

DNSSL specific options:

<span id="radv-dnssl-domain" class="code">domain *address*</span>  
This option specifies one DNS search domain. Can be used multiple times
    for multiple domains. It is mandatory to have at least one `domain`
    option in `dnssl` definition.

<span id="radv-dnssl-lifetime" class="code">lifetime \[mult\] *expr*</span>  
This option specifies the time how long the DNSSL information may be
    used by clients after the receipt of RA. Details are the same as for
    RDNSS `lifetime` option above. Default: 3 \* `max ra interval`.

### Attributes

RAdv defines two route attributes:

<span id="rta-ra-preference" class="code">enum ra_preference</span>  
The preference of the route. The value can be *RA_PREF_LOW*,
    *RA_PREF_MEDIUM* or *RA_PREF_HIGH*. If the attribute is not set,
    the [route preference](#radv-iface-route-preference)
    option is used.

<span id="rta-ra-lifetime" class="code">int ra_lifetime</span>  
The advertised lifetime of the route, in seconds. The special value of
    0xffffffff represents infinity. If the attribute is not set, the
    [route lifetime](#radv-iface-route-lifetime)
    option is used.

### Example


    ipv6 table radv_routes;         # Manually configured routes go here

    protocol static {
        ipv6 { table radv_routes; };

        route 2001:0DB8:4000::/48 unreachable;
        route 2001:0DB8:4010::/48 unreachable;

        route 2001:0DB8:4020::/48 unreachable {
            ra_preference = RA_PREF_HIGH;
            ra_lifetime = 3600;
        };
    }

    protocol radv {
        propagate routes yes;       # Propagate the routes from the radv_routes table
        ipv6 { table radv_routes; export all; };

        interface "eth2" {
            max ra interval 5;  # Fast failover with more routers
            managed yes;        # Using DHCPv6 on eth2
            prefix ::/0 {
                autonomous off; # So do not autoconfigure any IP
            };
        };

        interface "eth*";       # No need for any other options

        prefix 2001:0DB8:1234::/48 {
            preferred lifetime 0;   # Deprecated address range
        };

        prefix 2001:0DB8:2000::/48 {
            autonomous off;     # Do not autoconfigure
        };

        rdnss 2001:0DB8:1234::10;   # Short form of RDNSS

        rdnss {
            lifetime mult 10;
            ns 2001:0DB8:1234::11;
            ns 2001:0DB8:1234::12;
        };

        dnssl {
            lifetime 3600;
            domain "abc.com";
            domain "xyz.com";
        };
    }
