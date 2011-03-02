# Maintainer: Aaron Griffin <aaron@archlinux.org>

pkgname=shadow
pkgver=4.1.4.3
pkgrel=1
pkgdesc="Shadow password file utilities"
arch=('i686' 'x86_64')
url='http://pkg-shadow.alioth.debian.org/'
license=('custom')
groups=('base')
depends=('bash' 'pam')
backup=(etc/login.defs
        etc/pam.d/{chage,login,passwd,shadow,useradd,usermod,userdel}
        etc/pam.d/{chpasswd,newusers,groupadd,groupdel,groupmod}
        etc/pam.d/{chfn,chgpasswd,groupmems,chsh}
        etc/default/useradd)
options=('!libtool')
install=shadow.install
source=(ftp://pkg-shadow.alioth.debian.org/pub/pkg-shadow/shadow-$pkgver.tar.bz2
        useradd.defaults login passwd chgpasswd chpasswd newusers defaults.pam
	login.defs adduser shadow.cron.daily xstrdup.patch shadow-4.1.4.2-groupmod-pam-check.patch)
md5sums=('b8608d8294ac88974f27b20f991c0e79' 'beb64d09256ea46a4d96a783f096447f'\
         'bf137fac19884d71dc55c24b6d08e16c' 'b84204ab731bd02dca49d0637d44ebec'\
         '65e9ebce249a5b9ed021e2790452b9e1' '453a98456b297d2a69ca7e9b5f40d10b'\
         '453a98456b297d2a69ca7e9b5f40d10b' 'a31374fef2cba0ca34dfc7078e2969e4'\
         'fad9a7116366f7775b1099290be840da' '6ce67e423ee19c87ae64f661310b2408'\
         '1d64b4113e1d402746d9dd65f28a2c6f' '0eebe9d13065bec4b5d7ccf3bf46c509'\
         '7b747f7dca38b0b6e8ee56434378baae')
sha1sums=('ad9b85b5531ce8e68f4695efc4ac53ba7266269e' '9ae93de5987dd0ae428f0cc1a5a5a5cd53583f19'\
         '0b2d98a0ee3bfde8551ade48d4d35cc20ec702a1' '6f183bc7709b0a8d20ad17481a4ad025cf6e5056'\
         '4ad0e059406a305c8640ed30d93c2a1f62c2f4ad' 'd66096ed9477bd7242e8d2cc28eaa23170269788'\
         'd66096ed9477bd7242e8d2cc28eaa23170269788' '0e56fed7fc93572c6bf0d8f3b099166558bb46f1'\
         'fceb6defbf959f9bee5598e89378a49297968d1a' '78ec184a499f9708adcfcf0b7a3b22a60bf39f91'\
         '5d83ba7e11c765c951867cbe00b0ae7ff57148fa' '6010fffeed1fc6673ad9875492e1193b1a847b53'\
         '5823f38c0085b27e7e4327ab17ecc13563a43650')

build() {
  cd "$srcdir/$pkgname-$pkgver"

  #Ugh, force this to build shared libraries, for god's sake
  sed -i "s/noinst_LTLIBRARIES/lib_LTLIBRARIES/g" lib/Makefile.am
  libtoolize
  autoreconf
  export LDFLAGS="$LDFLAGS -lcrypt"

  patch -Np1 -i "$srcdir/xstrdup.patch"
  patch -Np1 -i "$srcdir/shadow-4.1.4.2-groupmod-pam-check.patch"

  # supress etc/pam.d/*, we provide our own
  sed -i '/^SUBDIRS/s/pam.d//' etc/Makefile.in

  ./configure \
    --prefix=/usr --libdir=/lib \
    --mandir=/usr/share/man --sysconfdir=/etc \
    --enable-shared --disable-static \
    --with-libpam --without-selinux
  make
}

package() {
  cd "$srcdir/$pkgname-$pkgver"
  make DESTDIR="$pkgdir" install

  # license
  install -Dm644 COPYING "$pkgdir/usr/share/licenses/shadow/COPYING"

  # interactive useradd
  install -Dm755 "$srcdir/adduser" "$pkgdir/usr/sbin/adduser"

  # useradd defaults
  install -Dm644 "$srcdir/useradd.defaults" "$pkgdir/etc/default/useradd"

  # cron job
  install -Dm744 "$srcdir/shadow.cron.daily" "$pkgdir/etc/cron.daily/shadow"

  # login.defs
  install -Dm644 "$srcdir/login.defs" "$pkgdir/etc/login.defs"

  # PAM config - cutsom
  install -Dm644 "$srcdir/login" "$pkgdir/etc/pam.d/login"
  install -Dm644 "$srcdir/passwd" "$pkgdir/etc/pam.d/passwd"
  install -Dm644 "$srcdir/chgpasswd" "$pkgdir/etc/pam.d/chgpasswd"
  install -Dm644 "$srcdir/chpasswd" "$pkgdir/etc/pam.d/chpasswd"
  install -Dm644 "$srcdir/newusers" "$pkgdir/etc/pam.d/newusers"
  # PAM config - from tarball
  install -Dm644 etc/pam.d/groupmems "$pkgdir/etc/pam.d/groupmems"

  # we use the 'useradd' PAM file for other similar utilities
  for file in chage chfn chsh groupadd groupdel groupmod shadow \
      useradd usermod userdel; do
    install -Dm644 "$srcdir/defaults.pam" "$pkgdir/etc/pam.d/$file"
  done

  # Remove su - using su from coreutils instead
  rm -v "$pkgdir/bin/su"
  find "$pkgdir/usr/share/man" -name 'su.1' -exec rm -v {} \;
}
