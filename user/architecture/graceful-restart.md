## Graceful restart

When BIRD is started after restart or crash, it repopulates routing tables in
an uncoordinated manner, like after clean start. This may be impractical in some
cases, because if the forwarding plane (i.e. kernel routing tables) remains
intact, then its synchronization with BIRD would temporarily disrupt packet
forwarding until protocols converge. Graceful restart is a mechanism that could
help with this issue. Generally, it works by starting protocols and letting them
repopulate routing tables while deferring route propagation until protocols
acknowledge their convergence. Note that graceful restart behavior have to be
configured for all relevant protocols and requires protocol-specific support
(currently implemented for Kernel and BGP protocols), it is activated for
particular boot by option `-R`.

Some protocols (e.g. BGP) could be restarted gracefully after both
intentional outage and crash, while others (e.g. OSPF) after intentional outage
only. For planned graceful restart, BIRD must be shut down by
[graceful restart](#cli-graceful-restart) command instead of
regular [down](#cli-down) command. In this way routing neighbors
are notified about planned graceful restart and routes are kept in kernel table
after shutdown.
