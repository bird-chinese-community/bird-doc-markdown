## Running BIRD

You can pass several command-line options to bird:

<span id="argv-config" class="code">-c *config name*</span>  
use given configuration file instead of *prefix*`/etc/bird.conf`.

<span id="argv-debug" class="code">-d</span>  
enable debug messages to stderr, and run bird in foreground.

<span id="argv-debug-file" class="code">-D *filename of debug log*</span>  
enable debug messages to given file.

<span id="argv-foreground" class="code">-f</span>  
run bird in foreground.

<span id="argv-group" class="code">-g *group*</span>  
use that group ID, see the next section for details.

<span id="argv-help" class="code">-h, --help</span>  
display command-line options to bird.

<span id="argv-local" class="code">-l</span>  
look for a configuration file and a communication socket in the current
    working directory instead of in default system locations. However, paths
    specified by options `-c`, `-s` have higher priority.

<span id="argv-parse" class="code">-p</span>  
just parse the config file and exit. Return value is zero if the config
    file is valid, nonzero if there are some errors.

<span id="argv-pid" class="code">-P *name of PID file*</span>  
create a PID file with given filename.

<span id="argv-recovery" class="code">-R</span>  
apply graceful restart recovery after start.

<span id="argv-socket" class="code">-s *name of communication socket*</span>  
use given filename for a socket for communications with the client,
    default is *prefix*`/var/run/bird.ctl`.

<span id="argv-user" class="code">-u *user*</span>  
drop privileges and use that user ID, see the next section for details.

<span id="argv-version" class="code">--version</span>  
display bird version.

BIRD writes messages about its work to log files or syslog (according to config).
