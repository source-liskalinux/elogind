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
makedepends=('meson' 'ninja' 'gperf' 'python-jinja')
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
        -Dman=false
    meson compile -C build
}

package() {
    cd "${srcdir}/${pkgname}-${pkgver}"
    DESTDIR="${pkgdir}" meson install -C build
    if [ ! -f "${pkgdir}/usr/libexec/elogind" ]; then
        echo "ERROR: /usr/libexec/elogind not found after install! Build/install failed silently." >&2
        exit 1
    fi
    # elogind >=256 dropped -Drootlibexecdir in favor of meson's builtin
    # libexecdir (default /usr/libexec), so the daemon now lives at
    # /usr/libexec/elogind instead of the old systemd-style
    # /usr/lib/elogind/elogind. Keep a compat symlink at the old path so
    # existing service scripts (lksysdir, etc.) that still point at
    # /usr/lib/elogind/elogind keep working without editing every consumer.
    install -d -m755 "${pkgdir}/usr/lib/elogind"
    ln -s /usr/libexec/elogind "${pkgdir}/usr/lib/elogind/elogind"
    ln -s elogind/sd-login.h "${pkgdir}/usr/include/sd-login.h"
    rm -rf "${pkgdir}/usr/lib/systemd"
    rm -rf "${pkgdir}/lib/systemd"
    # elogind implements a libsystemd-ABI-compatible library (sd-bus,
    # sd-login, sd-daemon), but it ships only as libelogind.so.0. programs
    # built against real systemd (e.g. NetworkManager) look for the literal
    # name "libsystemd.so.0" at runtime and fail with:
    #   error while loading shared libraries: libsystemd.so.0: cannot open shared object file
    # without this compat symlink. Same approach other elogind-based distros
    # (Devuan, Artix) use.
    if [ ! -e "${pkgdir}/usr/lib/libelogind.so.0" ]; then
        echo "ERROR: usr/lib/libelogind.so.0 not found after install! Cannot create libsystemd.so.0 compat symlink." >&2
        exit 1
    fi
    ln -s libelogind.so.0 "${pkgdir}/usr/lib/libsystemd.so.0"
    ln -s libsystemd.so.0 "${pkgdir}/usr/lib/libsystemd.so"
}
