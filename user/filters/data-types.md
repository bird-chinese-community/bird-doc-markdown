## Data types

Each variable and each value has certain type. Booleans, integers and enums
are incompatible with each other (that is to prevent you from shooting oneself
in the foot).

<span id="type-bool" class="code">bool</span>  
This is a boolean type, it can have only two values, `true` and
    `false`. Boolean is the only type you can use in `if` statements.

<span id="type-int" class="code">int</span>  
This is a general integer type. It is an unsigned 32bit type; i.e., you
    can expect it to store values from 0 to 4294967295. Overflows are not
    checked. You can use `0x1234` syntax to write hexadecimal values.

<span id="type-pair" class="code">pair</span>  
This is a pair of two short integers. Each component can have values
    from 0 to 65535. Literals of this type are written as `(1234,5678)`.
    The same syntax can also be used to construct a pair from two arbitrary
    integer expressions (for example `(1+2,a)`).

Operators `.asn` and `.data` can be used to extract corresponding
    components of a pair: `(`*asn*`, `*data*`)`.

<span id="type-quad" class="code">quad</span>  
This is a dotted quad of numbers used to represent router IDs (and
    others). Each component can have a value from 0 to 255. Literals of
    this type are written like IPv4 addresses.

<span id="type-string" class="code">string</span>  
This is a string of characters. There are no ways to modify strings in
    filters. You can pass them between functions, assign them to variables
    of type `string`, print such variables, use standard string
    comparison operations (e.g. `=, !=, <, >, <=, >=`), but
    you can't concatenate two strings. String literals are written as
    `"This is a string constant"`. Additionally matching (`~,
    !~`) operators could be used to match a string value against
    a shell pattern (represented also as a string).

<span id="type-bytestring" class="code">bytestring</span>  
This is a sequence of arbitrary bytes. There are no ways to modify
    bytestrings in filters. You can pass them between functions, assign
    them to variables of type `bytestring`, print such values, and
    compare bytestings (`=, !=`).

Bytestring literals are written as a sequence of hexadecimal digit
    pairs, optionally colon-separated. A bytestring specified this way
    must be either at least 16 bytes (32 digits) long, or prefixed by the
    `hex:` prefix: `01:23:45:67:89:ab:cd:ef:01:23:45:67:89:ab:cd:ef`,
    `0123456789abcdef0123456789abcdef`, `hex:`, `hex:12:34:56`,
    `hex:12345678`.

A bytestring can be made from a hex string using `from_hex()`
    function. Source strings can use any number of dots, colons, hyphens
    and spaces as byte separators: `from_hex(" 12.34 56:78 ab-cd-ef ")`.

<span id="type-ip" class="code">ip</span>  
This type can hold a single IP address. The IPv4 addresses are stored as
    IPv4-Mapped IPv6 addresses so one data type for both of them is used.
    Whether the address is IPv4 or not may be checked by `.is_v4`
    which returns a `bool`. IP addresses are written in the standard
    notation (`10.20.30.40` or `fec0:3:4::1`). You can apply special
    operator `.mask(`*number*`)` on values of type ip. It masks out
    all but first *number* bits from the IP address. So
    `1.2.3.4.mask(8) = 1.0.0.0` is true.

<span id="type-prefix" class="code">prefix</span>  
This type can hold a network prefix consisting of IP address, prefix
    length and several other values. This is the key in route tables.

Prefixes may be of several types, which can be determined by the special
    operator `.type`. The type may be:

`NET_IP4` and `NET_IP6` prefixes hold an IP prefix. The literals
    are written as *ipaddress*`/`*pxlen*. There are two special
    operators on these: `.ip` which extracts the IP address from the
    pair, and `.len`, which separates prefix length from the pair.
    So `1.2.0.0/16.len = 16` is true.

`NET_IP6_SADR` nettype holds both destination and source IPv6
    prefix. The literals are written as *ipaddress*`/`*pxlen*` from
    `*ipaddress*`/`*pxlen*, where the first part is the destination
    prefix and the second art is the source prefix. They support the same
    operators as IP prefixes, but just for the destination part. They also
    support `.src` and `.dst` operators to get respective parts of the
    address as separate `NET_IP6` values.

