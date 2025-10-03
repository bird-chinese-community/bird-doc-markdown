## What is BIRD

The name \`BIRD' is actually an acronym standing for \`BIRD Internet Routing
Daemon'. Let's take a closer look at the meaning of the name:

**BIRD**: Well, we think we have already explained that. It's an acronym
standing for \`BIRD Internet Routing Daemon', you remember, don't you? :-)

**Internet
Routing**: It's a program (well, a daemon, as you are going to
discover in a moment) which works as a dynamic router in an Internet type
network (that is, in a network running either the IPv4 or the IPv6 protocol).
Routers are devices which forward packets between interconnected networks in
order to allow hosts not connected directly to the same local area network to
communicate with each other. They also communicate with the other routers in the
Internet to discover the topology of the network which allows them to find
optimal (in terms of some metric) rules for forwarding of packets (which are
called routing tables) and to adapt themselves to the changing conditions such
as outages of network links, building of new connections and so on. Most of
these routers are costly dedicated devices running obscure firmware which is
hard to configure and not open to any changes (on the other hand, their special
hardware design allows them to keep up with lots of high-speed network
interfaces, better than general-purpose computer does). Fortunately, most
operating systems of the UNIX family allow an ordinary computer to act as a
router and forward packets belonging to the other hosts, but only according to a
statically configured table.

A **Routing Daemon** is in UNIX terminology a non-interactive program
running on background which does the dynamic part of Internet routing, that is
it communicates with the other routers, calculates routing tables and sends them
to the OS kernel which does the actual packet forwarding. There already exist
other such routing daemons: routed (RIP only), GateD (non-free),
[Zebra](http://www.zebra.org) and
[MRTD](http://sourceforge.net/projects/mrt),
but their capabilities are limited and they are relatively hard to configure
and maintain.

BIRD is an Internet Routing Daemon designed to avoid all of these shortcomings,
to support all the routing technology used in the today's Internet or planned to
be used in near future and to have a clean extensible architecture allowing new
routing protocols to be incorporated easily. Among other features, BIRD
supports:

- both IPv4 and IPv6 protocols
- multiple routing tables
- the Border Gateway Protocol (BGPv4)
- the Routing Information Protocol (RIPv2, RIPng)
- the Open Shortest Path First protocol (OSPFv2, OSPFv3)
- the Babel Routing Protocol
- the Router Advertisements for IPv6 hosts
- a virtual protocol for exchange of routes between different
          routing tables on a single host
- a command-line interface allowing on-line control and inspection
          of status of the daemon
- soft reconfiguration (no need to use complex online commands to
          change the configuration, just edit the configuration file and
          notify BIRD to re-read it and it will smoothly switch itself to
          the new configuration, not disturbing routing protocols unless
          they are affected by the configuration changes)
- a powerful language for route filtering

BIRD has been developed at the Faculty of Math and Physics, Charles
University, Prague, Czech Republic as a student project. It can be freely
distributed under the terms of the GNU General Public License.

BIRD has been designed to work on all UNIX-like systems. It has been
developed and tested under Linux 2.0 to 2.6, and then ported to FreeBSD, NetBSD
and OpenBSD, porting to other systems (even non-UNIX ones) should be relatively
easy due to its highly modular architecture.

BIRD 1.x supported either IPv4 or IPv6 protocol, but had to be compiled separately
for each one. BIRD~2 supports both of them with a possibility of further extension.
BIRD~2 supports Linux at least 3.16, FreeBSD 10, NetBSD 7.0, and OpenBSD 5.8.
Anyway, it will probably work well also on older systems.
