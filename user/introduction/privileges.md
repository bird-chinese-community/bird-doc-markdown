## Privileges

BIRD, as a routing daemon, uses several privileged operations (like setting
routing table and using raw sockets). Traditionally, BIRD is executed and runs
with root privileges, which may be prone to security problems. The recommended
way is to use a privilege restriction (options `-u`, `-g`). In that case
BIRD is executed with root privileges, but it changes its user and group ID to
an unprivileged ones, while using Linux capabilities to retain just required
privileges (capabilities CAP_NET\_\*). Note that the control socket is created
before the privileges are dropped, but the config file is read after that. The
privilege restriction is not implemented in BSD port of BIRD.

An unprivileged user (as an argument to `-u` options) may be the user
`nobody`, but it is suggested to use a new dedicated user account (like
`bird`). The similar considerations apply for the group option, but there is
one more condition -- the users in the same group can use `birdc` to
control BIRD.

Finally, there is a possibility to use external tools to run BIRD in an
environment with restricted privileges. This may need some configuration, but it
is generally easy -- BIRD needs just the standard library, privileges to read
the config file and create the control socket and the CAP_NET\_\* capabilities.