`NET_VPN4` and `NET_VPN6` prefixes hold an IP prefix with VPN
    Route Distinguisher (<a href="https://datatracker.ietf.org/doc/rfc4364" class="rfc">RFC
4364</a>). They support the same special
    operators as IP prefixes, and also `.rd` which extracts the Route
    Distinguisher. Their literals are written
    as *rd*` `*ipprefix*

`NET_ROA4` and `NET_ROA6` prefixes hold an IP prefix range
    together with an ASN. They support the same special operators as IP
    prefixes, and also `.maxlen` which extracts maximal prefix length,
    and `.asn` which extracts the ASN.

`NET_FLOW4` and `NET_FLOW6` hold an IP prefix together with a
    flowspec rule. Filters currently do not support much flowspec parsing,
    only `.src` and `.dst` operators to get source and destination
    parts of the flowspec as separate `NET_IP4` / `NET_IP6` values.

`NET_MPLS` holds a single MPLS label and its handling is currently
    not implemented.

<span id="type-rd" class="code"><span id="type-vpnrd"></span>rd</span>  
This is a route distinguisher according to <a href="https://datatracker.ietf.org/doc/rfc4364" class="rfc">RFC
4364</a>. There are
    three kinds of RDs: *asn*`:`*32bit int*, *asn4*`:`*16bit int*
    and *IPv4 address*`:`*32bit int*

<span id="type-ec" class="code">ec</span>  
This is a specialized type used to represent BGP extended community
    values. It is essentially a 64bit value, literals of this type are
    usually written as `(`*kind*`, `*key*`, `*value*`)`, where
    `kind` is a kind of extended community (e.g. `rt` / `ro` for a
    route target / route origin communities), the format and possible values
    of `key` and `value` are usually integers, but it depends on the
    used kind. Similarly to pairs, ECs can be constructed using expressions
    for `key` and `value` parts, (e.g. `(ro, myas, 3*10)`, where
    `myas` is an integer variable).

<span id="type-lc" class="code">lc</span>  
This is a specialized type used to represent BGP large community
    values. It is essentially a triplet of 32bit values, where the first
    value is reserved for the AS number of the issuer, while meaning of
    remaining parts is defined by the issuer. Literals of this type are
    written as `(123, 456, 789)`, with any integer values. Similarly to
    pairs, LCs can be constructed using expressions for its parts, (e.g.
    `(myas, 10+20, 3*10)`, where `myas` is an integer variable).

Operators `.asn`, `.data1`, and `.data2` can be used
    to extract corresponding components of LCs:
    `(`*asn*`, `*data1*`, `*data2*`)`.

<span id="type-set" class="code">int\|pair\|quad\|ip\|prefix\|ec\|lc\|rd\|enum set</span>  
Filters recognize several types of sets. Sets are similar to strings: you
    can pass them around but you cannot modify them. Literals of type `int
    set` look like `[ 1, 2, 5..7 ]`. As you can see, both simple
    values and ranges are permitted in sets.

For pair sets, expressions like `(123,*)` can be used to denote
    ranges (in that case `(123,0)..(123,65535)`). You can also use
    `(123,5..100)` for range `(123,5)..(123,100)`. You can also use
    `*` and `a..b` expressions in the first part of a pair, note that
    such expressions are translated to a set of intervals, which may be
    memory intensive. E.g. `(*,4..20)` is translated to `(0,4..20),
    (1,4..20), (2,4..20), ... (65535, 4..20)`.

EC sets use similar expressions like pair sets, e.g. `(rt, 123,
    10..20)` or `(ro, 123, *)`. Expressions requiring the translation
    (like `(rt, *, 3)`) are not allowed (as they usually have 4B range
    for ASNs).

Also LC sets use similar expressions like pair sets. You can use ranges
    and wildcards, but if one field uses that, more specific (later) fields
    must be wildcards. E.g., `(10, 20..30, *)` or `(10, 20, 30..40)`
    is valid, while `(10, *, 20..30)` or `(10, 20..30, 40)` is not
    valid.

