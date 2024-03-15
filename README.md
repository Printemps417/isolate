isolate
=======

Isolate is a sandbox built to safely run untrusted executables, like
programs submitted by competitors in a programming contest. Isolate
gives them a limited-access environment, preventing them from affecting
the host system. It takes advantage of features specific to the Linux
kernel, like namespaces and control groups.

Isolate was developed by Martin Mareš (<mj@ucw.cz>) and Bernard Blackham
(<bernard@blackham.com.au>) and still maintained by the former author.
Several other people contributed patches for features and bug fixes
(see Git history for a list). Thanks!

Originally, Isolate was a part of the [Moe Contest Environment](http://www.ucw.cz/moe/),
but it evolved to a separate project used by different
contest systems, most prominently [CMS](https://github.com/cms-dev/cms).
It now lives at [GitHub](https://github.com/ioi/isolate),
where you can submit bug reports and feature requests.

If you are interested in more details, please read Martin's and Bernard's
papers on [Isolate's design](https://mj.ucw.cz/papers/isolate.pdf) and
[grading system security](https://mj.ucw.cz/papers/secgrad.pdf) published
in the Olympiads in Informatics journal.
Also, Isolate's [manual page](http://www.ucw.cz/moe/isolate.1.html)
is available online.

To compile Isolate, you need:

  - headers for the libcap library (usually available in a libcap-dev package)

  - headers for the libsystemd library (libsystemd-dev package) for compilation
    of isolate-cg-keeper

You may need `a2x` (found in [AsciiDoc](https://asciidoc-py.github.io/a2x.1.html)) for building manual.
But if you only want the isolate binary, you can just run `make isolate`

Recommended system setup is described in sections INSTALLATION and REPRODUCIBILITY
of the manual page.

runoob@LAPTOP-EUEHO147:~/repo/test$ isolate init
Please specify an isolate command (e.g. --init, --run).
Usage: isolate [<options>] <command>

Options:
--as-uid=<uid>      Perform action on behalf of a given user (requires root)
--as-gid=<gid>      Perform action on behalf of a given group (requires root)
-b, --box-id=<id>       When multiple sandboxes are used in parallel, each must get a unique ID
--cg                Enable use of control groups
--cg-mem=<size>     Limit memory usage of the control group to <size> KB
-c, --chdir=<dir>       Change directory to <dir> before executing the program
--core=<size>       Limit core files to <size> KB (default: 0)
-d, --dir=<dir>         Make a directory <dir> visible inside the sandbox
--dir=<in>=<out>    Make a directory <out> outside visible as <in> inside
--dir=<in>=         Delete a previously defined directory rule (even a default one)
--dir=...:<opt>     Specify options for a rule:
dev     Allow access to block/char devices
fs      Mount a filesystem (e.g., --dir=/proc:proc:fs)
maybe   Skip the rule if <out> does not exist
noexec  Do not allow execution of binaries
norec   Do not bind the directory recursively
rw      Allow read-write access
tmp     Create as a temporary directory (implies rw)
-D, --no-default-dirs   Do not add default directory rules
-f, --fsize=<size>      Max size (in KB) of files that can be created
-E, --env=<var>         Inherit the environment variable <var> from the parent process
-E, --env=<var>=<val>   Set the environment variable <var> to <val>; unset it if <var> is empty
-x, --extra-time=<time> Set extra timeout, before which a timing-out program is not yet killed,
so that its real execution time is reported (seconds, fractions allowed)
-e, --full-env          Inherit full environment of the parent process
--inherit-fds       Inherit all file descriptors of the parent process
-m, --mem=<size>        Limit address space to <size> KB
-M, --meta=<file>       Output process information to <file> (name:value)
-n, --open-files=<max>  Limit number of open files to <max> (default: 64, 0=unlimited)
-q, --quota=<blk>,<ino> Set disk quota to <blk> blocks and <ino> inodes
--share-net         Share network namespace with the parent process
-s, --silent            Do not print status messages except for fatal errors
--special-files     Keep non-regular files (symlinks etc.) produced inside sandbox
-k, --stack=<size>      Limit stack size to <size> KB (default: 0=unlimited)
-r, --stderr=<file>     Redirect stderr to <file>
--stderr-to-stdout  Redirect stderr to stdout
-i, --stdin=<file>      Redirect stdin from <file>
-o, --stdout=<file>     Redirect stdout to <file>
-p, --processes[=<max>] Enable multiple processes (at most <max> of them); needs --cg
-t, --time=<time>       Set run time limit (seconds, fractions allowed)
--tty-hack          Allow interactive programs in the sandbox (see man for caveats)
-v, --verbose           Be verbose (use multiple times for even more verbosity)
--wait              If the sandbox is currently busy, wait instead of refusing to run
-w, --wall-time=<time>  Set wall clock time limit (seconds, fractions allowed)

Commands:
--init              Initialize sandbox (and its control group when --cg is used)
--run -- <cmd> ...  Run given command within sandbox
--cleanup           Clean up sandbox
--print-cg-root     Print the root of cgroup hierarchy
--version           Display program version and configuration

