## RPKI

### Introduction

The Resource Public Key Infrastructure (RPKI) is mechanism for origin
validation of BGP routes (<a href="https://datatracker.ietf.org/doc/rfc6480" class="rfc">RFC
6480</a>). BIRD supports only so-called
RPKI-based origin validation. There is implemented RPKI to Router (RPKI-RTR)
protocol (<a href="https://datatracker.ietf.org/doc/rfc6810" class="rfc">RFC
6810</a>). It uses some of the RPKI data to allow a router to
verify that the autonomous system announcing an IP address prefix is in fact
authorized to do so. This is not crypto checked so can be violated. But it
should prevent the vast majority of accidental hijackings on the Internet today,
e.g. the famous Pakistani accidental announcement of YouTube's address space.

The RPKI-RTR protocol receives and maintains a set of ROAs from a cache
server (also called validator). You can validate routes (<a href="https://datatracker.ietf.org/doc/rfc6483" class="rfc">RFC
6483</a>,
<a href="https://datatracker.ietf.org/doc/rfc6811" class="rfc">RFC
6811</a>) using function `roa_check()` in filter and set it as import
filter at the BGP protocol. BIRD offers crude automatic re-validating of
affected routes after RPKI update, see option [rpki
reload](#proto-rpki-reload). Or you can use a BIRD client command `reload in
`*bgp_protocol_name* for manual call of revalidation of all routes.

The same protocol, since version 2, also receives and maintains a set
of ASPAs. You can then validate AS paths using function `aspa_check()`
in (import) filters.

### Supported transports

- Unprotected transport over TCP uses a port 323. The cache server
          and BIRD router should be on the same trusted and controlled network
          for security reasons.
- SSHv2 encrypted transport connection uses the normal SSH port
          22.

### Configuration overview

We currently support just one cache server per protocol. However you can
define more RPKI protocols generally.


    protocol rpki [<name>] {
        roa4 { table <tab>; };
        roa6 { table <tab>; };
        aspa { table <tab>; };
        remote <ip> | "<domain>" [port <number>];
        port <number>;
        local address <ip>;
        refresh [keep] <number>;
        retry [keep] <number>;
        expire [keep] <number>;
        ignore max length <switch>;
        min version <number>;
        max version <number>;
        transport tcp {
            authentication none|md5;
            password "<text>";
        };
        transport ssh {
            bird private key "&lt;/path/to/id_rsa&gt;";
            remote public key "&lt;/path/to/known_host&gt;";
            user "<name>";
        };
    }

Alse note that you have to specify the ROA and ASPA channels. If you want to import
only IPv4 prefixes you have to specify only roa4 channel. Similarly with IPv6
prefixes only. If you want to fetch both IPv4 and even IPv6 ROAs you have to
specify both channels.

#### RPKI protocol options

<span id="rpki-remote" class="code">remote *ip* \| "*hostname*" \[port *number*\]</span>  
Specifies
        a destination address of the cache server.  Can be specified by an IP
        address or by full domain name string.  Only one cache can be specified
        per protocol. This option is required.

<span id="rpki-port" class="code">port *number*</span>  
Specifies the port number. The default port
        number is 323 for transport without any encryption and 22 for transport
        with SSH encryption.

<span id="rpki-local-address" class="code">local address *ip*</span>  
Define local address we should use as a source address for the RTR session.

<span id="rpki-refresh" class="code">refresh \[keep\] *number*</span>  
Time period in seconds. Tells how
        long to wait before next attempting to poll the cache using a Serial
        Query or a Reset Query packet. Must be lower than 86400 seconds (one
        day). Too low value can caused a false positive detection of
        network connection problems.  A keyword `keep` suppresses updating
        this value by a cache server.
        Default: 3600 seconds

<span id="rpki-retry" class="code">retry \[keep\] *number*</span>  
Time period in seconds between a failed
        Serial/Reset Query and a next attempt.  Maximum allowed value is 7200
        seconds (two hours). Too low value can caused a false positive
        detection of network connection problems.  A keyword `keep`
        suppresses updating this value by a cache server.
        Default: 600 seconds

<span id="rpki-expire" class="code">expire \[keep\] *number*</span>  
Time period in seconds. Received
        records are deleted if the client was unable to successfully refresh
        data for this time period.  Must be in range from 600 seconds (ten
        minutes) to 172800 seconds (two days).  A keyword `keep`
        suppresses updating this value by a cache server.
        Default: 7200 seconds

<span id="rpki-ignore-max-length" class="code">ignore max length *switch*</span>  
Ignore received max length in ROA records and use max value (32 or 128)
    instead. This may be useful for implementing loose RPKI check for
    blackholes. Default: disabled.

<span id="rpki-min-version" class="code">min version *number*</span>  
Minimal allowed version of the RTR protocol. BIRD will refuse to
    downgrade a connection below this version and drop the session instead.
    Default: 0

<span id="rpki-max-version" class="code">max version *number*</span>  
Maximal allowed version of the RTR protocol. BIRD will start with this
        version. Use this option if sending version 2 to your cache causes
        problems. Default: 2

<span id="rpki-transport-tcp" class="code">transport tcp { *TCP transport options...* }</span>  
Transport over
        TCP, it's the default transport. Cannot be combined with a SSH transport.
        Default: TCP, no authentication.

<span id="rpki-transport-ssh" class="code">transport ssh { *SSH transport options...* }</span>  
It enables a
        SSHv2 transport encryption. Cannot be combined with a TCP transport.
        Default: off

##### TCP transport options

<span id="rpki-tcp-authentication" class="code">authentication none\|md5</span>  
Select authentication method to be used. `none` means no
    authentication, `md5` is TCP-MD5 authentication (<a href="https://datatracker.ietf.org/doc/rfc2385" class="rfc">RFC
2385</a>).
    Default: no authentication.

<span id="rpki-tcp-password" class="code">password "*text*"</span>  
Use this password for TCP-MD5 authentication of the RPKI-To-Router session.

##### SSH transport options

<span id="rpki-ssh-private-key" class="code">bird private key "*/path/to/id_rsa*"</span>  
A path to the BIRD's private SSH key for authentication.
    It can be a *id_rsa* file.

<span id="rpki-ssh-remote-public-key" class="code">remote public key "*/path/to/known_host*"</span>  
A path to the cache's public SSH key for verification identity
    of the cache server. It could be a path to *known_host* file.

<span id="rpki-ssh-user" class="code">user "*name*"</span>  
A SSH user name for authentication. This option is required.

### Examples

#### BGP origin validation

Policy: Don't import `ROA_INVALID` routes.


    roa4 table r4;
    roa6 table r6;

    protocol rpki {
        debug all;

        roa4 { table r4; };
        roa6 { table r6; };

        # Please, do not use rpki-validator.realmv6.org in production
        remote "rpki-validator.realmv6.org" port 8282;

        retry keep 5;
        refresh keep 30;
        expire 600;
    }

    filter peer_in_v4 {
        if (roa_check(r4, net, bgp_path.last) = ROA_INVALID) then
        {
            print "Ignore RPKI invalid ", net, " for ASN ", bgp_path.last;
            reject;
        }
        accept;
    }

    protocol bgp {
        debug all;
        local as 65000;
        neighbor 192.168.2.1 as 65001;
        ipv4 {
            import filter peer_in_v4;
            export none;
        };
    }

#### SSHv2 transport encryption


    roa4 table r4;
    roa6 table r6;

    protocol rpki {
        debug all;

        roa4 { table r4; };
        roa6 { table r6; };

        remote 127.0.0.1 port 2345;
        transport ssh {
            bird private key "/home/birdgeek/.ssh/id_rsa";
            remote public key "/home/birdgeek/.ssh/known_hosts";
            user "birdgeek";
        };

        # Default interval values
    }
