## Aggregator

### Introduction

The Aggregator protocol explicitly merges routes by the given rules. There
   are four phases of aggregation. First routes are filtered, then sorted into buckets,
   then buckets are merged and finally the results are filtered once again.
   Aggregating an already aggregated route is forbidden.

This is an experimental protocol, use with caution.

### Configuration

<span id="aggregator-table" class="code">table *table*</span>  
The table from which routes are exported to get aggregated.

<span id="aggregator-export" class="code">export *...*</span>  
A standard channel's `export` clause, defining which routes are accepted into aggregation.

<span id="aggregator-rule" class="code">aggregate on *expr* \| *attribute* \[*, ...*\]</span>  
All the given filter expressions and route attributes are evaluated for each route. Then routes
    are sorted into buckets where **all** values are the same. Note: due to performance reasons,
    all filter expressions must return a compact type, e.g. integer, a BGP
    (standard, extended, large) community or an IP address. If you need to compare e.g. modified
    AS Paths in the aggregation rule, you can define a custom route attribute and set this attribute
    in the export filter. For now, it's mandatory to say `net` here, we can't merge prefixes yet.

<span id="aggregation-merge" class="code">merge by { *filter code* }</span>  
The given filter code has an extra symbol defined: `routes`. By iterating over `routes`,
    you get all the routes in the bucket and you can construct your new route. All attributes
    selected in `aggregate on` are already set to the common values. For now, it's not possible
    to use a named filter here. You have to finalize the route by calling `accept`.

<span id="aggregator-import" class="code">import *...*</span>  
Filter applied to the route after `merge by`. Here you can use a named filter.

<span id="aggregator-peer-table" class="code">peer table *table*</span>  
The table to which aggregated routes are imported. It may be the same table
    as `table`.

### Example


    protocol aggregator {
      table master6;
      export where defined(bgp_path);
      /* Merge all routes with the same AS Path length */
      aggregate on net, bgp_path.len;
      merge by {
        for route r in routes do {
          if ! defined(bgp_path) then { bgp_path = r.bgp_path }
          bgp_community = bgp_community.add(r.bgp_community);
        }
        accept;
      };
      import all;
      peer table agr_result;
    }
