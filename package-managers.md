# There a 3 main components
- the locally installed packages + a cached list of available packages
- the package manager program
- the remote registries that contain the actual software together with their metadata(size, deps, desc etc.)

# Init
1. Setup local keyring(see below)
2. Setup local package index from remote repos. This is what allows local available package lookup.

# Actions
## Install
Manager gets the link from its cached metadata for where to download the package from. It downloads it, installs it(extracts, moves files around as dictated by the package), and notes down the installed files per package basis so it can remove these files when prompted to uninstall the package. "Notes down" is usually done in some kind of DB.
## Remove
Based on the local DB it checks which files the package created when it got installed, and removes them.
## Update
Each package has some unique, non-changing identifier so it's matchable through version changes so the manager knows which previous installed version to uninstall and which new one to install. It then does the same dance as when installing: download new version, install, note down the files. and also uninstall the previous version too.

# Dependency resolution
Packages usually depend on other packages, these are listed in its metadata file. They are resolved(installed like any other package) at install time when installing the parent package.

# Keyring
A database of public keys of trusted repo maintainers(also package authors in pacman). When DB is updating its local package index it confirms the index's integrity by comparing `Release` file's bytehash to the decrypted `Release.gpg`. If it's equal, the index is trusted - it contains hashes of each package. When a package is being installed, it's hash is compared against the one in the trusted index.

| Component | pacman | apt |
|---|---|---|
| Local package DB | `/var/lib/pacman/local/` | `/var/lib/dpkg/status` |
| Cached repo index | `/var/lib/pacman/sync/` (`.db` per repo) | `/var/lib/apt/lists/` (flat files per repo) |
| Repo configuration | `/etc/pacman.conf` + `/etc/pacman.d/mirrorlist` | `/etc/apt/sources.list` + `/etc/apt/sources.list.d/` |
| GPG keyring | `/etc/pacman.d/gnupg/` | `/etc/apt/keyrings/` + `/etc/apt/trusted.gpg.d/` |
| Per-package signatures | `.sig` fetched alongside each `.pkg.tar.zst` | not standard |
| Repo-level signature | `core.db.sig` alongside each `.db` | `Release` + `Release.gpg` per repo |

> [!Note]
> OpenPGP - the standard derived from PGP(Pretty Good Privacy), GnuPG(GPG) - an implementation of OpenPGP.
