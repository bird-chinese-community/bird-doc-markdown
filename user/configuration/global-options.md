## Global options

<span id="opt-include" class="code">include "*filename*";</span>  
This statement causes inclusion of a new file. The *filename* could
    also be a wildcard, in that case matching files are included in
    alphabetic order. The maximal depth is 8. Note that this statement can
    be used anywhere in the config file, even inside other options, but
    always on the beginning of line. In the following example, the first
    semicolon belongs to the `include`, the second to `ipv6 table`.
    If the `tablename.conf` contains exactly one token (the name of the
    table), this construction is correct:


    ipv6 table
    include "tablename.conf";;

<span id="opt-log" class="code">log "*filename*" \[*limit* "*backup*"\] \| fixed "*filename*" *size* \| syslog \[name *name*\] \| stderr \| udp *address* \[port *port*\] all\|{ *list of classes* }</span>  
Set logging of messages having the given class (either `all` or `{
    error|trace [, `*...*`] }` etc.) into selected destination - a file
    specified as a filename string (with optional log rotation information),
    syslog (with optional name argument), the stderr output, or as a UDP
    message (in <a href="https://datatracker.ietf.org/doc/rfc3164" class="rfc">RFC
3164</a> syslog format).

Classes are:
    `info`, `warning`, `error` and `fatal` for messages about local problems,
    `debug` for debugging messages,
    `trace` when you want to know what happens in the network,
    `remote` for messages about misbehavior of remote machines,
    `auth` about authentication failures,
    `bug` for internal BIRD bugs.

Logging directly to file supports basic log rotation -- there is an
    optional log file limit and a backup filename, when log file reaches the
    limit, the current log file is renamed to the backup filename and a new
    log file is created. It's also possible to log to a single file behaving
    as a ring buffer with a fixed size.

You may specify more than one `log` line to establish logging to
    multiple destinations. Default: log everything to the system log, or
    to the debug output if debugging is enabled by `-d`/`-D`
    command-line option.

