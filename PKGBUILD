# Maintainer: Aaron Griffin <aaron@archlinux.org>

pkgname=shadow
pkgver=4.1.4.1
pkgrel=1
pkgdesc="Shadow password file utilities"
arch=('i686' 'x86_64')
url='http://pkg-shadow.alioth.debian.org/'
license=('custom')
groups=('base')
depends=('bash')
backup=(etc/login.defs
        etc/pam.d/{chage,login,passwd,shadow,useradd,usermod,userdel}
        etc/pam.d/{chpasswd,newusers,groupadd,groupdel,groupmod}
        etc/pam.d/{chfn,chgpasswd,groupmems,chsh}
        etc/default/useradd)
depends=('pam')
source=(ftp://pkg-shadow.alioth.debian.org/pub/pkg-shadow/shadow-$pkgver.tar.bz2
        useradd.defaults login passwd chgpasswd defaults.pam login.defs adduser
        shadow.cron.daily xstrdup.patch)
options=(!libtool)
install='shadow.install'
md5sums=('62f7dae4cb54fa84e478c4602d58cbe8'
         'beb64d09256ea46a4d96a783f096447f'
         '0aa429de6773ebcdf89db80165379cc6'
         'b84204ab731bd02dca49d0637d44ebec'
         '65e9ebce249a5b9ed021e2790452b9e1'
         'a31374fef2cba0ca34dfc7078e2969e4'
         '3699bed31154051c0508a890d6d95027'
         '6ce67e423ee19c87ae64f661310b2408'
         '1d64b4113e1d402746d9dd65f28a2c6f'
         '0eebe9d13065bec4b5d7ccf3bf46c509')

build() {
  cd $srcdir/$pkgname-$pkgver

  #Ugh, force this to build shared libraries, for god's sake
  sed -i "s/noinst_LTLIBRARIES/lib_LTLIBRARIES/g" lib/Makefile.am
  libtoolize
  autoreconf
  export LDFLAGS="$LDFLAGS -lcrypt"

  patch -Np1 -i $srcdir/xstrdup.patch || return 1

  # supress etc/pam.d/*, we provide our own
  sed -i '/^SUBDIRS/s/pam.d//' etc/Makefile.in

  ./configure \
    --prefix=/usr --libdir=/lib \
    --mandir=/usr/share/man --sysconfdir=/etc \
    --enable-shared --disable-static \
    --with-libpam --without-selinux
  make || return 1
  make DESTDIR=$pkgdir install

  # license
  install -Dm644 COPYING $pkgdir/usr/share/licenses/shadow/COPYING

  # interactive useradd
  install -Dm755 $srcdir/adduser $pkgdir/usr/sbin/adduser

  # useradd defaults
  install -Dm644 $srcdir/useradd.defaults $pkgdir/etc/default/useradd

  # cron job
  install -Dm744 $srcdir/shadow.cron.daily $pkgdir/etc/cron.daily/shadow

  # login.defs
  install -Dm644 $srcdir/login.defs $pkgdir/etc/login.defs

  # PAM config - cutsom
  install -Dm644 $srcdir/login $pkgdir/etc/pam.d/login
  install -Dm644 $srcdir/passwd $pkgdir/etc/pam.d/passwd
  install -Dm644 $srcdir/chgpasswd $pkgdir/etc/pam.d/chgpasswd
  # PAM config - from tarball
  install -Dm644 etc/pam.d/groupmems $pkgdir/etc/pam.d/groupmems

  # we use the 'useradd' PAM file for other similar utilities
  for file in chage chpasswd chfn chsh groupadd groupdel groupmod \
      newusers shadow useradd usermod userdel; do
    install -Dm644 $srcdir/defaults.pam $pkgdir/etc/pam.d/$file
  done

  # Remove su - using su from coreutils instead
  rm -v $pkgdir/bin/su
  find $pkgdir/usr/share/man -name 'su.1' -exec rm -v {} \;
}
