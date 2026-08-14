# PKGBUILD For elogind

# Contributor: Janorovic Volkov <janorovicvolkov@gmail.com>
# Maintainer: Janorovic Volkov <janorovicvolkov@gmail.com>

pkgname=elogind
pkgver=257.16
pkgrel=1
pkgdesc="The systemd project's logind, extracted to a standalone daemon"
arch=('x86_64')
url="https://github.com/elogind/elogind"
license=('LGPL-2.1-or-later')
depends=('dbus' 'pam' 'libcap' 'eudev' 'util-linux')
makedepends=('meson' 'ninja' 'gperf' 'python-jinja' 'docbook-xsl')
provides=('libelogind')
conflicts=('systemd-libs')
source=("https://github.com/elogind/elogind/archive/refs/tags/v${pkgver}.tar.gz")
sha256sums=('SKIP')

build() {
    cd "${srcdir}/${pkgname}-${pkgver}"
    meson setup build \
        --prefix=/usr \
        --sysconfdir=/etc \
        --localstatedir=/var \
        -Dcgroup-controller=elogind \
        -Ddefault-hierarchy=unified \
        -Dpamlibdir=/usr/lib/security \
        -Dman=true
    meson compile -C build
}

package() {
    cd "${srcdir}/${pkgname}-${pkgver}"
    DESTDIR="${pkgdir}" meson install -C build
    ln -s elogind/sd-login.h "${pkgdir}/usr/include/sd-login.h"
    rm -rf "${pkgdir}/usr/lib/systemd"
    rm -rf "${pkgdir}/lib/systemd"
}