You can also use named constants or compound expressions for non-prefix
    set values. However, it must be possible to evaluate these expressions
    before daemon boots. So you can use only constants inside them. Also,
    in case of compound expressions, they require parentheses around them.
    E.g.


    define one=1;
    define myas=64500;

    int set odds = [ one, (2+1), (6-one), (2*2*2-1), 9, 11 ];
    pair set ps = [ (1,one+one), (3,4)..(4,8), (5,*), (6,3..6), (7..9,*) ];
    ec set es = [ (rt, myas, *), (rt, myas+2, 0..16*16*16-1) ];

Sets of prefixes are special: their literals does not allow ranges, but
    allows prefix patterns that are written
    as *ipaddress*`/`*pxlen*`{`*low*`,`*high*`}`.
    Prefix *ip1*`/`*len1* matches prefix
    pattern *ip2*`/`*len2*`{`*l*`,`*h*`}` if the
    first `min(len1, len2)` bits of `ip1` and `ip2` are
    identical and `l <= len1 <= h`. A valid prefix pattern
    has to satisfy `low <= high`, but `pxlen` is not
    constrained by `low` or `high`. Obviously, a prefix matches a
    prefix set literal if it matches any prefix pattern in the prefix set
    literal.

There are also two shorthands for prefix patterns: *address*`/`*len*`+`
    is a shorthand for *address*`/`*len*`{`*len*`,`*maxlen*`}`
    (where *maxlen* is 32 for IPv4 and 128 for IPv6), that means
    network prefix *address*`/`*len* and all its subnets.
    *address*`/`*len*`-` is a shorthand for
    *address*`/`*len*`{0,`*len*`}`, that means network prefix
    *address*`/`*len* and all its supernets (network prefixes
    that contain it).

For example, `[ 1.0.0.0/8, 2.0.0.0/8+, 3.0.0.0/8-, 4.0.0.0/8{16,24}
    ]` matches prefix `1.0.0.0/8`, all subprefixes of
    `2.0.0.0/8`, all superprefixes of `3.0.0.0/8` and prefixes
    `4.X.X.X` whose prefix length is 16 to 24. `[ 0.0.0.0/0{20,24} ]`
    matches all prefixes (regardless of IP address) whose prefix length is
    20 to 24, `[ 1.2.3.4/32- ]` matches any prefix that contains IP
    address `1.2.3.4`. `1.2.0.0/16 ~ [ 1.0.0.0/8{15,17} ]`
    is true, but `1.0.0.0/16 ~ [ 1.0.0.0/8- ]` is false.

Cisco-style patterns like `10.0.0.0/8 ge 16 le 24` can be expressed
    in BIRD as `10.0.0.0/8{16,24}`, `192.168.0.0/16 le 24` as
    `192.168.0.0/16{16,24}` and `192.168.0.0/16 ge 24` as
    `192.168.0.0/16{24,32}`.

