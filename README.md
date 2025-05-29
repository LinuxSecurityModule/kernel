Linux Security Module Subsystem
=============================================================================
https://git.kernel.org/pub/scm/linux/kernel/git/pcmoore/lsm.git  
https://github.com/LinuxSecurityModule/kernel

The Linux Security Module (LSM) subsystem is a modular access control framework
design to allow multiple security models to be implemented inside of the Linux
kernel; popular LSMs include SELinux, AppArmor, and Smack.

The main Linux kernel README can be found at
[Documentation/admin-guide/README.rst](./Documentation/admin-guide/README.rst)

## Online Resources

The canonical LSM kernel repository is hosted by kernel.org:

* https://git.kernel.org/pub/scm/linux/kernel/git/pcmoore/lsm.git
* git://git.kernel.org/pub/scm/linux/kernel/git/pcmoore/lsm.git

There is also an officially maintained GitHub mirror:

* https://github.com/LinuxSecurityModule/kernel

The LSM mailing list can be found at the link below:
* http://vger.kernel.org/vger-lists.html#linux-security-module

The official LSM mailing list archive can be found at the link below:
* https://lore.kernel.org/linux-security-module

## Kernel Source Branches and Development Process

### Kernel Source Branches

There are four primary git branches associated with the development process:
stable-X.Y, dev, dev-staging, and next.  In addition to these four primary
branches there are also topic specific, work in progress branches that start
with a "working-" prefix; these branches can generally be ignored unless you
happen to be involved in the development of that particular topic.  The
management of these topic branches can vary depending on a number of factors,
but the details of each branch will be communicated in the relevant discussion
threads on the upstream mailing list.

#### stable-X.Y branch

The stable-X.Y branch is intended for stable kernel patches and is based on
Linus' X.Y-rc1 tag, or a later X.Y.Z stable kernel release tag as needed.
If serious problems are identified and a patch is developed during the kernel's
release candidate cycle, it may be a candidate for stable kernel marking and
inclusion into the stable-X.Y branch.  The main Linux kernel's documentation
on stable kernel patches has more information both on what patches may be
stable kernel candidates, and how to mark those patches appropriately; upstream
mailing list discussions on the merits of marking the patch for stable can also
be expected.  Once a patch has been merged into the stable-X.Y branch and spent
a day or two in the next branch (see the next branch notes), it will be sent to
Linus for merging into the next release candidate or final kernel release (see
the notes on pull requests in this document).  If the patch has been properly
marked for stable, the other stable kernel trees will attempt to backport the
patch as soon as it is present in Linus' tree, see the main Linux kernel
documentation for more details.

Unless specifically requested, developers should not base their patches on the
stable-X.Y branch.  Any merge conflicts that arise from merging patches
submitted upstream will be handled by the maintainer, although help and/or may
be requested in extreme cases.

#### dev branch

The dev branch is intended for development patches targeting the upcoming merge
window, and is based on Linus' latest X.Y-rc1 tag, or a later rc tag as needed
to avoid serious bugs, merge conflicts, or other significant problems.  This
branch is the primary development branch where the majority of patches are
merged during the normal kernel development cycle.  Patches merged into the
dev branch will be present in the next branch (see the next branch notes) and
will be sent to Linus during the next merge window.

Developers should use the dev branch as a stable basis for their own
development work, only under extreme circumstances will the dev branch be
rebased during the X.Y-rc cycle and the maintainer will be responsible for
resolving any merge conflicts, although help and/or may be requested in extreme
cases.

#### dev-staging branch

The dev-staging branch is intended for development patches that are not
targeting a specific merge window.  The dev-staging branch exists as a staging
area for the main dev branch and as such its use will be unpredictable and it
will be rebased as needed.  Patches merged into the dev-staging branch should
find their way into the primary dev branch at some point in the future,
although that is not guaranteed.

Unless specifically requested, developers should not use the dev-staging branch
as a basis for any development work.

#### next branch

The next branch is a composite branch built by merging the latest stable-X.Y
and dev branches in that order.  The main focus of the next branch is to
provide a single branch for linux-next integration testing that contains all of
the commits from the component branches.  The next branch will be updated
whenever there is a change to any one of the component branches, but it will
remain frozen during the merge window so as to cooperate with the wishes of the
linux-next team.

While developers can use the next branch as a basis for development, the dev
branch would likely be a more suitable, and stable, base.

### Kernel Development Process

