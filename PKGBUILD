# Maintainer: detiam <dehe_tian@outlook.com>
# Contributor: Andy Kluger <https://t.me/andykluger>
# Contributor: Dmitry Valter <`echo ZHZhbHRlciA8YXQ+IHByb3Rvbm1haWwgPGRvdD4gY29tCg== | base64 -d`>

# Upstream PKGBUILD (telegram-desktop in community repo):
# Maintainer: Sven-Hendrik Haase <svenstaro@archlinux.org>
# Contributor: hexchain <i@hexchain.org>

pkgname=telegram-desktop-kdefix-userfonts-altscroll
pkgver=4.16.6
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
        "mediaview-altscroll.patch"
        "block-sponsored_messages.patch"
        "fix-lzma-link.patch")
sha512sums=('5b580d36c8b8a90981c3541d4c148df37bb7ebdec0970bfbfe4c1a9a1175e3cf41c2a14d854d7d917482aa9086ee712844ec40780741df22ad6d42fcf3c48f1f'
            '7b3dd58276cbe2145887e3c127519a461be83485d9874c08d98d21e97bfb194a4355d0766746157b20d15027db8c265600b5d279cc07456153b2a2736832bae6'
            '38ff8627090ac9766a8d409fa26cbd4381640e08abcb677f4524a5305d011aa0f6ce81d69313a01f0db5c1a3794805bff2a059095258f5e8d9bd1133550cca2e'
            'c662524ca4f4a8df021ee94696d84896ed9a271df321933942806dda4544ea25f51a650ec8b4fc72f9a2219ea54cbfaf37b9604124f7263c86f74f1d647587ae'
            'd813a5ac6ff2208b693ecf494d7bf036087e223662f9f34aaaeafea0afe0fe798e867b9610f7221ea80319865502c20b61310d5a31634b888793873d63322463')

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
        Telegram/lib_ui/ui/style/style_core_custom_font.cpp
        
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
