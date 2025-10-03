## Configuration

By default, BIRD opens `bird.ctl` UNIX domain socket and the CLI tool
connects to it. If changed on the command line by the `-s` option,
BIRD or the CLI tool connects there instead.

It's also possible to configure additional remote control sockets in the
configuration file by `cli "name" { <m`options/ };/ and you can open how many
sockets you wish. There are no checks whether the user configured the same
socket multiple times and BIRD may behave weirdly if this happens. On shutdown,
the additional sockets get removed immediately and only the main socket stays
until the very end. If there are no options, the braces may be omitted.

Options:

<span id="cli-conf-restrict" class="code">restrict</span>  
Set the socket to be restricted as if the user always sent the
    `restrict` command after connecting. The user may still overload
    the daemon by requesting insanely complex filters so you shouldn't
    expose this socket to public even if restricted.

<span id="cli-conf-v2-attributes" class="code">v2 attributes</span>  
Display the names and composition of route attributes the same way as BIRD 2 does.
    This is a compatibility option for easier transition from BIRD 2 to BIRD 3.
