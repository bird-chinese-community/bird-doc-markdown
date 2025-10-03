## L3VPN

### Introduction

The L3VPN protocol serves as a translator between IP routes and VPN
routes. It is a component for BGP/MPLS IP VPNs (<a href="https://datatracker.ietf.org/doc/rfc4364" class="rfc">RFC
4364</a>) and implements
policies defined there. In import direction (VPN -\> IP), VPN routes matching
import target specification are stripped of route distinguisher and MPLS labels
and announced as IP routes, In export direction (IP -\> VPN), IP routes are
expanded with specific route distinguisher, export target communities and MPLS
label and announced as labeled VPN routes. Unlike the Pipe protocol, the L3VPN
protocol propagates just the best route for each network.

In BGP/MPLS IP VPNs, route distribution is controlled by Route Targets (RT).
VRFs are associated with one or more RTs. Routes are also associated with one or
more RTs, which are encoded as route target extended communities
in [bgp_ext_community](#rta-bgp-ext-community). A route is then
imported into each VRF that shares an associated Route Target. The L3VPN
protocol implements this mechanism through mandatory `import target` and
`export target` protocol options.

### Configuration

L3VPN configuration consists of a few mandatory options and multiple channel
definitions. For convenience, the default export filter in L3VPN channels is
`all`, as the primary way to control import and export of routes is through
protocol options `import target` and `export target`. If custom filters
are used, note that the export filter of the input channel is applied before
the route translation, while the import filter of the output channel is applied
after that.

In contrast to the Pipe protocol, the L3VPN protocol can handle both IPv4 and
IPv6 routes in one instance, also both IP side and VPN side are represented as
separate channels, although that may change in the future. The L3VPN is always
MPLS-aware protocol, therefore a MPLS channel is mandatory. Altogether, L3VPN
could have up to 5 channels: `ipv4`, `ipv6`, `vpn4`, `vpn6`, and
`mpls`.

<span id="l3vpn-route-distinguisher" class="code">route distinguisher *rd*</span>  
The route distinguisher that is attached to routes in the export
    direction. Mandatory.

<span id="l3vpn-rd" class="code">rd *rd*</span>  
A shorthand for the option `route distinguisher`.

<span id="l3vpn-import-target" class="code">import target *ec*\|*ec-set*\|none\|all</span>  
Route target extended communities specifying which routes should be
    imported. Either one community or a set. A route is imported if there is
    non-empty intersection between extended communities of the route and the
    import target of the L3VPN protocol. Mandatory.

<span id="l3vpn-export-target" class="code">export target *ec*\|*ec-set*\|none</span>  
Route target extended communities that are attached to the route in the
    export direction. Either one community or a set. Other route target
    extended communities are removed. Mandatory.

<span id="l3vpn-route-target" class="code">route target *ec*\|*ec-set*\|none</span>  
A shorthand for both `import target` and `export target`.

### Attributes

The L3VPN protocol does not define any route attributes.

### Example

Here is an example of L3VPN setup with one VPN and BGP uplink. IP routes
learned from a customer in the VPN are stored in `vrf0vX` tables, which are
mapped to kernel VRF vrf0. Routes can also be exchanged through BGP with
different sites hosting that VPN. Forwarding of VPN traffic through the network
is handled by MPLS.

Omitted from the example are some routing protocol to exchange routes with
the customer and some sort of MPLS-aware IGP to resolve next hops for BGP VPN
routes.


    # MPLS basics
    mpls domain mdom;
    mpls table  mtab;

    protocol kernel krt_mpls {
        mpls { table mtab; export all; };
    }

    vpn4 table vpntab4;
    vpn6 table vpntab6;

    # Exchange VPN routes through BGP
    protocol bgp {
        vpn4 { table vpntab4; import all; export all; };
        vpn6 { table vpntab6; import all; export all; };
        mpls { label policy aggregate; };
        local 10.0.0.1 as 10;
        neighbor 10.0.0.2 as 10;
    }

    # VRF 0
    ipv4 table vrf0v4;
    ipv6 table vrf0v6;

    protocol kernel kernel0v4 {
        vrf "vrf0";
        ipv4 { table vrf0v4; export all; };
        kernel table 100;
    }

    protocol kernel kernel0v6 {
        vrf "vrf0";
        ipv6 { table vrf0v6; export all; };
        kernel table 100;
    }

    protocol l3vpn l3vpn0 {
        vrf "vrf0";
        ipv4 { table vrf0v4; };
        ipv6 { table vrf0v6; };
        vpn4 { table vpntab4; };
        vpn6 { table vpntab6; };
        mpls { label policy vrf; };

        rd 10:12;
        import target [(rt, 10, 32..40)];
        export target [(rt, 10, 30), (rt, 10, 31)];
    }
