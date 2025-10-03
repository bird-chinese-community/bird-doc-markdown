## BMP

The BGP Monitoring Protocol is used for monitoring BGP sessions and obtaining
routing table data. The current implementation in BIRD is a preliminary release
with a limited feature set, it will be subject to significant changes in the
future. It is not ready for production usage and therefore it is not compiled
by default and have to be enabled during installation by the configure option
`--with-protocols=`.

The implementation supports monitoring protocol state changes, pre-policy
routes (in [BGP import tables](#bgp-import-table)) and post-policy
routes (in regular routing tables). All BGP protocols are monitored automatically.

### Configuration (incomplete)

<span id="bmp-tx-buffer-limit" class="code">tx buffer limit *number*</span>  
How much data we are going to queue before we call the session stuck
    and restart it, in megabytes. Default value: 1024 (effectively 1 gigabyte).

### Example


    protocol bmp {
        # The monitoring station to connect to
        station address ip 198.51.100.10 port 1790;

        # Monitor received routes (in import table)
        monitoring rib in pre_policy;

        # Monitor accepted routes (passed import filters)
        monitoring rib in post_policy;

        # Allow only 64M of pending data
        tx buffer limit 64;
    }
