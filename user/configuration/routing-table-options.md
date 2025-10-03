## Routing table options

Most routing tables do not need any options and are defined without an option
block, but there are still some options to tweak routing table behavior. Note
that implicit tables (`master4` and `master6`) can be redefined in order
to set options.

<span id="table-debug" class="code">debug all\|off\|{ states\|routes\|filters \[, *...*\] }</span>  
Set table debugging options. Like in [protocol
debugging](#proto-debug), tables are capable of writing trace
    messages about its work to the log (with category `trace`).
    For now, this does nothing, but in version 3, it is used. Default: off.

<span id="rtable-sorted" class="code">sorted *switch*</span>  
Usually, a routing table just chooses the selected (best) route from a
    list of routes for each network, while keeping remaining routes unsorted.
    If enabled, these lists of routes are kept completely sorted (according
    to preference or some protocol-dependent metric).

This is needed for some protocol features (e.g. `secondary` option of
    BGP protocol, which allows to accept not just a selected route, but the
    first route (in the sorted list) that is accepted by filters), but it is
    incompatible with some other features (e.g. `deterministic med`
    option of BGP protocol, which activates a way of choosing selected route
    that cannot be described using comparison and ordering). Minor advantage
    is that routes are shown sorted in `show route`, minor disadvantage
    is that it is slightly more computationally expensive. Default: off.

<span id="rtable-trie" class="code">trie *switch*</span>  
BIRD routing tables are implemented with hash tables, which is efficient
    for exact-match lookups, but inconvenient for longest-match lookups or
    interval lookups (finding superprefix or subprefixes). This option
    activates additional trie structure that is used to accelerate these
    lookups, while using the hash table for exact-match lookups.

This has advantage for [RPKI](#rpki) (on ROA tables),
    for [recursive next-hops](#bgp-gateway) (on IGP tables),
    and is required for [flowspec validation](#bgp-validate)
    (on base IP tables). Another advantage is that interval results (like
    from `show route in ...` command) are lexicographically sorted. The
    disadvantage is that trie-enabled routing tables require more memory,
    which may be an issue especially in multi-table setups. Default: off.

<span id="rtable-min-settle-time" class="code">min settle time *time*</span>  
Specify a minimum value of the settle time. When a ROA table changes,
    automatic [RPKI reload](#proto-rpki-reload) may be
    triggered, after a short settle time. Minimum settle time is a delay
    from the last ROA table change to wait for more updates. Default: 1 s.

<span id="rtable-max-settle-time" class="code">max settle time *time*</span>  
Specify a maximum value of the settle time. When a ROA table changes,
    automatic [RPKI reload](#proto-rpki-reload) may be
    triggered, after a short settle time. Maximum settle time is an upper
    limit to the settle time from the initial ROA table change even if
    there are consecutive updates gradually renewing the settle time.
    Default: 20 s.

<span id="rtable-gc-threshold" class="code">gc threshold *number*</span>  
Specify a minimum amount of removed networks that triggers a garbage
    collection (GC) cycle. Default: 1000.

<span id="rtable-gc-period" class="code">gc period *time*</span>  
Specify a period of time between consecutive GC cycles. When there is a
    significant amount of route withdraws, GC cycles are executed repeatedly
    with given period time (with some random factor). When there is just
    small amount of changes, GC cycles are not executed. In extensive route
    server setups, running GC on hundreds of full BGP routing tables can
    take significant amount of time, therefore they should use higher GC
    periods. Default: adaptive, based on number of routing tables in the
    configuration. From 10 s (with \<= 25 routing tables) up to 600 s (with
    \>= 1500 routing tables).
