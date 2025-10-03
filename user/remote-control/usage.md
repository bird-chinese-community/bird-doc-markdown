## Usage

Here is a brief list of supported functions.

Note: Many commands have the *name* of the protocol instance as an argument.
This argument can be omitted if there exists only a single instance.

<span id="cli-show-status" class="code">show status</span>  
Show router status, that is BIRD version, uptime and time from last
    reconfiguration.

<span id="cli-show-interfaces" class="code">show interfaces \[summary\]</span>  
Show the list of interfaces. For each interface, print its type, state,
    MTU and addresses assigned.

<span id="cli-show-protocols" class="code">show protocols \[all\]</span>  
Show list of protocol instances along with tables they are connected to
    and protocol status, possibly giving verbose information, if `all` is
    specified.

<span id="cli-reload-bgp" class="code">reload bgp \[in\|out\] \[*name*\]</span>  
Manually request (in) or send (out) route refresh (or both) on the
        given BGP protocol(s).

<span id="cli-show-ospf-iface" class="code">show ospf interface \[*name*\] \["*interface*"\]</span>  
Show detailed information about OSPF interfaces.

<span id="cli-show-ospf-neighbors" class="code">show ospf neighbors \[*name*\] \["*interface*"\]</span>  
Show a list of OSPF neighbors and a state of adjacency to them.

<span id="cli-show-ospf-state" class="code">show ospf state \[all\] \[*name*\]</span>  
Show detailed information about OSPF areas based on a content of the
    link-state database. It shows network topology, stub networks,
    aggregated networks and routers from other areas and external routes.
    The command shows information about reachable network nodes, use option
    `all` to show information about all network nodes in the link-state
    database.

<span id="cli-show-ospf-topology" class="code">show ospf topology \[all\] \[*name*\]</span>  
Show a topology of OSPF areas based on a content of the link-state
    database. It is just a stripped-down version of 'show ospf state'.

<span id="cli-show-ospf-lsadb" class="code">show ospf lsadb \[global \| area *id* \| link\] \[type *number*\] \[lsid *id*\] \[self \| router *id*\] \[*name*\]</span>  
Show contents of an OSPF LSA database. Options could be used to filter
    entries.

<span id="cli-show-rip-interfaces" class="code">show rip interfaces \[*name*\] \["*interface*"\]</span>  
Show detailed information about RIP interfaces.

<span id="cli-show-rip-neighbors" class="code">show rip neighbors \[*name*\] \["*interface*"\]</span>  
Show a list of RIP neighbors and associated state.

<span id="cli-show-static" class="code">show static \[*name*\]</span>  
Show detailed information about static routes.

<span id="cli-show-bfd-sessions" class="code">show bfd sessions \[*name*\] \[address (*IP*\|*prefix*)\] \[(interface\|dev) "*name*"\] \[ipv4\|ipv6\] \[direct\|multihop\] \[all\]</span>  
Show information about BFD sessions. Options could be used to filter
    entries, or in the case of the option `all` to give verbose output.

<span id="cli-show-symbols" class="code">show symbols \[table\|filter\|function\|protocol\|template\|roa\|*symbol*\]</span>  
Show the list of symbols defined in the configuration (names of
    protocols, routing tables etc.).

<span id="cli-show-route" class="code">show route \[\[(for\|in)\] *prefix*\|for *IP*\] \[table (*t*\|all)\] \[(import\|export) table *p*.*c*\] \[filter *f*\|where *cond*\] \[(export\|preexport\|noexport) *p*\] \[protocol *p*\] \[(stats\|count)\] \[*options*\]</span>  
Show contents of specified routing tables, that is routes, their metrics
    and (in case the `all` switch is given) all their attributes.

You can specify a *prefix* if you want to print routes for a
    specific network. If you use `for `*prefix or IP*, you'll get
    the entry which will be used for forwarding of packets to the given
    destination. Finally, if you use `in `*prefix*, you get all
    prefixes covered by the given prefix.
    By default, all routes for each network are printed with
    the selected one at the top, unless `primary` is given in which case
    only the selected route is shown.

The `show route` command can process one or multiple routing
    tables. The set of selected tables is determined on three levels: First,
    tables can be explicitly selected by `table` switch, which could be
    used multiple times, all tables are specified by `table all`. Second,
    tables can be implicitly selected by channels or protocols that are
    arguments of several other switches (e.g., `export`, `protocol`).
    Last, the set of default tables is used: `master4`, `master6` and
    each first table of any other network type.

There are internal tables when `(import|export) table` options
    are used for some channels. They can be selected explicitly with
    `(import|export) table` switch, specifying protocol *p* and
    channel name *c*.