<span id="opt-debug-protocols" class="code">debug protocols all\|off\|{ states\|routes\|filters\|interfaces\|events\|packets \[, *...*\] }</span>  
Set global defaults of protocol debugging options.
    See [debug](#proto-debug) in the following section.
    Default: off.

<span id="opt-debug-channels" class="code">debug channels all\|off\|{ states\|routes\|filters\|events \[, *...*\] }</span>  
Set global defaults of channel debugging options.
    See [debug](#channel-debug) in the channel section.
    Default: off.

<span id="opt-debug-tables" class="code">debug tables all\|off\|{ states\|routes\|filters\|events \[, *...*\] }</span>  
Set global defaults of table debugging options.
    See [debug](#rtable-debug) in the table section.
    Default: off.

<span id="opt-debug-commands" class="code">debug commands *number*</span>  
Control logging of client connections (0 for no logging, 1 for logging
    of connects and disconnects, 2 and higher for logging of all client
    commands). Default: 0.

<span id="opt-debug-latency" class="code">debug latency all\|off\|{ ping\|wakeup\|scheduling\|sockets\|events\|timers }</span>  
Activate tracking of internal scheduler actions. This is a developer
    and technical support tool for cases when internal events are missed.
    You should keep this off unless you know what you are doing. Default: off.

<span id="opt-debug-latency-limit" class="code">debug latency limit *time*</span>  
If `debug latency` is enabled, this option allows to specify a limit
    for elapsed time. Events exceeding the limit are logged. Default: 1 s.

<span id="opt-watchdog-warn" class="code">watchdog warning *time*</span>  
Set time limit for I/O loop cycle. If one iteration took more time to
    complete, a warning is logged. Default: 5 s.

<span id="opt-watchdog-timeout" class="code">watchdog timeout *time*</span>  
Set time limit for I/O loop cycle. If the limit is breached, BIRD is
    killed by abort signal. The timeout has effective granularity of
    seconds, zero means disabled. Default: disabled (0).

<span id="opt-mrtdump" class="code">mrtdump "*filename*"</span>  
Set MRTdump file name. This option must be specified to allow MRTdump
    feature. Default: no dump file.

<span id="opt-mrtdump-protocols" class="code">mrtdump protocols all\|off\|{ states\|messages \[, *...*\] }</span>  
Set global defaults of MRTdump options. See `mrtdump` in the
    following section. Default: off.

<span id="opt-filter" class="code">filter *name local variables*{ *commands* }</span>  
Define a filter. You can learn more about filters in the following
    chapter.

<span id="opt-function" class="code">function *name* (*parameters*) \[ -\> *return type* \] *local variables* { *commands* }</span>  
Define a function. You can learn more about functions in the following chapter.

<span id="opt-protocol" class="code">protocol rip\|ospf\|bgp\|*...* \[*name* \[from *name2*\]\] { *protocol options* }</span>  
Define a protocol instance called *name* (or with a name like
    "rip5" generated automatically if you don't specify any
    *name*). You can learn more about configuring protocols in
    their own chapters. When `from `*name2* expression is used,
    initial protocol options are taken from protocol or template
    *name2* You can run more than one instance of most protocols
    (like RIP or BGP). By default, no instances are configured.

<span id="opt-template" class="code">template rip\|ospf\|bgp\|*...* \[*name* \[from *name2*\]\] { *protocol options* }</span>  
Define a protocol template instance called *name* (or with a name like
    "bgp1" generated automatically if you don't specify any *name*).
    Protocol templates can be used to group common options when many
    similarly configured protocol instances are to be defined. Protocol
    instances (and other templates) can use templates by using `from`
    expression and the name of the template. At the moment templates (and
    `from` expression) are not implemented for OSPF protocol.

<span id="opt-define" class="code">define *constant* = *expression*</span>  
Define a constant. You can use it later in every place you could use a
    value of the same type. Besides, there are some predefined numeric
    constants based on /etc/iproute2/rt\_\* files. A list of defined constants
    can be seen (together with other symbols) using 'show symbols' command.

<span id="opt-attribute" class="code">attribute *type* *name*</span>  
Declare a custom route attribute. You can set and get it in filters like
    any other route attribute. This feature is intended for marking routes
    in import filters for export filtering purposes instead of locally
    assigned BGP communities which have to be deleted in export filters.

<span id="opt-router-id" class="code">router id *number* \| *IPv4 address*</span>  
Set BIRD's router ID. It's a 4-byte non-zero integer that should be unique
    within an AS. Default: the lowest IPv4 address of the router.

<span id="opt-router-id-from" class="code">router id from \[-\] \[ "*mask*" \] \[ *prefix* \] \[, *...*\]</span>  
Set BIRD's router ID based on an IPv4 address of an interface specified by
    an interface pattern.
    See [interface](#proto-iface) section for detailed
    description of interface patterns with extended clauses.

<span id="opt-hostname" class="code">hostname "*name*"</span>  
Set hostname. Default: node name as returned by \`uname -n'.

<span id="opt-graceful-restart" class="code">graceful restart wait *number*</span>  
During graceful restart recovery, BIRD waits for convergence of routing
    protocols. This option allows to specify a timeout for the recovery to
    prevent waiting indefinitely if some protocols cannot converge. Default:
    240 seconds.

<span id="opt-timeformat" class="code">timeformat route\|protocol\|base\|log "*format1*" \[*limit* "*format2*"\]</span>  
This option allows to specify a format of date/time used by BIRD. The
    first argument specifies for which purpose such format is used.
    `route` is a format used in 'show route' command output,
    `protocol` is used in 'show protocols' command output, `base` is
    used for other commands and `log` is used in a log file.

"*format1*" is a format string using *strftime(3)* notation (see
    *man strftime* for details). It is extended to support sub-second
    time part with variable precision (up to microseconds) using "%f"
    conversion code (e.g., "%T.%3f" is hh:mm:ss.sss time). *limit* and
    "*format2*" allow to specify the second format string for times in
    past deeper than *limit* seconds.

There are several shorthands: `iso long` is a ISO 8601 date/time
    format (YYYY-MM-DD hh:mm:ss) that can be also specified using `"%F
    %T"`. Similarly, `iso long ms` and `iso long us` are ISO 8601
    date/time formats with millisecond or microsecond precision.
    `iso short` is a variant of ISO 8601 that uses just the time format
    (hh:mm:ss) for near times (up to 20 hours in the past) and the date
    format (YYYY-MM-DD) for far times. This is a shorthand for `"%T"
    72000 "%F"`. And there are also `iso short ms` and `iso short us`
    high-precision variants of that.

By default, BIRD uses the `iso short ms` format for `route` and
    `protocol` times, and the `iso long ms` format for `base` and
    `log` times.

<span id="opt-table" class="code">*nettype* table *name* \[ { *option*; \[*...*\] } \]</span>  
Define a new routing table. The default routing tables `master4` and
    `master6` are defined implicitly, other routing tables have to be
    defined by this option. See the [routing table configuration
section](#rtable-opts) for routing table options.

<span id="opt-mpls-domain" class="code">mpls domain *name* \[ { *option*; \[*...*\] } \]</span>  
Define a new MPLS domain. MPLS domains represent independent label
    spaces and are responsible for MPLS label management. All MPLS-aware
    protocols are associated with some MPLS domain. See the [MPLS
configuration section](#mpls-opts) for MPLS domain options.

<span id="opt-eval" class="code">eval *expr*</span>  
Evaluates given filter expression. It is used by the developers for testing of filters.

### Thread setup

BIRD runs in several threads. There is one main thread, taking care about startup,
shutdown, (re)configuration, CLI and several protocols which have not yet been updated
to run in other threads. Then there are several thread groups, running the rest.

Default thread group is `worker`. This group runs (by default) BGP, BMP, MRT,
Pipe and RPKI. Also the routing table maintenance routines run in these threads.
For BFD, there is another thread group called
`express`, with minimal latency, expecting all tasks to be extremely fast.
The threads are started as soon as some work is required from them, therefore
if you don't configure BFD, the express thread won't start.

Any configuration of thread groups must be strictly placed before any table
or protocol configuration, as these depend on the thread group existence.

Thread groups are configured by writing `thread group `*name*` {}` blocks,
which contain the following options:

<span id="thread-group-threads" class="code">threads *number*</span>  
Set how many threads should BIRD spawn in this thread group.
    Every thread can utilize one complete CPU core. You probably want to
        keep at least one free core for other processes. The maximum feasible
    thread count heavily depends on the actual workload and must be determined
    by testing or estimation. Default: 1

<span id="thread-group-default" class="code">default *bool*</span>  
Mark this thread group as default. There must be exactly one thread group
    marked as default. Default default thread group: `worker`

<span id="thread-max-latency" class="code">max latency *time*</span>  
Set maximum latency for the thread group. The group tries to dispatch every
    event before this time elapses. Setting this too low causes BIRD
    to spend lots of time on overhead and fill logs with reports.
    Setting this too high causes BIRD to hold locks for long times.
    Do not change unless you know what you are doing.
    Default: 1 s for worker, 10 ms for express.

<span id="thread-min-time" class="code">min time *time*</span>  
Set minimum awarded loop execution time for the thread group.
    This is an internal performance tuning knob which may change between
    minor versions. Do not change unless you know what you are doing.
    Default: 10 ms for worker, 1 ms for express.

<span id="thread-min-time" class="code">max time *time*</span>  
Set maximum awarded loop execution time for the thread group. This is
    an internal performance tuning knob which may change between
    minor versions. Do not change unless you know what you are doing.
    Default: 300 ms for worker, 10 ms for express.

<span id="thread-wakeup-time" class="code">wakeup time *time*</span>  
Set maximum sleep time for the thread group. Every thread will wake up
    after this time even if no work is requested. This is an internal
    IO loop knob which may change between minor versions.
    Do not change unless you know what you are doing.
    Default: 31415 s for worker, 60 s for express.

There is also a "simple" thread group setting. If you write
`threads `*number* on top level, it is equivalent to setting
the worker group thread count to that number, and the express group thread count
to one. This setting is deprecated and may disappear in some future version.