After Linus closes the kernel merge window upstream, the stable-X.Y branch
associated with the current kernel release candidate, the dev branch, and
potentially the dev-staging branch (see the dev-staging branch notes) will
be reset to match the latest vX.Y-rc1 tag in Linus' tree.  The next branch, as
a composite branch composed from these branches, will be updated as a result.

During the development cycle that starts with the close of the kernel merge
window and ends with the tagged kernel release, patches will be accepted into
the stable-X.Y and dev branches as described in their respective sections in
this document.  While patches will be accepted into the stable-X.Y branch at
any point in time, significant changes will likely not be accepted into the dev
branch when there are two or less weeks left in the development cycle; this
typically means that only critical bugfixes are accepted once the vX.Y-rc6
kernel is released.  During this time the next branch will be regenerated on an
as needed basis based on changes in the component branches, and pull requests
will be sent as needed to Linus for patches in the stable-X.Y branch.

Once Linus releases the final vX.Y kernel and the merge window opens, two
things will happen.  The first is that the dev branch will be duplicated into
a new stable-X'.Y' branch, representing the new upcoming kernel release, and
the second is that a pull request will be sent from this branch for inclusion
into the current merge window.  During the merge window process the dev and
next branches should be frozen, although there is a possibility that some
patches may be merged into dev-staging for testing or process related reasons.

#### Pull Requests for Linus

In order to send a pull request to Linus, either for a critical bugfix or as
part of the merge window, a signed git tag must be created that points to the
pull request point.  The tag should be named using the "{subsystem}-pr-{date}"
format and can be generated with the following git command:

```
% git tag -s -m "{subsystem}/stable-X'.Y' PR {date}" {subsystem}-pr-{date}
```

Once the signed tag has been created, it should be used as the basis for the
pull request.

## LSM Development and Process Guidelines

## New LSM Hooks

While LSM hooks are considered outside of the Linux kernel's stable API
promise, in order to limit unnecessary churn within the kernel we do try to
minimize changes to the set of LSM hooks.  With that in mind, we have the
following requirements for new LSM hooks:

* Hooks should be designed to be LSM agnostic.  While it is possible that only
one LSM might implement the hook at the time of submission, the hook's behavior
should be generic enough that other LSMs could provide a meaningful
implementation.

* The hook must be documented with a function header block that conforms to
the kernel documentation style.  At a minimum the documentation should explain
the parameters, return values, a brief overall description, any special
considerations for the callers, and any special considerations for the LSM
implementations.

* New LSM hooks must demonstrate their usefulness by providing a meaningful
implementation for at least one in-kernel LSM.  The goal is to demonstrate the
purpose and expected semantics of the hooks.  Out of tree kernel code, and pass
through implementations, such as the BPF LSM, are not eligible for LSM hook
reference implementations.

It is important to note that these requirements are not complete, due to the
ever changing nature of the Linux kernel and the unique nature of each LSM
hook.  Ultimately, new LSM hooks are added to the kernel at the discretion of
the maintainers and reviewers.

## Modifying LSM Hooks

Changing the parameters, return value, or calling locations of an existing LSM
hook can impact individual in-kernel LSMs as well as ongoing development
efforts that have not yet landed in the upstream Linux kernel.  Developers that
wish to change a LSM hook should work with existing in-kernel LSMs, related
development efforts, and any associated subsystems to ensure that the
individual LSMs are properly transitioned to the new hook.  Any existing kernel
selftests should also be updated as necessary to ensure they continue to
work properly.

Due to the potential for cross LSM and cross subsystem conflicts when changing
LSM hooks, it is possible that patches which modify LSM hooks may be staged, or
delayed, for a period of time to allow for a more manageable transition.

## Removing LSM Hooks

If a LSM hook is no longer used by any in-kernel LSMs, there is no ongoing work
in progress involving the hook, and no expectation of future work that will use
the hook, the LSM community may consider removal of the LSM hook.  The decision
to ultimately remove the LSM hook should balance ongoing maintenance and
performance concerns with the social challenges of reintroducing the hook if
it is needed at a later date.

In the case where a Linux kernel selftest has been created to verify the proper
behavior of a LSM hook, the presence of the selftest alone should not prevent
removal of the LSM hook.

## New LSMs

Historically we have had few requirements around new LSM additions, with
Arjan van de Ven being the first to describe a basic protocol for accepting new
LSMs into the Linux kernel[^1].  In an attempt to document Arjan's basic ideas
and update them for modern Linux kernel development, here are a list of
requirements for new LSM submissions:

