## Control structures

Filters support several control structures: conditions, for loops and case
switches.

Syntax of a condition is: `if `*boolean expression*` then `*commandT*`;
else `*commandF*`;` and you can use `{ `*command1*`; `*command2*`;
`*...*` }` instead of either command. The `else` clause may be
omitted. If the *boolean expression* is true, *commandT* is
executed, otherwise *commandF* is executed.

For loops allow to iterate over elements in compound data like BGP paths or
community lists. The syntax is: `for [ `*type*` ] `*variable*` in `*expr*`
do `*command*`;` and you can also use compound command like in conditions.
The expression is evaluated to a compound data, then for each element from such
data the command is executed with the item assigned to the variable. A variable
may be an existing one (when just name is used) or a locally defined (when type
and name is used). In both cases, it must have the same type as elements.

The `case` is similar to case from Pascal. Syntax is `case
`*expr*` { else: | `*set_body_expr*` /: `*statement*` ; [... ] }`.
The expression after `case` can be of any type that could be a member of
a set, while the *set_body_expr* before `:` can be anything (constants,
intervals, expressions) that could be a part of a set literal. One exception is
prefix type, which can be used in sets bud not in `case` structure. Multiple
commands must be grouped by `{}`. If *expr* matches one
of the `:` clauses, the statement or block after it is
executed. If *expr* matches neither of the `:` clauses, the
statement or block after `else:` is executed.

Here is example that uses `if` and `case` structures:


    if 1234 = i then printn "."; else {
        print "not 1234";
        print "You need {} around multiple commands";
    }

    for int asn in bgp_path do {
        printn "ASN: ", asn;
        if asn < 65536 then print " (2B)"; else print " (4B)";
    }

    case arg1 {
        2: { print "two"; print "Multiple commands must brace themselves."; }
        3 .. 5: print "three to five";
        else: print "something else";
    }
