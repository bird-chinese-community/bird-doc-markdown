## Protocols and channels

BIRD protocol is an abstract class of producers and consumers of the routes.
Each protocol may run in multiple instances and bind on one side to route
tables via channels, on the other side to specified listen sockets (BGP),
interfaces (Babel, OSPF, RIP), APIs (Kernel, Direct), or nothing (Static, Pipe).

There are also two protocols that do not have any channels -- BFD and Device.
Both of them are kind of service for other protocols.

Each protocol is connected to a routing table through a channel. Some protocols
support only one channel (OSPF, RIP), some protocols support more channels (BGP, Direct).
Each channel has two filters which can accept, reject and modify the routes.
An *export* filter is applied to routes passed from the routing table to the protocol,
an *import* filter is applied to routes in the opposite direction.
