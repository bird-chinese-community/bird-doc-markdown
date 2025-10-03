## Routes and network types

BIRD works with several types of routes. Some of them are typical IP routes,
others are better described as forwarding rules. We call them all routes,
regardless of this difference.

Every route consists of several attributes (read more about them in the
[Route attributes](#route-attributes) section); the common for all
routes are:

- IP address of router which told us about this route
- Source protocol instance
- Route preference
- Optional attributes defined by protocols

Other attributes depend on nettypes. Some of them are part of the primary key, these are marked (PK).

### IPv4 and IPv6 routes

The traditional routes. Configuration keywords are `ipv4` and `ipv6`.

- (PK) Route destination (IP prefix together with its length)
- Route next hops (see below)

### IPv6 source-specific routes

The IPv6 routes containing both destination and source prefix. They are used
for source-specific routing (SSR), also called source-address dependent routing
(SADR), see <a href="https://datatracker.ietf.org/doc/rfc8043" class="rfc">RFC
8043</a>. Currently limited mostly to the Babel protocol.
Configuration keyword is `ipv6 sadr`.

- (PK) Route destination (IP prefix together with its length)
- (PK) Route source (IP prefix together with its length)
- Route next hops (see below)

### VPN IPv4 and IPv6 routes

Routes for IPv4 and IPv6 with VPN Route Distinguisher (<a href="https://datatracker.ietf.org/doc/rfc4364" class="rfc">RFC
4364</a>).
Configuration keywords are `vpn4` and `vpn6`.

- (PK) Route destination (IP prefix together with its length)
- (PK) Route distinguisher (according to 
  <a href="https://datatracker.ietf.org/doc/rfc4364" class="rfc">RFC
  4364</a>
  )
- Route next hops

### Route Origin Authorization for IPv4 and IPv6

These entries can be used to validate route origination of BGP routes.
A ROA entry specifies prefixes which could be originated by an AS number.
Their keywords are `roa4` and `roa6`.

- (PK) IP prefix together with its length
- (PK) Matching prefix maximal length
- (PK) AS number

### Flowspec for IPv4 and IPv6

Flowspec rules are a form of firewall and traffic flow control rules
distributed mostly via BGP. These rules may help the operators stop various
network attacks in the beginning before eating up the whole bandwidth.
Configuration keywords are `flow4` and `flow6`.

- (PK) IP prefix together with its length
- (PK) Flow definition data
- Flow action (encoded internally as BGP communities according to 
  <a href="https://datatracker.ietf.org/doc/rfc8955" class="rfc">RFC
  8955</a>
  )

### MPLS switching rules

MPLS routes control MPLS forwarding in the same way as IP routes control IP
forwarding. MPLS-aware routing protocols produce both labeled IP routes and
corresponding MPLS routes. Configuration keyword is `mpls`.

- (PK) MPLS label
- Route next hops

### Route next hops

This is not a nettype. The route next hop is a complex attribute common for many
nettypes as you can see before. Every next hop has its assigned device
(either assumed from its IP address or set explicitly). It may have also
an IP address and an MPLS stack (one or both independently).
Maximal MPLS stack depth is set (in compile time) to 8 labels.

Every route (when eligible to have a next hop) can have more than one next hop.
In that case, every next hop has also its weight.
