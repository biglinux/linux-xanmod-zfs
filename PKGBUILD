# Maintainer: Bernhard Landauer <bernhard@manjaro.org>
# Maintainer: Philip Müller <philm[at]manjaro[dot]org>

_linuxprefix=linux-xanmod
_extramodules=$(find /usr/lib/modules -type d -iname 6.1.7*xanmod* | rev | cut -d "/" -f1 | rev)

pkgname="$_linuxprefix-zfs"
pkgver=2.1.7
pkgrel=6171
pkgdesc='Kernel modules for the Zettabyte File System.'
arch=('x86_64')
url="http://zfsonlinux.org/"
license=('CDDL')
groups=("$_linuxprefix-extramodules")
depends=("$_linuxprefix" "kmod" "zfs-utils=${pkgver}")
makedepends=("$_linuxprefix-headers")
provides=("zfs=${pkgver}")
install=zfs.install
source=("https://github.com/zfsonlinux/zfs/releases/download/zfs-${pkgver}/zfs-${pkgver}.tar.gz"{,.asc}
        'https://github.com/andrewc12/openzfs/commit/68b1eba.patch')
sha256sums=('6462e63e185de6ff10c64ffa6ed773201a082f9dd13e603d7e8136fcb4aca71b'
            'SKIP'
            '2b6996a310893d63f79fa32f8be2bd54f0cbe2a17c993c32c461cd04d1e81ae8')
validpgpkeys=('4F3BA9AB6D1F8D683DC2DFB56AD860EED4598027'  # Tony Hutter (GPG key for signing ZFS releases) <hutter2@llnl.gov>
              'C33DF142657ED1F7C328A2960AB9E991C6AF658B') # Brian Behlendorf <behlendorf1@llnl.gov>

prepare() {
    cd "zfs-${pkgver}"

    ./autogen.sh
    sed -i "s|\$(uname -r)|${_kernver}|g" configure
}

build() {
    _kernver=$(find /usr/lib/modules -type d -iname 6.1.7*xanmod* | rev | cut -d "/" -f1 | rev)

    cd "zfs-${pkgver}"
    ./configure --prefix=/usr --sysconfdir=/etc --sbindir=/usr/bin --libdir=/usr/lib \
                --datadir=/usr/share --includedir=/usr/include --with-udevdir=/lib/udev \
                --libexecdir=/usr/lib/zfs-${pkgver} --with-config=kernel \
                --with-linux=/usr/lib/modules/${_kernver}/build
    make
}

package(){
    cd "zfs-${pkgver}"
    install -Dm644 module/*/*.ko -t "$pkgdir/usr/lib/modules/$_extramodules/"

    # compress each module individually
    find "$pkgdir" -name '*.ko' -exec xz -T1 {} +
}