* The new LSM's author(s) must commit to maintain and support the new LSM for
an extended period of time; this applies both to authors that are employed to
develop and maintain a LSM as well as those that develop and maintain a LSM on
their own time.  If the authors are currently supporting a LSM as part of their
employment, there is an expectation upstream that support will continue beyond
the authors' tenure at their current company.  In either case, if the authors
are unable to commit to supporting the LSM for an extended period of time, a
reasonable succession plan must be submitted along with the LSM.

* The new LSM must be sufficiently unique to justify the additional work
involved in reviewing, maintaining, and supporting the LSM.  It is reasonable
for there to be a level of overlap between LSMs, but either the security model
or the admin/user experience must be significantly unique.

* New LSMs must include documentation providing a clear explanation of the
LSM's requirements, goals, and expected uses.  The documentation does not need
to rise to the level of a formal security model, but it should include a basic
threat model with a description of the mitigations provided by the LSM.  Both
the threat model and the LSM mitigations must be considered "reasonable" by
the LSM community as a whole.

* Any user visible interfaces provided by the LSM must be well documented.  It
is important to remember the user visible APIs are considered to be "forever
APIs" by the Linux kernel community; do not add an API that cannot be supported
for the next 20+ years.

* New LSMs must be accompanied by a test suite to verify basic functionality
and help identify regressions.  The test suite must be publicly available
without download restrictions requiring accounts, subscriptions, etc.  Test
coverage does not need to reach a specific percentage, but core functionality
and any user interfaces should be well covered by the test suite.  Maintaining
the test suite in a public git repository is preferable over tarball snapshots.
Integrating the test suite with existing automated Linux kernel testing
services is encouraged.

* The LSM implementation must follow general Linux kernel coding practices,
faithfully implement the security model and APIs described in the
documentation, and be free of any known defects at the time of submission.

* If new userspace tools, or patches to existing tools, are necessary to
configure, operate, or otherwise manage the LSM, these tools or patches must
be publicly available without download restrictions requiring accounts,
subscriptions, etc.  Maintaining these tools or patches in a public git
repository is preferable over tarball snapshots.

It is important to note that these requirements are not complete, due to the
ever changing nature of the Linux kernel and the unique nature of each LSM.
Ultimately, new LSMs are added to the kernel at the discretion of the
maintainers and reviewers.

[^1]: https://lore.kernel.org/all/20071026141358.38342c0f@laptopd505.fenrus.org

## Removing LSMs

If a LSM has not been actively maintained for a period of time such that it is
becoming a maintenance burden for other developers, or there are serious
concerns about the LSM's ability to deliver on its stated purpose, the LSM
community may consider deprecating and ultimately removing the LSM from the
Linux kernel.  However, before considering deprecation, the LSM community
should make every reasonable effort to find a suitable maintainer for the LSM
while also surveying the major Linux distributions to better understand the
impact a deprecation would have on the downstream distro/user experience.  If
deprecation remains the only viable option, the following process should be
used as a starting point for deprecating the LSM:

* The LSM's Kconfig description should indicate that the LSM is being
deprecated and the LSM should not be built into the kernel by default.

* Entries in Documentation/API/obsolete should be created for any user visible
interfaces associated with the LSM.

* When the LSM is enabled at boot or runtime, it should display a message on
the console that it is now deprecated and will be removed at some point in the
future.  While the message should be displayed without delaying the boot at
first, after one or two kernel releases it may be helpful to add a small,
e.g. five second, delay after displaying the message to draw attention to the
deprecation notice.  The delay can be increased in successive kernel releases
until it reaches a level than any reasonable user wouldn't be able to ignore,
e.g. 30 seconds.

* Finally, after an additional two to three kernel releases, after any
deadlines listed in the Documentation/API/obsolete entries, and once the LSM
community is satisfied that all users running modern kernels have migrated away
from the LSM, the LSM can be removed from the Linux kernel and any entries in
Documentation/API/obsolete can be moved to Documentation/API/removed.

It is important to note that the steps above are intended as basic guidance for
a generic LSM; it is likely that changes, including additional actions, will be
needed for individual LSMs based on their design, implementation, and
downstream usage.  The LSM community should take the process above as input,
but ultimately the process should be tailored to the LSM being deprecated and
the associated environment.
