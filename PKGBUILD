# Maintainer: Erik Bjäreholt <erik@bjareho.lt>
# Maintainer: Brian Vuku <brayo@brayo.dev>

# PRs welcome at: https://github.com/ActivityWatch/aur-activitywatch-tauri-bin

pkgname=activitywatch-tauri-bin
pkgver='0.14.0'
pkgrel=1
pkgdesc="Track how you spend time on your computer. Simple, extensible, no third parties. (Tauri build)"
arch=('x86_64')
url="https://github.com/ActivityWatch/activitywatch"
license=('MPL-2.0')
provides=("activitywatch")
conflicts=("activitywatch")
depends=(
    'gtk3'
    'webkit2gtk-4.1'
    'libayatana-appindicator'
)
options=('!strip' '!debug')
source=("https://github.com/ActivityWatch/activitywatch/releases/download/v${pkgver}/activitywatch-tauri-v${pkgver}-linux-x86_64.zip")
sha256sums=('335eb47a3b44351e6553f685646d2727cff577e67e72829ab9a274a963aed175')

package() {
    # aw-tauri itself ships as a .deb inside the zip: /usr/bin/aw-tauri,
    # its .desktop file and icons
    bsdtar -xOf activitywatch/aw-tauri/aw-tauri.deb 'data.tar.*' | bsdtar -xf - -C "$pkgdir"

    # Install the modules where aw-tauri looks for bundled modules
    # (<prefix>/lib/aw-tauri/modules, relative to /usr/bin/aw-tauri)
    moddir="$pkgdir/usr/lib/aw-tauri/modules"
    mkdir -p "$moddir"
    for dir in activitywatch/aw-*/; do
        dir=$(basename "$dir")
        [ "$dir" == "aw-tauri" ] && continue
        cp -r "activitywatch/$dir" "$moddir"
    done
    # aw-tauri picks aw-awatcher over the Python watchers when it's available
    cp -r activitywatch/awatcher "$moddir"

    # Symlink module executables to /usr/bin
    modulenames=("aw-watcher-afk" "aw-watcher-window" "aw-watcher-input" "aw-notify" "aw-server-rust/aw-sync" "awatcher/aw-awatcher")
    for name in "${modulenames[@]}"; do
        # if a module has a path, use that,
        # else assume its in a dir with the same name as the module
        dir=$(dirname "$name")
        if [ "$dir" == "." ]; then
            dir=$name
        else
            # strip the path from the name
            name=$(basename "$name")
        fi
        # check that the module exists
        if [ ! -f "$moddir/$dir/$name" ]; then
            echo "WARNING: $dir/$name does not exist, skipping"
            continue
        fi
        ln -s "/usr/lib/aw-tauri/modules/$dir/$name" "$pkgdir/usr/bin/$name"
    done
}
