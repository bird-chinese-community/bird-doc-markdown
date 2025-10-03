## Introduction

BIRD contains a simple programming language. (No, it can't yet read mail :-).
There are two objects in this language: filters and functions. Filters are
interpreted by BIRD core when a route is being passed between protocols and
routing tables. The filter language contains control structures such as if's and
switches, but it allows no loops. An example of a filter using many features can
be found in `filter/test.conf`.

Filter gets the route, looks at its attributes and modifies some of them if
it wishes. At the end, it decides whether to pass the changed route through
(using `accept`) or whether to `reject` it. A simple filter looks like
this:


    filter not_too_far
    {
        int var;
        if defined( rip_metric ) then
            var = rip_metric;
        else {
            var = 1;
            rip_metric = 1;
        }
        if rip_metric &gt; 10 then
            reject "RIP metric is too big";
        else
            accept "ok";
    }

As you can see, a filter has a header, a list of local variables, and a body.
The header consists of the `filter` keyword followed by a (unique) name of
filter. The list of local variables consists of *type name*`;`
pairs where each pair declares one local variable. The body consists of `{ `*statements*` }`. Each *statement* is terminated by a `;`. You
can group several statements to a single compound statement by using braces
(`{ `*statements*` }`) which is useful if you want to make a bigger
block of code conditional.

BIRD supports functions, so that you don not have to repeat the same blocks
of code over and over. Functions can have zero or more parameters and they can
have local variables. If the function returns value, then you should always
specify its return type. Direct recursion is possible. Function definitions look
like this:


    function name() -> int
    {
        int local_variable;
        int another_variable = 5;
        return 42;
    }

    function with_parameters(int parameter) -> pair
    {
        print parameter;
        return (1, 2);
    }

Like in C programming language, variables are declared inside function body,
either at the beginning, or mixed with other statements. Declarations may
contain initialization. You can also declare variables in nested blocks, such
variables have scope restricted to such block. There is a deprecated syntax to
declare variables after the `function` line, but before the first `{`.
Functions are called like in C: `name(); with_parameters(5);`. Function
may return values using the `return `*\[expr\]* command. Returning a
value exits from current function (this is similar to C).

Filters are defined in a way similar to functions except they cannot have
explicit parameters and cannot return. They get a route table entry as an implicit parameter, it
is also passed automatically to any functions called. The filter must terminate
with either `accept` or `reject` statement. If there is a runtime error in
filter, the route is rejected.

A nice trick to debug filters is to use `show route filter `*name*
from the command line client. An example session might look like:


    pavel@bug:~/bird$ ./birdc -s bird.ctl
    BIRD 0.0.0 ready.
    bird> show route
    10.0.0.0/8         dev eth0 [direct1 23:21] (240)
    195.113.30.2/32    dev tunl1 [direct1 23:21] (240)
    127.0.0.0/8        dev lo [direct1 23:21] (240)
    bird> show route ?
    show route [<prefix>] [table <t>] [filter <f>] [all] [primary]...
    bird> show route filter { if 127.0.0.5 &tilde; net then accept; }
    127.0.0.0/8        dev lo [direct1 23:21] (240)
    bird>
