aur-activitywatch-tauri-bin
===========================

An AUR package for the Tauri build of ActivityWatch (`aw-tauri`) using prebuilt binaries.

For the Qt build (`aw-qt`), see [activitywatch-bin](https://github.com/ActivityWatch/aur-activitywatch-bin).
The two packages conflict, so only one can be installed at a time.

Modules are installed to `/usr/lib/aw-tauri/modules`, where aw-tauri discovers them,
so there's no need to run `move-to-aw-modules.sh` from the release zip.


## How to update the AUR package

You need:
 - Maintainer rights to the AUR package
 - Your AUR ssh key configured

After modifying `PKGBUILD` as appropriate, and testing the package on your machine, run the following to:
 - build the package
   - updates checksums with `updpkgsums`
   - regenerates `.SRCINFO` with `makepkg --printsrcinfo > .SRCINFO`
 - commit the changes
 - push to the AUR
```sh
make package
git add PKGBUILD .SRCINFO
git commit -m "Updated .SRCINFO"
git remote add aur aur@aur.archlinux.org/activitywatch-tauri-bin.git
git push aur
```