It is not possible to mix IPv4 and IPv6 prefixes in a prefix set. It is
    currently possible to mix IPv4 and IPv6 addresses in an ip set, but that
    behavior may change between versions without any warning; don't do it
    unless you are more than sure what you are doing. (Really, don't do it.)

<span id="type-enum" class="code">enum</span>  
Enumeration types are fixed sets of possibilities. You can't define your
    own variables of such type, but some route attributes are of enumeration
    type. Enumeration types are incompatible with each other.

<span id="type-bgppath" class="code">bgppath</span>  
BGP path is a list of autonomous system numbers. You can't write
    literals of this type. There are several special operators on bgppaths:

*P*`.first` returns the first ASN (the neighbor ASN) in path *P*.

*P*`.last` returns the last ASN (the source ASN) in path *P*.

*P*`.last_nonaggregated` returns the last ASN in the non-aggregated part of the path *P*.

Both `first` and `last` return zero if there is no appropriate
    ASN, for example if the path contains an AS set element as the first (or
    the last) part. If the path ends with an AS set, `last_nonaggregated`
    may be used to get last ASN before any AS set.

*P*`.len` returns the length of path *P*.

*P*`.empty` makes the path *P* empty. Can't be used as a value, always modifies the object.

*P*`.prepend(`*A*`)` prepends ASN *A* to path *P* and
    returns the result.

*P*`.delete(`*A*`)` deletes all instances of ASN *A* from
    from path *P* and returns the result. *A* may also be an integer
    set, in that case the operator deletes all ASNs from path *P* that are
    also members of set *A*.

*P*`.filter(`*A*`)` deletes all ASNs from path *P* that are
    not members of integer set *A*, and returns the result.
    I.e., `filter` do the same as `delete` with inverted set *A*.

Methods `prepend`, `delete` and `filter` keep the
    original object intact as long as you use the result in any way. You can
    also write e.g. *P*`.prepend(`*A*`);` as a standalone statement.
    This variant does modify the original object with the result of the operation.

<span id="type-bgpmask" class="code">bgpmask</span>  
BGP masks are patterns used for BGP path matching (using `path
    ~ [= 2 3 5 * =]` syntax). The masks resemble wildcard patterns
    as used by UNIX shells. Autonomous system numbers match themselves,
    `*` matches any (even empty) sequence of arbitrary AS numbers and
    `?` matches one arbitrary AS number. For example, if `bgp_path`
    is 4 3 2 1, then: `bgp_path ~ [= * 4 3 * =]` is true,
    but `bgp_path ~ [= * 4 5 * =]` is false. There is also
    `+` operator which matches one or multiple instances of previous
    expression, e.g. `[= 1 2+ 3 =]` matches both path 1 2 3 and path
    1 2 2 2 3, but not 1 3 nor 1 2 4 3. Note that while `*` and `?`
    are wildcard-style operators, `+` is regex-style operator.

BGP mask expressions can also contain integer expressions enclosed in
    parenthesis and integer variables, for example `[= * 4 (1+2) a =]`.
    You can also use ranges (e.g. `[= * 3..5 2 100..200 * =]`)
    and sets (e.g. `[= 1 2 [3, 5, 7] * =]`).

<span id="type-clist" class="code">clist</span>  
Clist is similar to a set, except that unlike other sets, it can be
    modified. The type is used for community list (a set of pairs) and for
    cluster list (a set of quads). There exist no literals of this type.
    There are special operators on clists:

*C*`.len` returns the length of clist *C*.

*C*`.empty` makes the list *C* empty. Can't be used as a value, always modifies the object.

*C*`.add(`*P*`)` adds pair (or quad) *P* to clist *C* and
    returns the result. If item *P* is already in clist *C*, it does
    nothing. *P* may also be a clist, in that case all its members are
    added; i.e., it works as clist union.

*C*`.delete(`*P*`)` deletes pair (or quad) *P* from clist
    *C* and returns the result. If clist *C* does not contain item
    *P*, it does nothing. *P* may also be a pair (or quad) set, in that
    case the operator deletes all items from clist *C* that are also
    members of set *P*. Moreover, *P* may also be a clist, which works
    analogously; i.e., it works as clist difference.

*C*`.filter(`*P*`)` deletes all items from clist *C* that are
    not members of pair (or quad) set *P*, and returns the result. I.e., `filter` do the same
    as `delete` with inverted set *P*. *P* may also be a clist, which
    works analogously; i.e., it works as clist intersection.

Methods `add`, `delete` and `filter` keep the
    original object intact as long as you use the result in any way. You can
    also write e.g. *P*`.add(`*A*`);` as a standalone statement.
    This variant does modify the original object with the result of the operation.

*C*`.min` returns the minimum element of clist *C*.

*C*`.max` returns the maximum element of clist *C*.

Operators `.min`, `.max` can be used together with `filter`
    to extract the community from the specific subset of communities
    (e.g. localpref or prepend) without the need to check every possible
    value (e.g. `filter(bgp_community, [(23456, 1000..1099)]).min`).

<span id="type-eclist" class="code">eclist</span>  
Eclist is a data type used for BGP extended community lists. Eclists
    are very similar to clists, but they are sets of ECs instead of pairs.
    The same operations (like `add`, `delete` or `~` and
    `!~` membership operators) can be used to modify or test
    eclists, with ECs instead of pairs as arguments.

<span id="type-lclist" class="code">lclist</span>  
Lclist is a data type used for BGP large community lists. Like eclists,
    lclists are very similar to clists, but they are sets of LCs instead of
    pairs. The same operations (like `add`, `delete` or `~`
    and `!~` membership operators) can be used to modify or test
    lclists, with LCs instead of pairs as arguments.
