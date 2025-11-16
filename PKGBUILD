# Maintainer: FirstAirBender <noblechuk5[at]gmail[dot]com>
# Maintainer: txtsd <aur.archlinux@ihaveea.quest>
# Contributor: Luis Martinez <luis dot martinez at disroot dot org>
# Contributor: Philip Goto <philip.goto@gmail.com>

pkgname=librespot
pkgver=0.7.0
pkgrel=4
pkgdesc='Open source client library for Spotify'
arch=(x86_64 armv7h aarch64)
url='https://github.com/librespot-org/librespot'
license=('MIT')
depends=(
  alsa-lib
  avahi
  gcc-libs
  glibc
  gst-plugins-base-libs
  gstreamer
)
makedepends=(
  cargo
  git
  jack
  libpulse
  portaudio
  sdl2
)
optdepends=(
  'gst-plugins-base: Audio playback using GStreamer'
  'gst-plugins-good: Audio playback using GStreamer'
  'jack2: Audio playback using JACK'
  'libpulse: Audio playback using PulseAudio'
  'portaudio: Audio playback using PortAudio'
  'sdl2: Audio playback using SDL2'
)
options=(!lto)
source=("git+${url}#tag=v${pkgver}")
sha256sums=('ab17e53f4df4561ac6055c43aacf3757363df997487e9723041a50615df771f1')

prepare() {
  cd "${pkgname}"

  export RUSTUP_TOOLCHAIN=stable
  export CARGO_TARGET_DIR=target
  cargo fetch --locked --target "$(rustc -vV | sed -n 's/host: //p')"
}

build() {
  cd "${pkgname}"

  export RUSTUP_TOOLCHAIN=stable
  export CARGO_TARGET_DIR=target
  cargo build --frozen --release --no-default-features --features "native-tls,alsa-backend,pulseaudio-backend,with-libmdns" --workspace
}

check() {
  cd "${pkgname}"

  export RUSTUP_TOOLCHAIN=stable
  cargo test --frozen --release --no-default-features --features "native-tls,pulseaudio-backend,with-libmdns" --workspace
}

package() {
  cd "${pkgname}"

  install -Dm755 "target/release/${pkgname}" "${pkgdir}/usr/bin/${pkgname}"
  install -Dm644 "contrib/${pkgname}.service" "${pkgdir}/usr/lib/systemd/system/${pkgname}.service"
  install -Dm644 "contrib/${pkgname}.user.service" "${pkgdir}/usr/lib/systemd/user/${pkgname}.service"
  install -Dm644 LICENSE "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}
