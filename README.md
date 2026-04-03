# sshfs_duo
A patch for sshfs to support duo mobile 2FA


Original project source: https://github.com/winfsp/sshfs-win

The sshfs.exe in this directory is the version compiled with Duo 2FA support.
Drop this file in C:\Program Files\SSHFS-Win\bin after installing sshfs-win
and Duo support should be functional. (Make a backup of the original just
in case.)

To build sshfs.exe with Duo 2FA support, just follow the instructions for
setting up the build environment on GitHub, drop the file 40-duoauth.patch
into the 'patches' subdirectory, and 'make' the project. Also included here
is the full sshfs-duo.c with changes that was used to generate the patch file
using:

diff -u sshfs/sshfs.c ./sshfs-duo.c > patches/40-duoauth.patch

The current patch is hard-coded and not very generalizable. It requires
a Duo prompt that ends with "(1-1):" and will only respond with "1\n".

If Duo auth gets more complex than that, this code will require updating
and recompiling. In principle, the Duo challenge and response could be passed
in as parameters (-o duoChallenge="(1-1):"). In practice, this means updating
the sshfs-win.c wrapper to handle the new options, which may not be a major
project, but since sshfs-win.exe is most easily invoked from the command line
via 'net use', you would also have to find a way to pass the parameters
through net use unharmed. It won't just pass through arbitrary options, so
as far as I can see, the only real way to do it is to pass the options in by
manipulating the sort of phony UNC path given to net use for sshfs mounts.
So you could form the path as something like \\sshfs\!(1-1):!1!flathers@...
and then munge that through sshfs-win to pass it into sshfs.
