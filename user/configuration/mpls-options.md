## MPLS options

The MPLS domain definition is mandatory for a MPLS router. All MPLS channels
and MPLS-aware protocols are associated with some MPLS domain (although usually
implicitly with the sole one). In the MPLS domain definition you can configure
details of MPLS label allocation. Currently, there is just one option,
`label range`.

Note that the MPLS subsystem is experimental, it is likely that there will be
some backward-incompatible changes in the future.

<span id="mpls-domain-label-range" class="code">label range *name* { start *number*; length *number*; \[*...*\] }</span>  
Define a new label range, or redefine implicit label ranges `static`
    and `dynamic`. MPLS channels use configured label ranges for dynamic
    label allocation, while `static` label range is used for static label
    allocation. The label range definition must specify the extent of the
    range. By default, the range `static` is 16-1000, while the range
    `dynamic` is 1000-10000.

MPLS channel should be defined in each MPLS-aware protocol in addition to its
regular channels. It is responsible for label allocation and for announcing MPLS
routes to the MPLS routing table. Besides common [channel
options](#channel-opts), MPLS channels have some specific options:

<span id="mpls-channel-domain" class="code">domain *name*</span>  
Specify a MPLS domain to which this channel and protocol belongs.
    Default: The first defined MPLS domain.

<span id="mpls-channel-label-range" class="code">label range *name*</span>  
Use specific label range for dynamic label allocation. Note that static
    labels always use the range `static`. Default: the range `dynamic`.

<span id="mpls-channel-label-policy" class="code">label policy static\|prefix\|aggregate\|vrf</span>  
Label policy specifies how routes are grouped to forwarding equivalence
    classes (FECs) and how labels are assigned to them.

The policy `static` means no dynamic label allocation is done, and
    static labels must be set in import filters using the route attribute
    [mpls_label](#rta-mpls-label).

The policy `prefix` means each prefix uses separate label associated
    with that prefix. When a labeled route is updated, it keeps the label.
    This policy is appropriate for IGPs.

The policy `aggregate` means routes are grouped to FECs according to
    their next hops (including next hop labels), and one label is used for
    all routes in the same FEC. When a labeled route is updated, it may
    change next hop, change FEC and therefore change label. This policy is
    appropriate for BGP.

The policy `vrf` is only valid in L3VPN protocols. It uses one label
    for all routes from a VRF, while replacing the original next hop with
    lookup in the VRF.

Default: `prefix`.

This is a trivial example of MPLS setup:


    mpls domain mdom {
        label range bgprange { start 2000; length 1000; };
    }

    mpls table mtab;

    protocol static {
        ipv6;
        mpls;

        route 2001:db8:1:1/64 mpls 100 via 2001:db8:1:2::1/64 mpls 200;
    }

    protocol bgp {
        # regular channels
        ipv6 mpls { ... };
        vpn6 mpls { ... };

        # MPLS channel
        mpls {
            # domain mdom;
            # table mtab;
            label range bgprange;
            label policy aggregate;
        };

        ...
    }
