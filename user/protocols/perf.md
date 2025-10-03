## Perf

### Introduction

The Perf protocol is a generator of fake routes together with a time measurement
framework. Its purpose is to check BIRD performance and to benchmark filters.

Import mode of this protocol runs in several steps. In each step, it generates 2^x routes,
imports them into the appropriate table and withdraws them. The exponent x is configurable.
It runs the benchmark several times for the same x, then it increases x by one
until it gets too high, then it stops.

Export mode of this protocol repeats route refresh from table and measures how long it takes.

Output data is logged on info level. There is a Perl script `proto/perf/parse.pl`
which may be handy to parse the data and draw some plots.

Implementation of this protocol is experimental. Use with caution and do not keep
any instance of Perf in production configs for long time. The config interface is also unstable
and may change in future versions without warning.

### Configuration

<span id="perf-mode" class="code">mode import\|export</span>  
Set perf mode. Default: import

<span id="perf-repeat" class="code">repeat *number*</span>  
Run this amount of iterations of the benchmark for every amount step. Default: 4

<span id="perf-from" class="code">exp from *number*</span>  
Begin benchmarking on this exponent for number of generated routes in one step.
    Default: 10

<span id="perf-to" class="code">exp to *number*</span>  
Stop benchmarking on this exponent. Default: 20

<span id="perf-threshold-min" class="code">threshold min *time*</span>  
If a run for the given exponent took less than this time for route import,
    increase the exponent immediately. Default: 1 ms

<span id="perf-threshold-max" class="code">threshold max *time*</span>  
If every run for the given exponent took at least this time for route import,
    stop benchmarking. Default: 500 ms
