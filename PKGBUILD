# Maintainer: FirstAirBender <noblechuk5[at]gmail[dot]com>
# Contributor: Luis Martinez <luis dot martinez at disroot dot org>
# Contributor: Philip Goto <philip.goto@gmail.com>

pkgname=librespot
pkgver=0.5.0
pkgrel=1
pkgdesc='Open source client library for Spotify'
arch=('x86_64')
url='https://github.com/librespot-org/librespot'
license=('MIT')
depends=(
    'alsa-lib'
)
makedepends=(
    'cargo'
)
optdepends=(
    'gst-plugins-base: Audio playback using GStreamer'
    'gst-plugins-good: Audio playback using GStreamer'
    'jack2: Audio playback using JACK'
    'libpulse: Audio playback using PulseAudio'
    'portaudio: Audio playback using PortAudio'
    'sdl2: Audio playback using SDL2'
)
source=("$pkgname-$pkgver.tar.gz::$url/archive/$pkgver.tar.gz")
sha256sums=('4ea500bb5673fbf4cc9dcbb7afdc228bf0cdb434cc3a0be79afa6bb385a8b5f8')

prepare() {
    cd "$pkgname-$pkgver"
    export RUSTUP_TOOLCHAIN=stable
    export PKG_FEATURES=alsa-backend
    PKG_FEATURE_MAP=(
        [jack2]='jackaudio-backend'
        [gst-plugins-good]='gstreamer-backend'
        [gst-plugins-base]='gstreamer-backend'
        [sdl2]='sdl-backend'
        [libpulse]='pulseaudio-backend'
        [portaudio]='portaudio-backend'
    )
    for pkg in "${!PKG_FEATURE_MAP[@]}"; do
        if [[ -n `pacman -Qsq "$pkg" | sed -E "/^$pkg\$/b; /.+/d"` ]]; then
            export PKG_FEATURES="$PKG_FEATURES,${PKG_FEATURE_MAP[$pkg]}"
        fi 2>/dev/null >&2
    done
    cargo fetch --locked --target "$(rustc -vV | sed -n 's/host: //p')"
}

build() {
    cd "$pkgname-$pkgver"
    export RUSTUP_TOOLCHAIN=stable
    export CARGO_TARGET_DIR=target
    export CARGO_BUILD_JOBS="$(nproc --ignore $(($(nproc) / 2)))"
    echo >&2 "Building $pkgname-$pkgver with the following features: $PKG_FEATURES"
    cargo build --release --frozen --no-default-features --features "$PKG_FEATURES"
}

# check() {
#   cd "$pkgname-$pkgver"
#     export RUSTUP_TOOLCHAIN=stable
#     cargo test --frozen --no-default-features --features "$PKG_FEATURES"
# }

package() {
    cd "$pkgname-$pkgver"
    install -Dvm0755 -t "$pkgdir/usr/bin/" "target/release/$pkgname"
    install -Dvm644 "contrib/$pkgname.service" -t "$pkgdir/usr/lib/systemd/system/"
    install -Dvm644 "contrib/$pkgname.user.service" "$pkgdir/usr/lib/systemd/user/$pkgname.service"
    install -Dvm644 LICENSE -t "$pkgdir/usr/share/licenses/$pkgname/"
}
