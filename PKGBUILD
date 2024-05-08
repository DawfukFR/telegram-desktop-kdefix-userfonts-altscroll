# Maintainer: detiam <dehe_tian@outlook.com>
# Contributor: Andy Kluger <https://t.me/andykluger>
# Contributor: Dmitry Valter <`echo ZHZhbHRlciA8YXQ+IHByb3Rvbm1haWwgPGRvdD4gY29tCg== | base64 -d`>

# Upstream PKGBUILD (telegram-desktop in community repo):
# Maintainer: Sven-Hendrik Haase <svenstaro@archlinux.org>
# Contributor: hexchain <i@hexchain.org>

pkgname=telegram-desktop-kdefix-userfonts-altscroll
pkgver=5.0.1
pkgrel=1
conflicts=('telegram-desktop')
provides=('telegram-desktop')
pkgdesc='Official Telegram Desktop client, with changes: disable built-in unread indicator (so KDE unread counter can be used), fonts to yours as set by fontconfig, alt scroll mechanism in media view'
arch=('x86_64')
url="https://desktop.telegram.org/"
license=('GPL3')
options=('!debug')
depends=('hunspell' 'ffmpeg' 'hicolor-icon-theme' 'lz4' 'minizip' 'openal'
         'qt6-imageformats' 'qt6-svg' 'qt6-wayland' 'xxhash'
         'rnnoise' 'pipewire' 'libxtst' 'libxrandr' 'libxcomposite' 'libxdamage' 'abseil-cpp' 'libdispatch'
         'openssl' 'protobuf' 'glib2' 'libsigc++-3.0'
         'libxcomposite' 'libvpx' 'libxdamage' 'kcoreaddons' 'jemalloc')
makedepends=('cmake' 'git' 'ninja' 'python' 'range-v3' 'tl-expected' 'microsoft-gsl' 'meson'
             'extra-cmake-modules' 'wayland-protocols' 'plasma-wayland-protocols' 'libtg_owt'
             'gobject-introspection' 'boost' 'fmt' 'mm-common' 'perl-xml-parser' 'python-packaging' 'dos2unix' 'patch')
optdepends=('webkit2gtk: embedded browser features'
            'xdg-desktop-portal: desktop integration')
source=("https://github.com/telegramdesktop/tdesktop/releases/download/v${pkgver}/tdesktop-${pkgver}-full.tar.gz"
        "kde-theme-injection-fix.patch"
        "mediaview-altscroll.patch")
sha512sums=('45b7833f20f01d78c09163e205af7d68afffcfc88075ba6af35dc6cbbce1f0205c0150b137ca09e6bdaf271240e4d1336411ad427bc27a2b2ad42dc435ee0ec2'
            '7b3dd58276cbe2145887e3c127519a461be83485d9874c08d98d21e97bfb194a4355d0766746157b20d15027db8c265600b5d279cc07456153b2a2736832bae6'
            'd0cde8cae20455da13bf7e59f216cc2c2175935ab44779321bfa698ead770782b0d67c0095bf703c7e95639d6e9dffb7ca3dcf879d94151dc1831aa3b8c33893')

prepare() {
    cd tdesktop-$pkgver-full

    # patching
    for patch in "${source[@]}"; do
        if [[ $patch == *.patch ]]; then
        msg2 "Applying $patch"
        patch --no-backup-if-mismatch -Np1 -i "$srcdir/$patch"
        fi
    done

    # userfonts
    for ttf in Telegram/lib_ui/fonts/*.ttf; do
        rm "$ttf" && touch "$ttf"
    done && sed -i 's/DemiBold/Bold/g' \
        Telegram/lib_ui/ui/style/style_core_font.cpp
        
    find "${srcdir}"/ -type f -exec dos2unix {} \;
}

build() {
    CXXFLAGS+=' -ffat-lto-objects'

    # Turns out we're allowed to use the official API key that telegram uses for their snap builds:
    # https://github.com/telegramdesktop/tdesktop/blob/8fab9167beb2407c1153930ed03a4badd0c2b59f/snap/snapcraft.yaml#L87-L88
    # Thanks @primeos!
    cmake -B build -S tdesktop-$pkgver-full -G Ninja \
        -DCMAKE_CXX_FLAGS=-g0 \
        -DCMAKE_VERBOSE_MAKEFILE=OFF \
        -DCMAKE_INSTALL_PREFIX="/usr" \
        -DCMAKE_BUILD_TYPE=Release \
        -DDESKTOP_APP_USE_PACKAGED_FONTS=OFF \
        -DTDESKTOP_API_ID=611335 \
        -DTDESKTOP_API_HASH=d524b414d21f4d37f08684c1df41ac9c
    cmake --build build
}

package() {
    DESTDIR="$pkgdir" cmake --install build
}
