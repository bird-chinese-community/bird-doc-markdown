## Pipe

### Introduction

The Pipe protocol serves as a link between two routing tables, allowing
routes to be passed from a table declared as primary (i.e., the one the pipe is
connected to using the `table` configuration keyword) to the secondary one
(declared using `peer table`) and vice versa, depending on what's allowed by
the filters. Export filters control export of routes from the primary table to
the secondary one, import filters control the opposite direction. Both tables
must be of the same nettype.

The Pipe protocol retransmits all routes from one table to the other table,
retaining their original source and attributes. If import and export filters
are set to accept, then both tables would have the same content.

The primary use of multiple routing tables and the Pipe protocol is for
policy routing, where handling of a single packet doesn't depend only on its
destination address, but also on its source address, source interface, protocol
type and other similar parameters. In many systems (Linux being a good example),
the kernel allows to enforce routing policies by defining routing rules which
choose one of several routing tables to be used for a packet according to its
parameters. Setting of these rules is outside the scope of BIRD's work (on
Linux, you can use the `ip` command), but you can create several routing
tables in BIRD, connect them to the kernel ones, use filters to control which
routes appear in which tables and also you can employ the Pipe protocol for
exporting a selected subset of one table to another one.

### Configuration

Essentially, the Pipe protocol is just a channel connected to a table on both
sides. Therefore, the configuration block for `protocol pipe` shall directly
include standard channel config options; see the example below.

<span id="pipe-peer-table" class="code">peer table *table*</span>  
Defines secondary routing table to connect to. The primary one is
    selected by the `table` keyword.

<span id="pipe-max-generation" class="code">max generation *expr*</span>  
Sets maximal generation of route that may pass through this pipe.
    The generation value is increased by one by each pipe on its path.
    Not meeting this requirement causes an error message complaining about
    an overpiped route. If you have long chains of pipes, you probably want
    to raise this value; anyway the default of 16 should be enough for even
    most strange uses. Maximum is 254.

### Attributes

The Pipe protocol doesn't define any route attributes.

### Example

Let's consider a router which serves as a boundary router of two different
autonomous systems, each of them connected to a subset of interfaces of the
router, having its own exterior connectivity and wishing to use the other AS as
a backup connectivity in case of outage of its own exterior line.

Probably the simplest solution to this situation is to use two routing tables
(we'll call them `as1` and `as2`) and set up kernel routing rules, so that
packets having arrived from interfaces belonging to the first AS will be routed
according to `as1` and similarly for the second AS. Thus we have split our
router to two logical routers, each one acting on its own routing table, having
its own routing protocols on its own interfaces. In order to use the other AS's
routes for backup purposes, we can pass the routes between the tables through a
Pipe protocol while decreasing their preferences and correcting their BGP paths
to reflect the AS boundary crossing.


    ipv4 table as1;             # Define the tables
    ipv4 table as2;

    protocol kernel kern1 {         # Synchronize them with the kernel
        ipv4 { table as1; export all; };
        kernel table 1;
    }

    protocol kernel kern2 {
        ipv4 { table as2; export all; };
        kernel table 2;
    }

    protocol bgp bgp1 {         # The outside connections
        ipv4 { table as1; import all; export all; };
        local as 1;
        neighbor 192.168.0.1 as 1001;
    }

    protocol bgp bgp2 {
        ipv4 { table as2; import all; export all; };
        local as 2;
        neighbor 10.0.0.1 as 1002;
    }

    protocol pipe {             # The Pipe
        table as1;
        peer table as2;
        export filter {
            if net ~ [ 1.0.0.0/8+] then {   # Only AS1 networks
                if preference>10 then preference = preference-10;
                if source=RTS_BGP then bgp_path.prepend(1);
                accept;
            }
            reject;
        };
        import filter {
            if net ~ [ 2.0.0.0/8+] then {   # Only AS2 networks
                if preference>10 then preference = preference-10;
                if source=RTS_BGP then bgp_path.prepend(2);
                accept;
            }
            reject;
        };
    }
