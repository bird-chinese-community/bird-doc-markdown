## MPLS

Multiprotocol Label Switching (MPLS) is a networking technology which works
below IP routing but above the link (e.g. ethernet) layer. It is described in
<a href="https://datatracker.ietf.org/doc/rfc3031" class="rfc">RFC
3031</a>.

In regular IP forwarding, the destination address of a packet is independently
examined in each hop, a route with longest prefix match is selected from the
routing table, and packet is processed accordingly. In general, there is no
difference between border routers and internal routers w.r.t. IP forwarding.

In MPLS forwarding, when a packet enters the network, it is classified (based on
destination address, ingress interface and other factors) into one of forwarding
equivalence classes (FECs), then a header with a MPLS label identifying the FEC
is attached to it, and the packet is forwarded. In internal routers, only the
MPLS label is examined, the matching MPLS route is selected from the MPLS
routing table, and the packet is processed accordingly. The specific value of
MPLS label has local meaning only and may change between hops (that is why it is
called label switching). When the packet leaves the network, the MPLS header is
removed.

The advantage of the MPLS approach is that other factors than the destination
address can be considered and used consistently in the whole network, for
example IP traffic with multiple overlapping private address ranges could be
mixed together, or particular paths for specific flows could be defined. Another
advantage is that MPLS forwarding by internal routers can be much simpler than
IP forwarding, as instead of the longest prefix match algorithm it uses simpler
exact match for MPLS route selection. The disadvantage is additional complexity
in signaling. For further details, see <a href="https://datatracker.ietf.org/doc/rfc3031" class="rfc">RFC
3031</a>.

MPLS-aware routing protocols not only distribute IP routing information, but
they also distribute labels. Therefore, they produce labeled routes - routes
representing label switched paths (LSPs) through the MPLS domain. Such routes
have IP prefix and next hop address like regular (non-labeled) routes, but they
also have local MPLS label (in route attribute [mpls_label](#rta-mpls-label)) and outgoing MPLS label (as a part of the next hop). They
are stored in regular IP routing tables.

Labeled routes are used for exchange of routing information between routing
protocols and for ingress (IP -\> MPLS) forwarding, but they are not directly
used for MPLS forwarding. For that purpose [MPLS
routes](#mpls-routes) are used. These are routes that have local MPLS label as a primary key
and they are stored in the MPLS routing table.

In BIRD, the whole process generally works this way: A MPLS-aware routing
protocol (say BGP) receives routing information including remote label. It
produces a route with attribute [mpls_policy](#rta-mpls-policy)
specifying desired [MPLS label policy](#mpls-channel-label-policy).
Such route then passes the import filter (which could modify the MPLS label
policy or perhaps assign a static label) and when it is accepted, a local MPLS
label is selected (according to the label policy) and attached to the route,
producing labeled route. When a new MPLS label is allocated, the MPLS-aware
protocol automatically produces corresponding MPLS route. When all labeled
routes that use specific local MPLS label are retracted, the corresponding MPLS
route is retracted too.

There are three important concepts for MPLS in BIRD: MPLS domains, MPLS tables
and MPLS channels. MPLS domain represents an independent label space, all
MPLS-aware protocols are associated with some MPLS domain. It is responsible for
label management, handling label allocation requests from MPLS-aware protocols.
MPLS table is just a routing table for MPLS routes. Routers usually have one
MPLS domain and one MPLS table, with Kernel protocol to export MPLS routes into
kernel FIB.

MPLS channels make protocols MPLS-aware, they are responsible for keeping track
of active FECs (and corresponding allocated labels), selecting FECs / local
labels for labeled routes, and maintaining correspondence between labeled routes
and MPLS routes.

Note that local labels are allocated to individual MPLS-aware protocols and
therefore it is not possible to share local labels between different protocols.
