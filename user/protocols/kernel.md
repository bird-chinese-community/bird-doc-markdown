## Kernel

The Kernel protocol is not a real routing protocol. Instead of communicating
with other routers in the network, it performs synchronization of BIRD's routing
tables with the OS kernel. Basically, it sends all routing table updates to the
kernel and from time to time it scans the kernel tables to see whether some
routes have disappeared (for example due to unnoticed up/down transition of an
interface) or whether an \`alien' route has been added by someone else (depending
on the `learn` switch, such routes are either ignored or accepted to our
table).

Note that routes created by OS kernel itself, namely direct routes
representing IP subnets of associated interfaces, are imported only with
`learn all` enabled.

If your OS supports only a single routing table, you can configure only one
instance of the Kernel protocol. If it supports multiple tables (in order to
allow policy routing; such an OS is for example Linux), you can run as many
instances as you want, but each of them must be connected to a different BIRD
routing table and to a different kernel table.

Because the kernel protocol is partially integrated with the connected
routing table, there are two limitations - it is not possible to connect more
kernel protocols to the same routing table and changing route destination
(gateway) in an export filter of a kernel protocol does not work. Both
limitations can be overcome using another routing table and the pipe protocol.

The Kernel protocol supports both IPv4 and IPv6 channels; only one channel
can be configured in each protocol instance. On Linux, it also supports [IPv6
SADR](#ip-sadr-routes) and [MPLS](#mpls-routes)
channels.

### Configuration

<span id="krt-persist" class="code">persist *switch*</span>  
Tell BIRD to leave all its routes in the routing tables when it exits
    (instead of cleaning them up).

<span id="krt-scan-time" class="code">scan time *number*</span>  
Time in seconds between two consecutive scans of the kernel routing
    table.

<span id="krt-learn" class="code">learn *switch*\|all</span>  
Enable learning of routes added to the kernel routing tables by other
    routing daemons or by the system administrator. This is possible only on
    systems which support identification of route authorship. By default,
    routes created by kernel (marked as "proto kernel") are not imported.
    Use `learn all` option to import even these routes.

<span id="krt-kernel-table" class="code">kernel table *number*</span>  
Select which kernel table should this particular instance of the Kernel
    protocol work with. Available only on systems supporting multiple
    routing tables.

<span id="krt-metric" class="code">metric *number*</span>  
(Linux)
    Use specified value as a kernel metric (priority) for all routes sent to
    the kernel. When multiple routes for the same network are in the kernel
    routing table, the Linux kernel chooses one with lower metric. Also,
    routes with different metrics do not clash with each other, therefore
    using dedicated metric value is a reliable way to avoid overwriting
    routes from other sources (e.g. kernel device routes). Metric 0 has a
    special meaning of undefined metric, in which either OS default is used,
    or per-route metric can be set using `krt_metric` attribute. Default:
    32.

<span id="krt-graceful-restart" class="code">graceful restart *switch*</span>  
Participate in graceful restart recovery. If this option is enabled and
    a graceful restart recovery is active, the Kernel protocol will defer
    synchronization of routing tables until the end of the recovery. Note
    that import of kernel routes to BIRD is not affected.

<span id="krt-merge-paths" class="code">merge paths *switch* \[limit *number*\]</span>  
Usually, only best routes are exported to the kernel protocol. With path
    merging enabled, both best routes and equivalent non-best routes are
    merged during export to generate one ECMP (equal-cost multipath) route
    for each network. This is useful e.g. for BGP multipath. Note that best
    routes are still pivotal for route export (responsible for most
    properties of resulting ECMP routes), while exported non-best routes are
    responsible just for additional multipath next hops. This option also
    allows to specify a limit on maximal number of nexthops in one route. By
    default, multipath merging is disabled. If enabled, default value of the
    limit is 16.

<span id="krt-netlink-rx-buffer" class="code">netlink rx buffer *number*</span>  
(Linux)
    Set kernel receive buffer size (in bytes) for the netlink socket. The default
    value is OS-dependent (from the `/proc/sys/net/core/rmem_default`
    file), If you get some "Kernel dropped some netlink message ..." warnings,
    you may increase this value.

### Attributes

The Kernel protocol defines several attributes. These attributes are
translated to appropriate system (and OS-specific) route attributes. We support
these attributes:

<span id="rta-krt-source" class="code">int krt_source</span>  
The original source of the imported kernel route. The value is
    system-dependent. On Linux, it is a value of the protocol field of the
    route. See /etc/iproute2/rt\_protos for common values. On BSD, it is
    based on STATIC and PROTOx flags. The attribute is read-only.

<span id="rta-krt-metric" class="code">int krt_metric</span>  
(Linux)
    The kernel metric of the route. When multiple same routes are in a
    kernel routing table, the Linux kernel chooses one with lower metric.
    Note that preferred way to set kernel metric is to use protocol option
    `metric`, unless per-route metric values are needed.

<span id="rta-krt-prefsrc" class="code">ip krt_prefsrc</span>  
(Linux)
    The preferred source address. Used in source address selection for
    outgoing packets. Has to be one of the IP addresses of the router.

<span id="rta-krt-realm" class="code">int krt_realm</span>  
(Linux)
    The realm of the route. Can be used for traffic classification.

<span id="rta-krt-scope" class="code">int krt_scope</span>  
(Linux IPv4)
    The scope of the route. Valid values are 0-254, although Linux kernel
    may reject some values depending on route type and nexthop. It is
    supposed to represent \`indirectness' of the route, where nexthops of
    routes are resolved through routes with a higher scope, but in current
    kernels anything below *link* (253) is treated as *global* (0).
    When not present, global scope is implied for all routes except device
    routes, where link scope is used by default.

In Linux, there is also a plenty of obscure route attributes mostly focused
on tuning TCP performance of local connections. BIRD supports most of these
attributes, see Linux or iproute2 documentation for their meaning. Attributes
`krt_lock_*` and `krt_feature_*` have type bool, `krt_congctl` has type
string, others have type int. Supported attributes are:

`krt_mtu`, `krt_lock_mtu`, `krt_window`, `krt_lock_window`,
`krt_rtt`, `krt_lock_rtt`, `krt_rttvar`, `krt_lock_rttvar`,
`krt_ssthresh`, `krt_lock_ssthresh`, `krt_cwnd`, `krt_lock_cwnd`,
`krt_advmss`, `krt_lock_advmss`, `krt_reordering`, `krt_lock_reordering`,
`krt_hoplimit`, `krt_lock_hoplimit`, `krt_rto_min`, `krt_lock_rto_min`,
`krt_initcwnd`, `krt_lock_initcwnd`, `krt_initrwnd`, `krt_lock_initrwnd`,
`krt_quickack`, `krt_lock_quickack`, `krt_congctl`, `krt_lock_congctl`,
`krt_fastopen_no_cookie`, `krt_lock_fastopen_no_cookie`,
`krt_feature_ecn`, `krt_feature_allfrag`

### Example

A simple configuration can look this way:


    protocol kernel {
        export all;
    }

Or for a system with two routing tables:


    protocol kernel {       # Primary routing table
        learn;          # Learn alien routes from the kernel
        persist;        # Do not remove routes on bird shutdown
        scan time 10;       # Scan kernel routing table every 10 seconds
        ipv4 {
            import all;
            export all;
        };
    }

    protocol kernel {       # Secondary routing table
        kernel table 100;
        ipv4 {
            table auxtable;
            export all;
        };
    }
