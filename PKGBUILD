pkgname=elogind
pkgver=255.5
pkgrel=1
pkgdesc="The systemd project's logind, extracted to a standalone daemon"
arch=('x86_64')
url="https://github.com/elogind/elogind"
license=('LGPL-2.1-or-later')
depends=('dbus' 'pam' 'libcap' 'udev' 'util-linux-libs')
makedepends=('meson' 'ninja' 'gperf' 'python-jinja' 'docbook-xsl')
provides=('libelogind')
conflicts=('systemd-libs')
source=("https://github.com/elogind/elogind/archive/v${pkgver}/${pkgname}-${pkgver}.tar.gz")
sha256sums=('SKIP')

build() {
    cd "${pkgname}-${pkgver}"
    meson setup build \
        --prefix=/usr \
        --sysconfdir=/etc \
        --localstatedir=/var \
        -Drootlibdir=/usr/lib \
        -Dcgroup-controller=elogind \
        -Ddefault-hierarchy=unified \
        -Dpamlibdir=/usr/lib/security \
        -Dman=true
    meson compile -C build
}

package() {
    cd "${pkgname}-${pkgver}"
    DESTDIR="${pkgdir}" meson install -C build
    ln -s elogind/sd-login.h "${pkgdir}/usr/include/sd-login.h"
    rm -rf "${pkgdir}/usr/lib/systemd"
    rm -rf "${pkgdir}/lib/systemd"
}
