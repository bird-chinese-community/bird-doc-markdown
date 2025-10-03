## Route attributes

A filter is implicitly passed a route, and it can access its attributes just
like it accesses variables. There are common route attributes, protocol-specific
route attributes and custom route attributes. Most common attributes are
mandatory (always defined), while remaining are optional.  Attempts to access
undefined attribute result in a runtime error; you can check if an attribute is
defined by using the `defined( `*attribute*` )` operator. One notable
exception to this rule are attributes of bgppath and \*clist types, where
undefined value is regarded as empty bgppath/\*clist for most purposes.

Attributes can be defined by just setting them in filters. Custom attributes
have to be first declared by [attribute](#opt-attribute) global
option. You can also undefine optional attribute back to non-existence by using
the `unset( `*attribute*` )` operator.

Common route attributes are:

<span id="rta-net" class="code">*prefix* net</span>  
The network prefix or anything else the route is talking about. The
    primary key of the routing table. Read-only. (See the [chapter about
routes](#routes).)

<span id="rta-preference" class="code">*int* preference</span>  
Preference of the route.

<span id="rta-from" class="code">*ip* from</span>  
The router which the route has originated from.

<span id="rta-gw" class="code">*ip* gw</span>  
Next hop packets routed using this route should be forwarded to.

<span id="rta-proto" class="code">*string* proto</span>  
The name of the protocol which the route has been imported from.
    Read-only.

<span id="rta-source" class="code">*enum* source</span>  
what protocol has told me about this route. Possible values:
    `RTS_STATIC`, `RTS_INHERIT`, `RTS_DEVICE`,
    `RTS_RIP`, `RTS_OSPF`, `RTS_OSPF_IA`, `RTS_OSPF_EXT1`,
    `RTS_OSPF_EXT2`, `RTS_BGP`, `RTS_PIPE`, `RTS_BABEL`.

<span id="rta-dest" class="code">*enum* dest</span>  
Type of destination the packets should be sent to
    (`RTD_ROUTER` for forwarding to a neighboring router,
    `RTD_DEVICE` for routing to a directly-connected network,
    `RTD_MULTIPATH` for multipath destinations,
    `RTD_BLACKHOLE` for packets to be silently discarded,
    `RTD_UNREACHABLE`, `RTD_PROHIBIT` for packets that should be
    returned with ICMP host unreachable / ICMP administratively prohibited
    messages). Can be changed, but only to `RTD_BLACKHOLE`,
    `RTD_UNREACHABLE` or `RTD_PROHIBIT`.

<span id="rta-ifname" class="code">*string* ifname</span>  
Name of the outgoing interface. Sink routes (like blackhole, unreachable
    or prohibit) and multipath routes have no interface associated with
    them, so `ifname` returns an empty string for such routes. Setting it
    would also change route to a direct one (remove gateway).

<span id="rta-ifindex" class="code">*int* ifindex</span>  
Index of the outgoing interface. System wide index of the interface. May
    be used for interface matching, however indexes might change on interface
    creation/removal. Zero is returned for routes with undefined outgoing
    interfaces. Read-only.

<span id="rta-weight" class="code">*int* weight</span>  
Multipath weight of route next hops. Valid values are 1-256. Reading
    returns the weight of the first next hop, setting it sets weights of all
    next hops to the specified value. Therefore, this attribute is not much
    useful for manipulating individual next hops of an ECMP route, but can
    be used in BGP multipath setup to set weights of individual routes that
    are merged to one ECMP route during export to the Kernel protocol
    (with active [marge paths](#krt-merge-paths) option).

<span id="rta-gw-mpls" class="code">*int* gw_mpls</span>  
Outgoing MPLS label attached to route (i.e., incoming MPLS label on the
    next hop router for this label-switched path). Reading returns the label
    value and setting it sets it to the start of the label stack. Setting
    implicit-NULL label (3) disables the MPLS label stack. Only the first
    next hop and only one label in the label stack supported right now. This
    is experimental option, will be likely changed in the future to handle
    full MPLS label stack.

<span id="rta-igp-metric" class="code">*int* igp_metric</span>  
The optional attribute that can be used to specify a distance to the
    network for routes that do not have a native protocol metric attribute
    (like `ospf_metric1` for OSPF routes). It is used mainly by BGP to
    compare internal distances to boundary routers (see below).

<span id="rta-mpls-label" class="code">*int* mpls_label</span>  
Local MPLS label attached to the route. This attribute is produced by
    MPLS-aware protocols for labeled routes. It can also be set in import
    filters to assign static labels, but that also requires static MPLS
    label policy.

<span id="rta-mpls-policy" class="code">*enum* mpls_policy</span>  
For MPLS-aware protocols, this attribute defines which
    [MPLS label policy](#mpls-channel-label-policy) will be
    used for the route. It can be set in import filters to change it on
    per-route basis. Valid values are `MPLS_POLICY_NONE` (no label),
    `MPLS_POLICY_STATIC` (static label), `MPLS_POLICY_PREFIX`
    (per-prefix label), `MPLS_POLICY_AGGREGATE` (aggregated label),
    and `MPLS_POLICY_VRF` (per-VRF label). See [MPLS label
policy](#mpls-channel-label-policy) for details.

<span id="rta-mpls-class" class="code">*int* mpls_class</span>  
When [MPLS label policy](#mpls-channel-label-policy) is
    set to `aggregate`, it may be useful to apply more fine-grained
    aggregation than just one based on next hops. When routes have different
    value of this attribute, they will not be aggregated under one local
    label even if they have the same next hops.

Protocol-specific route attributes are described in the corresponding
protocol sections.
