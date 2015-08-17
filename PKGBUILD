# $Id: 
# Maintainer: Jubei-Mitsuyoshi <jubei.house.of.five.masters@gmail.com>
# Contributor: Aaron Griffin <aaron@archlinux.org>
# Contributor: Tobias Powalowski <tpowa@archlinux.org>
# Contributor: Thomas B�chler <thomas@archlinux.org>
# Contributor: Aline Freitas <aline@alinefreitas.com.br>

pkgname=udev-lsd-fork
_axepkgname=udev
pkgver=189
pkgrel=1
pkgdesc="The userspace dev tools (udev)"
depends=('util-linux' 'glib2' 'kmod' 'hwids' 'bash' 'acl')
provides=("udev=$pkgver")
conflicts=("udev<$pkgver" 'udev-lsd')
install=udev.install
arch=(i686 x86_64)
url="https://bitbucket.org/braindamaged/udev"
license=('GPL')
makedepends=('gobject-introspection' 'gperf' 'libxslt')
source=(https://bitbucket.org/braindamaged/udev/downloads/$_axepkgname-$pkgver.tar.gz
        initcpio-hooks-udev
        initcpio-install-udev
		soname.patch)
backup=(etc/udev/udev.conf)
groups=('base')
options=(!makeflags !libtool)
md5sums=('0fce29b89b700ab5c3f7ed9431d96a31'
         'e433c11d38cf4f877b41d06e2753ebe0'
         'e6faf4c3fe456f10d8efd2487d5e3cb7'
		 '3B0833020E59315A3C575A146482F84C')

build() {
  cd $srcdir/$_axepkgname-$pkgver
  
  patch -Np1 <"$srcdir/soname.patch"

  ./configure --prefix=/usr \
              --sysconfdir=/etc \
              --libexecdir=/usr/lib \
              --with-firmware-path=/usr/lib/firmware/updates:/lib/firmware/updates:/usr/lib/firmware:/lib/firmware \
              --with-usb-ids-path=/usr/share/hwdata/usb.ids \
              --with-pci-ids-path=/usr/share/hwdata/pci.ids

  make
}


package() {
  cd $srcdir/$_axepkgname-$pkgver
  make DESTDIR=${pkgdir} install

  # install the mkinitpcio hook
  install -D -m644 ../initcpio-hooks-udev ${pkgdir}/usr/lib/initcpio/hooks/udev
  install -D -m644 ../initcpio-install-udev ${pkgdir}/usr/lib/initcpio/install/udev

  # udevd moved, symlink to make life easy for restarting udevd manually
  ln -s ../lib/udev/udevd ${pkgdir}/usr/bin/udevd

  # the path to udevadm is hardcoded in some places
  install -d ${pkgdir}/sbin
  ln -s ../usr/bin/udevadm ${pkgdir}/sbin/udevadm

  # fix wrong path to /bin/sh
  sed -i -e 's#/usr/bin/sh#/bin/sh#g' $pkgdir/usr/lib/udev/keyboard-force-release.sh

  # Replace dialout/tape/cdrom group in rules with uucp/storage/optical group
  for i in $pkgdir/usr/lib/udev/rules.d/*.rules; do
    sed -i -e 's#GROUP="dialout"#GROUP="uucp"#g;
               s#GROUP="tape"#GROUP="storage"#g;
               s#GROUP="cdrom"#GROUP="optical"#g' $i
  done
}