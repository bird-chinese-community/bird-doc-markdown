## Protocol options

For each protocol instance, you can configure a bunch of options. Some of
them (those described in this section) are generic, some are specific to the
protocol (see sections talking about the protocols).

Several options use a *switch* argument. It can be either `on`,
`yes` or a numeric expression with a non-zero value for the option to be
enabled or `off`, `no` or a numeric expression evaluating to zero to
disable it. An empty *switch* is equivalent to `on` ("silence means
agreement").

<span id="proto-disabled" class="code">disabled *switch*</span>  
Disables the protocol. You can change the disable/enable status from the
    command line interface without needing to touch the configuration.
    Disabled protocols are not activated. Default: protocol is enabled.

<span id="proto-debug" class="code">debug all\|off\|{ states\|routes\|filters\|interfaces\|events\|packets \[, *...*\] }</span>  
Set protocol debugging options. If asked, each protocol is capable of
    writing trace messages about its work to the log (with category
    `trace`). You can either request printing of `all` trace messages
    or only of the selected types: `states` for protocol state changes
    (protocol going up, down, starting, stopping etc.), `routes` for
    routes exchanged with the routing table, `filters` for details on
    route filtering, `interfaces` for interface change events sent to
    the protocol, `events` for events internal to the protocol and
    `packets` for packets sent and received by the protocol. Classes
    `routes` and `filters` can be also set per-channel using
    [channel debugging option](#channel-debug)) Default: off.

<span id="proto-mrtdump" class="code">mrtdump all\|off\|{ states\|messages \[, *...*\] }</span>  
Set protocol MRTdump flags. MRTdump is a standard binary format for
    logging information from routing protocols and daemons. These flags
    control what kind of information is logged from the protocol to the
    MRTdump file (which must be specified by global `mrtdump` option, see
    the previous section). Although these flags are similar to flags of
    `debug` option, their meaning is different and protocol-specific. For
    BGP protocol, `states` logs BGP state changes and `messages` logs
    received BGP messages. Other protocols does not support MRTdump yet.

<span id="proto-router-id" class="code">router id *number* \| *IPv4 address*</span>  
This option can be used to override global router id for a given
    protocol. Default: uses global router id.

<span id="proto-hostname" class="code">hostname "*name*"</span>  
This option can be used to override global hostname for a given
    protocol. Default: uses global hostname.

<span id="proto-description" class="code">description "*text*"</span>  
This is an optional description of the protocol. It is displayed as a
    part of the output of 'show protocols all' command.

<span id="proto-vrf" class="code">vrf "*text*"\|default</span>  
Associate the protocol with specific VRF. The protocol will be
    restricted to interfaces assigned to the VRF and will use sockets bound
    to the VRF. A corresponding VRF interface must exist on OS level. For
    kernel protocol, an appropriate table still must be explicitly selected
    by `table` option.

By selecting `default`, the protocol is associated with the default
    VRF; i.e., it will be restricted to interfaces not assigned to any
    regular VRF. That is different from not specifying `vrf` at all, in
    which case the protocol may use any interface regardless of its VRF
    status.

Note that for proper VRF support it is necessary to use Linux kernel
    version at least 4.14, older versions have limited VRF implementation.
    Before Linux kernel 5.0, a socket bound to a port in default VRF collide
    with others in regular VRFs. In BGP, this can be avoided by using
    [strict bind](#bgp-strict-bind) option.

<span id="proto-channel" class="code">*channel name* \[{*channel config*}\]</span>  
Every channel must be explicitly stated. See the protocol-specific
    configuration for the list of supported channel names. See the
    [channel configuration section](#channel-opts) for channel
    definition.

There are several options that give sense only with certain protocols:

<span id="proto-iface" class="code">interface \[-\] \[ "*mask*" \] \[ *prefix* \] \[, *...*\] \[ { *option*; \[*...*\] } \]</span>  
Specifies a set of interfaces on which the protocol is activated with
    given interface-specific options. A set of interfaces specified by one
    interface option is described using an interface pattern. The interface
    pattern consists of a sequence of clauses (separated by commas), each
    clause is a mask specified as a shell-like pattern. Interfaces are
    matched by their name.

An interface matches the pattern if it matches any of its clauses. If
    the clause begins with `-`, matching interfaces are excluded. Patterns
    are processed left-to-right, thus `interface "eth0", -"eth*", "*";`
    means eth0 and all non-ethernets.

Some protocols (namely OSPFv2 and Direct) support extended clauses that
    may contain a mask, a prefix, or both of them. An interface matches such
    clause if its name matches the mask (if specified) and its address
    matches the prefix (if specified). Extended clauses are used when the
    protocol handles multiple addresses on an interface independently.

An interface option can be used more times with different interface-specific
    options, in that case for given interface the first matching interface
    option is used.

This option is allowed in Babel, BFD, Device, Direct, OSPF, RAdv and RIP
    protocols. In OSPF protocol it is used in the `area` subsection.

Default: none.

Examples:

`interface "*" { type broadcast; };` - start the protocol on all
    interfaces with `type broadcast` option.

`interface "eth1", "eth4", "eth5" { type ptp; };` - start the
    protocol on enumerated interfaces with `type ptp` option.

`interface -192.168.1.0/24, 192.168.0.0/16;` - start the protocol
    on all interfaces that have address from 192.168.0.0/16, but not from
    192.168.1.0/24.

`interface "eth*" 192.168.1.0/24;` - start the protocol on all
    ethernet interfaces that have address from 192.168.1.0/24.

<span id="proto-tx-class" class="code">tx class\|dscp *number*</span>  
This option specifies the value of ToS/DS/Class field in IP headers of
    the outgoing protocol packets. This may affect how the protocol packets
    are processed by the network relative to the other network traffic. With
    `class` keyword, the value (0-255) is used for the whole ToS/Class
    octet (but two bits reserved for ECN are ignored). With `dscp`
    keyword, the value (0-63) is used just for the DS field in the octet.
    Default value is 0xc0 (DSCP 0x30 - CS6).

<span id="proto-tx-priority" class="code">tx priority *number*</span>  
This option specifies the local packet priority. This may affect how the
    protocol packets are processed in the local TX queues. This option is
    Linux specific. Default value is 7 (highest priority, privileged traffic).

<span id="proto-pass" class="code">password "*password*" \| *bytestring* \[ { *password options* } \]</span>  
Specifies a password that can be used by the protocol as a shared secret
    key. Password option can be used more times to specify more passwords.
    If more passwords are specified, it is a protocol-dependent decision
    which one is really used. Specifying passwords does not mean that
    authentication is enabled, authentication can be enabled by separate,
    protocol-dependent `authentication` option.

A password can be specified as a string or as a sequence of hexadecimal
    digit pairs ([bytestring](#type-bytestring)).

This option is allowed in BFD, OSPF, RIP, and Babel protocols. BGP has
    also `password` option, but it is slightly different and described
    separately. Default: none.

Password option can contain section with some (not necessary all) password sub-options:

<span id="proto-pass-id" class="code">id *number*</span>  
ID of the password, (0-255). If it is not specified, BIRD will choose ID
    based on an order of the password item in the interface, starting from
    1. For example, second password item in one interface will have default
    ID 2. ID 0 is allowed by BIRD, but some other implementations may not
    allow it. ID is used by some routing protocols to identify which
    password was used to authenticate protocol packets.

<span id="proto-pass-gen-from" class="code">generate from "*time*"</span>  
The start time of the usage of the password for packet signing.
    The format of *time* is `YYYY-MM-DD [hh:mm:ss[.sss]]`.

<span id="proto-pass-gen-to" class="code">generate to "*time*"</span>  
The last time of the usage of the password for packet signing.

<span id="proto-pass-accept-from" class="code">accept from "*time*"</span>  
The start time of the usage of the password for packet verification.

<span id="proto-pass-accept-to" class="code">accept to "*time*"</span>  
The last time of the usage of the password for packet verification.

<span id="proto-pass-from" class="code">from "*time*"</span>  
Shorthand for setting both `generate from` and `accept from`.

<span id="proto-pass-to" class="code">to "*time*"</span>  
Shorthand for setting both `generate to` and `accept to`.

<span id="proto-pass-algorithm" class="code">algorithm ( keyed md5 \| keyed sha1 \| hmac sha1 \| hmac sha256 \| hmac sha384 \| hmac sha512 \| blake2s128 \| blake2s256 \| blake2b256 \| blake2b512 )</span>  
The message authentication algorithm for the password when cryptographic
    authentication is enabled. The default value depends on the protocol.
    For RIP and OSPFv2 it is Keyed-MD5 (for compatibility), for OSPFv3 and
    Babel it is HMAC-SHA-256.
