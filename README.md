Linux Security Module Subsystem
=============================================================================
https://git.kernel.org/pub/scm/linux/kernel/git/pcmoore/lsm.git  
https://github.com/LinuxSecurityModule/kernel

The Linux Security Module (LSM) subsystem is a modular access control framework
design to allow multiple security models to be implemented inside of the Linux
Kernel; popular LSMs include SELinux, AppArmor, and Smack.

The main Linux Kernel README can be found at
[Documentation/admin-guide/README.rst](./Documentation/admin-guide/README.rst)

## Online Resources

The canonical SELinux kernel repository is hosted by kernel.org:

* https://git.kernel.org/pub/scm/linux/kernel/git/pcmoore/lsm.git
* git://git.kernel.org/pub/scm/linux/kernel/git/pcmoore/lsm.git

There is also an officially maintained GitHub mirror:

* https://github.com/LinuxSecurityModule/kernel

The Linux Security Module (LSM) mailing list can be found at the link below:
* http://vger.kernel.org/vger-lists.html#linux-security-module

The official (LSM) mailing list archive can be found at the link below:
* https://lore.kernel.org/linux-security-module

## Kernel Tree Process

After the merge window closes upstream, a decision will be made regarding the
need to rebase the next branch on top of the current Linux -rc1 release. If
there have been a number of subsystem related changes outside of the
subsystem's next branch, or if the branch's base is too far behind
linux/master, it may be necessary to rebase the next branch. If a rebase is
needed, it should be done before any patches are merged, and rebasing the next
branch during the remaining -rcX releases should only be done in extreme cases.

Patches will be merged into the subsystem's next branch during the development
cycle which extends from merge window close up until the merge window reopens.
However, it is important to note that large, complicated, or invasive patches
sent late in the development cycle may be deferred until the next cycle. As a
general rule, only small patches or critical fixes will be merged after
-rc5/-rc6.

Any patches deemed necessary for the current Linux -rcX releases will be merged
into the current stable-X.Y branch, marked with a signed tag, and a pull
request sent against linux/master as soon as it is reasonable to do so.

During the development cycle Fedora Rawhide test kernels will be generated
using the next and most recent stable-X.Y branches on a weekly basis, if not
more often. These kernels will be tested against the SELinux test suite and
audit test suite as well as being made available to everyone for additional
testing.

Once the merge window opens, the next branch will be copied to a new branch,
stable-X.Y, and the branch will be marked with a signed tag in the format
selinux-pr-YYYYMMDD. A pull request will be sent against the linux/master
branch using the signed tag.
