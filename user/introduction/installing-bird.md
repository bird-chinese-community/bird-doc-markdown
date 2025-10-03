## Installing BIRD

On a recent UNIX system with GNU development tools (GCC, binutils, m4, make)
and Perl, installing BIRD should be as easy as:


    ./configure
    make
    make install
    vi /usr/local/etc/bird.conf
    bird

You can use `./configure --help` to get a list of configure
options. The most important ones are: `--with-protocols=` to produce a slightly smaller
BIRD executable by configuring out routing protocols you don't use, and
`--prefix=` to install BIRD to a place different from `/usr/local`.
