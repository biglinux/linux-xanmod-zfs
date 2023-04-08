# Maintainer: Bernhard Landauer <bernhard@manjaro.org>
# Maintainer: Philip Müller <philm[at]manjaro[dot]org>

_linuxprefix=linux-xanmod
_extramodules=$(find /usr/lib/modules -type d -iname 6.2.10*xanmod* | rev | cut -d "/" -f1 | rev)

pkgname="$_linuxprefix-zfs"
pkgver=2.1.9
pkgrel=62101
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
        'https://github.com/openzfs/zfs/commit/59f187563937aa0d6c74a9854eb1cab6632866f9.patch')
sha256sums=('6b172cdf2eb54e17fcd68f900fab33c1430c5c59848fa46fab83614922fe50f6'
            'SKIP'
            '659a214380e77b5eb4f629c02eeaaf7cb4b0f9ffe316d17bb19ae370b54bc912')
validpgpkeys=('4F3BA9AB6D1F8D683DC2DFB56AD860EED4598027'  # Tony Hutter (GPG key for signing ZFS releases) <hutter2@llnl.gov>
              'C33DF142657ED1F7C328A2960AB9E991C6AF658B') # Brian Behlendorf <behlendorf1@llnl.gov>

prepare() {
    cd "zfs-${pkgver}"

    # Backport fix for build issues on linux-6.2.8 kernel. (https://github.com/openzfs/zfs/issues/14658)
    patch -p1 -i ../59f187563937aa0d6c74a9854eb1cab6632866f9.patch

    ./autogen.sh
    sed -i "s|\$(uname -r)|${_kernver}|g" configure
}

build() {
    _kernver=$(find /usr/lib/modules -type d -iname 6.2.10*xanmod* | rev | cut -d "/" -f1 | rev)

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
