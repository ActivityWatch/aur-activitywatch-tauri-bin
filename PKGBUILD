# Maintainer: Erik Bjäreholt <erik@bjareho.lt>
# Maintainer: Brian Vuku <brayo@brayo.dev>

# PRs welcome at: https://github.com/ActivityWatch/aur-activitywatch-tauri-bin

pkgname=activitywatch-tauri-bin
pkgver='0.14.3'
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
    # aw-notify links the system libssl.so.3 (Arch's openssl is now 4.x)
    'openssl-3'
)
options=('!strip' '!debug')
source=("https://github.com/ActivityWatch/activitywatch/releases/download/v${pkgver}/activitywatch-tauri-v${pkgver}-linux-x86_64.zip")
sha256sums=('9891ec6dbdbbf4bcab5ba554e588c28a3aee2071b2df408f590cb0eccf61fcca')

package() {
    # aw-tauri itself ships as a .deb inside the zip: /usr/bin/aw-tauri,
    # its .desktop file and icons, plus the Rust modules it bundles as flat
    # executables in /usr/lib/aw-tauri/modules (aw-awatcher, aw-notify, aw-sync)
    bsdtar -xOf activitywatch/aw-tauri/aw-tauri.deb 'data.tar.*' | bsdtar -xf - -C "$pkgdir"

    # Add the Python watchers next to them. aw-tauri discovers aw-* executables
    # in <prefix>/lib/aw-tauri/modules and in its aw-* subdirectories.
    moddir="$pkgdir/usr/lib/aw-tauri/modules"
    for dir in activitywatch/aw-*/; do
        name=$(basename "$dir")
        # aw-tauri itself, and aw-server-rust (only aw-sync, which the .deb bundles)
        case "$name" in aw-tauri|aw-server-rust) continue ;; esac
        # already bundled by the .deb (aw-notify)
        [ -e "$moddir/$name" ] && continue
        cp -r "$dir" "$moddir"
    done

    # Check the installed layout in $pkgdir and symlink module executables to /usr/bin.
    # Fail on a missing module: a silent skip would ship a package without a watcher.
    modules=("aw-awatcher" "aw-notify" "aw-sync" "aw-watcher-afk/aw-watcher-afk" "aw-watcher-window/aw-watcher-window" "aw-watcher-input/aw-watcher-input")
    for module in "${modules[@]}"; do
        if [ ! -x "$moddir/$module" ]; then
            echo "ERROR: $moddir/$module is missing or not executable" >&2
            return 1
        fi
        ln -s "/usr/lib/aw-tauri/modules/$module" "$pkgdir/usr/bin/$(basename "$module")"
    done
}
