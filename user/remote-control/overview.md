## Overview

You can use the command-line client `birdc` to talk with a running
BIRD. Communication is done using the appropriate UNIX domain socket. The
commands can perform simple actions such as enabling/disabling of protocols,
telling BIRD to show various information, telling it to show routing table
filtered by filter, or asking BIRD to reconfigure. Press `?` at any time to
get online help. Option `-r` can be used to enable a restricted mode of BIRD
client, which allows just read-only commands (`show ...`). Option `-v` can
be passed to the client, to make it dump numeric return codes along with the
messages. You do not necessarily need to use `birdc` to talk to BIRD, your
own applications could do that, too -- the format of communication between BIRD
and `birdc` is stable (see the programmer's documentation).

There is also lightweight variant of BIRD client called `birdcl`, which
does not support command line editing and history and has minimal dependencies.
This is useful for running BIRD in resource constrained environments, where
Readline library (required for regular BIRD client) is not available.
