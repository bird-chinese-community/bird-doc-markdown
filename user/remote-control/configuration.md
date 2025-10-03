## Configuration

By default, BIRD opens `bird.ctl` UNIX domain socket and the CLI tool
connects to it. If changed on the command line by the `-s` option,
BIRD or the CLI tool connects there instead.

It's also possible to configure additional remote control sockets in the
configuration file by `cli "name";` and you can open how many
sockets you wish. There are no checks whether the user configured the same
socket multiple times and BIRD may behave weirdly if this happens. On shutdown,
the additional sockets get removed immediately and only the main socket stays
until the very end.

The remote control socket can be also set as restricted by
`cli "name" { restrict; };` instead of sending the `restrict` command
after connecting. The user may still overload the daemon by requesting insanely
complex filters so you shouldn't expose this socket to public anyway.
