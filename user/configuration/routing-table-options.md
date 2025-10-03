## Routing table options

Most routing tables do not need any options and are defined without an option
block, but there are still some options to tweak routing table behavior. Note
that implicit tables (`master4` and `master6`) can be redefined in order
to set options.

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

<span id="rtable-cork-threshold" class="code">cork threshold *number* *number*</span>  
Too many pending exports may lead to memory bloating. In such cases,
        BIRD tries to relieve the memory pressure by pausing some routines until
        the queue sizes get low enough. This option allows the user to set the
        thresholds; first value is the low threshold (when to resume), the
        second one is the high threshold (when to pause). The higher is the
        threshold, the more memory can get used. In most cases, the defaults
    should work for you but if you experience memory bloating on import
    surges, this knob is the first to turn down. Default: 333300 1011010.

<span id="rtable-export-settle-time" class="code">export settle time *time* *time*</span>  
Minimum and maximum settle times, respectively, for export announcements.
    When multiple routes are changing, this mechanism waits for the changes
    to settle before waking up sleeping export threads but if the changes are coming
    steadily, BIRD isn't waiting forever; at most the maximum time.
    Default values: `1 ms 100 ms`. You have to always provide both values.

<span id="rtable-route-refresh-export-settle-time" class="code">route refresh export settle time *time* *time*</span>  
Minimum and maximum settle times, respectively, for export announcements
    (the same as above), valid when any channel is currently doing a route refresh.
    This serves a purpose of even more aggresive change bundling, knowing that there
    is some active process generating changes in a fast pace. If you don't want
    this feature, set this to the same values as [export settle
time](#rtable-export-settle-time).
    Default values: `100 ms 3 s`.

<span id="rtable-digest-settle-time" class="code">digest settle time *time* *time*</span>  
Minimum and maximum settle times, respectively, for table change digests.
    This settle time applies to ROA table changes where a trie is generated
    containing all changed ROAs to automatically reload depending channels.
    Default values: `1 s 20 s`.

<span id="rtable-debug" class="code">debug all\|off\|{ states\|routes\|events \[, *...*\] }</span>  
Set table debugging options. Each table can write some trace messages
    into log with category `trace`. You can request `all` trace messages
    or select some types: `states` for table state changes and auxiliary
    processes, `routes` for auxiliary route notifications (next hop update,
        flowspec revalidation) and `events` for more detailed auxiliary routine
    debug. See also [channel debugging option](#channel-debug).
    Default: off.

<span id="proto-thread-group" class="code">thread group *name*</span>  
Assign this table's maintenance tasks to this thread group.
    Default: `worker`.
