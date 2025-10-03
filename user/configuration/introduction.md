## Introduction

BIRD is configured using a text configuration file. Upon startup, BIRD reads
*prefix*`/etc/bird.conf` (unless the `-c` command line option
is given). Configuration may be changed at user's request: if you modify the
config file and then signal BIRD with `SIGHUP`, it will adjust to the new
config. Then there's the client which allows you to talk with BIRD in an
extensive way.

In the config, everything on a line after `#` or inside `/* */` is
a comment, whitespace characters are treated as a single space. If there's a
variable number of options, they are grouped using the `{ }` brackets. Each
option is terminated by a `;`. Configuration is case sensitive. There are two
ways how to name symbols (like protocol names, filter names, constants etc.).
You can either use a simple string starting with a letter (or underscore)
followed by any combination of letters, numbers and underscores (e.g. `R123`,
`my_filter`, `bgp5`) or you can enclose the name into apostrophes (`'`)
and than you can use any combination of numbers, letters, underscores, hyphens,
dots and colons (e.g.  `'1:strange-name'`, `'-NAME-'`, `'cool::name'`).

Here is an example of a simple config file. It enables synchronization of
routing tables with OS kernel, learns network interfaces and runs RIP on all
network interfaces found.


    protocol kernel {
        ipv4 {
            export all; # Default is export none
        };
        persist;        # Don't remove routes on BIRD shutdown
    }

    protocol device {
    }

    protocol rip {
        ipv4 {
            import all;
            export all;
        };
        interface "*";
    }
