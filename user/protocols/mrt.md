## MRT

### Introduction

The MRT protocol is a component responsible for handling the Multi-Threaded
Routing Toolkit (MRT) routing information export format, which is mainly used
for collecting and analyzing of routing information from BGP routers. The MRT
protocol can be configured to do periodic dumps of routing tables, created MRT
files can be analyzed later by other tools. Independent MRT table dumps can also
be requested from BIRD client. There is also a feature to save incoming BGP
messages in MRT files, but it is controlled by [mrtdump](#proto-mrtdump) options independently of MRT protocol, although that might
change in the future.

BIRD implements the main MRT format specification as defined in <a href="https://datatracker.ietf.org/doc/rfc6396" class="rfc">RFC
6396</a>
and the ADD_PATH extension (<a href="https://datatracker.ietf.org/doc/rfc8050" class="rfc">RFC
8050</a>).

### Configuration

MRT configuration consists of several statements describing routing table
dumps. Multiple independent periodic dumps can be done as multiple MRT protocol
instances. The MRT protocol does not use channels. There are two mandatory
statements: `filename` and `period`.

The behavior can be modified by following configuration parameters:

<span id="mrt-table" class="code">table *name* \| "*pattern*"</span>  
Specify a routing table (or a set of routing tables described by a
    wildcard pattern) that are to be dumped by the MRT protocol instance.
    Default: the master table.

<span id="mrt-filter" class="code">filter { *filter commands* }</span>  
The MRT protocol allows to specify a filter that is applied to routes as
    they are dumped. Rejected routes are ignored and not saved to the MRT
    dump file. Default: no filter.

<span id="mrt-where" class="code">where *filter expression*</span>  
An alternative way to specify a filter for the MRT protocol.

<span id="mrt-filename" class="code">filename "*filename*"</span>  
Specify a filename for MRT dump files. The filename may contain time
    format sequences with *strftime(3)* notation (see *man strftime*
    for details), there is also a sequence "%N" that is expanded to the name
    of dumped table. Therefore, each periodic dump of each table can be
    saved to a different file. Mandatory, see example below.

<span id="mrt-period" class="code">period *number*</span>  
Specify the time interval (in seconds) between periodic dumps.
    Mandatory.

<span id="mrt-always-add-path" class="code">always add path *switch*</span>  
The MRT format uses special records (specified in <a href="https://datatracker.ietf.org/doc/rfc8050" class="rfc">RFC
8050</a>) for
    routes received using BGP ADD\_PATH extension to keep Path ID, while
    other routes use regular records. This has advantage of better
    compatibility with tools that do not know special records, but it loses
    information about which route is the best route. When this option is
    enabled, both ADD\_PATH and non-ADD\_PATH routes are stored in ADD\_PATH
    records and order of routes for network is preserved. Default: disabled.

### Example


    protocol mrt {
        table "tab*";
        where source = RTS_BGP;
        filename "/var/log/bird/%N_%F_%T.mrt";
        period 300;
    }
