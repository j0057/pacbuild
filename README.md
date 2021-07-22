# pacbuild

`pacbuild` maintains a custom Pacman repository, where PKGBUILDs are Git
submodules that come from [AUR][aur], [svntogit-packages][sgp],
[svntogit-community][sgc], or really any other git repo.

[aur]: https://aur.archlinux.org/
[sgp]: https://github.com/archlinux/svntogit-packages
[sgc]: https://github.com/archlinux/svntogit-community

## Dependencies in Arch

`pacbuild` depends on `devtools` and the group `base-devel`. 

## Configuring/bootstrapping

Create a new Git repository and add this repository as a submodule:

    git init aur64
    cd aur64
    git submodule add https://github.com/j0057/pacbuild

Add a configuration file called `pacbuild.conf`:

    NAME=aur64
    LIB=/var/lib/aur64

Where `NAME` is the name you wish to use in Pacman, and `LIB` is the path
various artifacts will be stored:

    /var/lib/aur64
    ├── build
    │   └── ...
    ├── log
    │   └── ...
    ├── pkg
    │   ├── aur64.db -> aur64.db.tar.gz
    │   ├── aur64.db.tar.gz
    │   ├── aur64.files -> aur64.files.tar.gz
    │   ├── aur64.files.tar.gz
    │   └── pacbuild-git-0.1.27.gaddf8af-1-any.pkg.tar.zst
    └── src
        └── ...

Where `build` will store the chroots for `arch-nspawn` and `mkchrootpkg`, `log`
will store the build log files, `pkg` will be the actual Pacman repo, and `src`
will store the source files.

Next, to bootstrap, build the repo by calling `pacbuild` directly inside the
submodule, and then install pacbuild:

    ./pacbuild/pacbuild
    sudo pacman -Syu pacbuild

From now on, you can run `pacbuild` from anywhere, though it will bail out if
you run it in a directory that doesn't contain a `pacbuild.conf`.

## Adding and updating PKGBUILDs

TL;DR: it's just Git submodules.

### Adding and updating packages from the AUR

Adding a PKGBUILD from the AUR is done by adding a Git submodule:

    git submodule add https://aur.archlinux.org/minecraft-launcher

To update the PKGBUILDs and importantly -- review the incoming changes:

    git submodule update --remote
    git submodule summary 
    git diff --cached --submodule=diff

With `--submodule=log`, you get an overview of incoming commit messages; with
`--submodule=diff`, the actual changes in the PKGBUILD and supporting files.
If the changes are OK, commit them and build them.

### Adding and updating packages from svntogit

Adding a PKGBUILD from svntogit is done by cloning svntogit-packages
to a dedicated spot, and referencing this repo using the `--reference`
option of [git submodule add][gs], which is actually documented
on the [git clone][gc] man page:

    (cd ..; git submodule clone https://github.com/archlinux/svntogit-packages)

    git submodule add --reference ../svntogit-packages --branch packages/zsh https://github.com/archlinux/svntogit-packages zsh
    git submodule add --reference ../svntogit-packages --branch packages/bash https://github.com/archlinux/svntogit-packages bash

When updating, first fetch the dedicated svntogit-packages repository, then
do the `git submodule update --remote` as normal to avoid downloading everything
multiple times.

Building packages from the core/extra/community/multilib repos is not a central
use case for me, so I don't really care that this is a bit clunky.

[gs]: https://git-scm.com/docs/git-submodule
[gc]: https://git-scm.com/docs/git-clone

### Adding a PKGBUILD from any other Git repository

Any other repo that contains a PKGBUILD can be added as a submodule:

    git submodule add https://github.com/j0057/build-repo

Updating works the same as for AUR packages.

## Removing a package from the repo

This is a D.I.Y. process:

- Use [repo-add/repo-remove][ra] on the repo file.
- Remove the package file with `rm`. (The `-R` option does not apply to
  `repo-remove`.)
- Use `git rm -r` to remove the submodule, and your favorite `$EDITOR` to
  remove it from `.gitmodules`.

[ra]: https://man.archlinux.org/man/repo-add.8.en

## Dealing with GPG keys

I searched high and low for how to pass some GPG keys to `mkchrootpkg`, and the
answer is... add them to your own keyring, outside the build container. Please
let me know if me telling you this essential information has saved you from
wasting four hours of reading source code.
