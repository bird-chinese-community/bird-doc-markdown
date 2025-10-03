## Routing tables

The heart of BIRD is a routing table. BIRD has several independent routing tables;
each of them contains routes of exactly one *nettype* (see below). There are two
default tables -- `master4` for IPv4 routes and `master6` for IPv6 routes.
Other tables must be explicitly configured.

These routing tables are not kernel forwarding tables. No forwarding is done by
BIRD. If you want to forward packets using the routes in BIRD tables, you may
use the Kernel protocol (see below) to synchronize them with kernel FIBs.

Every nettype defines a (kind of) primary key on routes. Every route source can
supply one route for every possible primary key; new route announcement replaces
the old route from the same source, keeping other routes intact. BIRD always
chooses the best route for each primary key among the known routes and keeps the
others as suboptimal. When the best route is retracted, BIRD re-runs the best
route selection algorithm to find the current best route.

The global best route selection algorithm is (roughly) as follows:

- Preferences of the routes are compared.
- Source protocol instance preferences are compared.
- If source protocols are the same (e.g. BGP vs. BGP), the protocol's route selection algorithm is invoked.
- If source protocols are different (e.g. BGP vs. OSPF), result of the algorithm is undefined.

<span id="dsc-table-sorted"></span>Usually, a routing table just chooses a selected
route from a list of entries for one network. Optionally, these lists of entries
are kept completely sorted (according to preference or some protocol-dependent
metric). See [sorted](#rtable-sorted) table option for details.