You can also ask for printing only routes processed and accepted by
    a given filter (`filter `*name* or `filter { `*filter*` }` or matching a given condition (`where `*condition*).

The `export`, `preexport` and `noexport` switches ask for
    printing of routes that are exported to the specified protocol or
    channel. With `preexport`, the export filter of the channel is
    skipped. With `noexport`, routes rejected by the export filter are
    printed instead. Note that routes not exported for other reasons
    (e.g. secondary routes or routes imported from that protocol) are not
    printed even with `noexport`. These switches also imply that
    associated routing tables are selected instead of default ones.

You can also select just routes added by a specific protocol.
    `protocol `*p*. This switch also implies that associated
    routing tables are selected instead of default ones.

If BIRD is configured to keep filtered routes (see `import keep
    filtered` option), you can show them instead of routes by using
    `filtered` switch.

The `stats` switch requests showing of route statistics (the
    number of networks, number of routes before and after filtering). If
    you use `count` instead, only the statistics will be printed.

<span id="cli-mrt-dump" class="code">mrt dump table *name*\|"*pattern*" to "*filename*" \[filter *f*\|where *c*\]</span>  
Dump content of a routing table to a specified file in MRT table dump
    format. See [MRT protocol](#mrt) for details.

<span id="cli-configure" class="code">configure \[soft\] \["*config file*"\] \[timeout \[*number*\]\]</span>  
Reload configuration from a given file. BIRD will smoothly switch itself
    to the new configuration, protocols are reconfigured if possible,
    restarted otherwise. Changes in filters usually lead to restart of
    affected protocols.

The previous configuration is saved and the user can switch back to it
    with [configure undo](#cli-configure-undo) command. The
    old saved configuration is released (even if the reconfiguration attempt
    fails due to e.g. a syntax error).

If `soft` option is used, changes in filters does not cause BIRD to
    restart affected protocols, therefore already accepted routes (according
    to old filters) would be still propagated, but new routes would be
    processed according to the new filters.

If `timeout` option is used, config timer is activated. The new
    configuration could be either confirmed using `configure confirm`
    command, or it will be reverted to the old one when the config timer
    expires. This is useful for cases when reconfiguration breaks current
    routing and a router becomes inaccessible for an administrator. The
    config timeout expiration is equivalent to `configure undo`
    command. The timeout duration could be specified, default is 300 s.

<span id="cli-configure-confirm" class="code">configure confirm</span>  
Deactivate the config undo timer and therefore confirm the current
    configuration.

<span id="cli-configure-undo" class="code">configure undo</span>  
Undo the last configuration change and smoothly switch back to the
    previous (stored) configuration. If the last configuration change was
    soft, the undo change is also soft. There is only one level of undo, but
    in some specific cases when several reconfiguration requests are given
    immediately in a row and the intermediate ones are skipped then the undo
    also skips them back.

<span id="cli-configure-check" class="code">configure check \["*config file*"\]</span>  
Read and parse given config file, but do not use it. useful for checking
    syntactic and some semantic validity of an config file.

<span id="cli-enable-disable-restart" class="code">enable\|disable\|restart *name*\|"*pattern*"\|all</span>  
Enable, disable or restart a given protocol instance, instances matching
    the *pattern* or `all` instances.

<span id="cli-reload" class="code">reload filters \[in\|out\] (*name*\|"*pattern*"\|all) \[partial prefix\]</span>  
Reload a given protocol instance, that means re-import routes from the
    protocol instance and re-export preferred routes to the instance. If
    `in` or `out` options are used, the command is restricted to one
    direction (re-import or re-export).

This command is useful if appropriate filters have changed but the
    protocol instance was not restarted (or reloaded), therefore it still
    propagates the old set of routes. For example when `configure soft`
    command was used to change filters.

If `partial prefix` option is used, only corresponding routes are reloaded.
    Protocol BGP does partial reload only if it has import table enabled, otherwise partial reload for BGP is refused.

Re-export always succeeds, but re-import is protocol-dependent and might
    fail (for example, if BGP neighbor does not support route-refresh
    extension). In that case, re-export is also skipped. Note that for the
    pipe protocol, both directions are always reloaded together (`in` or
    `out` options are ignored in that case).

<span id="cli-timeformat" class="code">timeformat "*format1*" \[*limit* "*format2*"\]</span>  
Override format of date/time used by BIRD in this CLI session.

Meaning of "*format1*", *limit*, and "*format2*" is the same as in the
    [timeformat](#opt-timeformat) configuration option. Also, the
    same `iso ...` shorthands may be used.

<span id="cli-down" class="code">down</span>  
Shut BIRD down.

<span id="cli-graceful-restart" class="code">graceful restart</span>  
Shut BIRD down for graceful restart. See [graceful
restart](#graceful-restart) section for details.

<span id="cli-debug" class="code">debug *protocol*\|*pattern*\|all all\|off\|{ states\|routes\|filters\|events\|packets \[, *...*\] }</span>  
Control protocol debugging.

<span id="cli-dump" class="code">dump resources\|sockets\|ao keys\|events\|interfaces\|neighbors\|attributes\|routes\|protocols "*file*"</span>  
Creates the given file (it must not exist) and dumps contents of
    internal data structures there. By sending SIGUSR1, you get all of
    these concatenated to `bird.dump` in the current directory.
    The file is only readable for the user running the daemon.
    The format of dump files is internal and could change in the future
    without any notice.

<span id="cli-echo" class="code">echo all\|off\|{ *list of log classes* } \[ *buffer-size* \]</span>  
Control echoing of log messages to the command-line output.
    See [log option](#opt-log) for a list of log classes.

<span id="cli-eval" class="code">eval *expr*</span>  
Evaluate given expression.
