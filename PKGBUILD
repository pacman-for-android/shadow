# Maintainer: Aaron Griffin <aaron@archlinux.org>

pkgname=shadow
pkgver=4.1.2.1
pkgrel=2
pkgdesc="Shadow password file utilities"
arch=(i686 x86_64)
url='http://pkg-shadow.alioth.debian.org/'
license=('custom')
groups=('base')
backup=(etc/login.defs
        etc/pam.d/{chage,login,passwd,shadow,useradd,usermod,userdel}
        etc/pam.d/{chpasswd,newusers,groupadd,groupdel,groupmod}
        etc/pam.d/{chfn,chgpasswd,groupmems,chsh}
        etc/default/useradd)
depends=('pam')
source=(ftp://pkg-shadow.alioth.debian.org/pub/pkg-shadow/shadow-$pkgver.tar.bz2
        useradd.defaults login passwd defaults.pam login.defs adduser shadow.cron.daily
        xstrdup.patch)
options=(!libtool)
install='shadow.install'
md5sums=('c178e49c45495e296dabbe4ae01a0fbe'
         'beb64d09256ea46a4d96a783f096447f'
         '0aa429de6773ebcdf89db80165379cc6'
         'b84204ab731bd02dca49d0637d44ebec'
         'a31374fef2cba0ca34dfc7078e2969e4'
         '3699bed31154051c0508a890d6d95027'
         '6ce67e423ee19c87ae64f661310b2408'
         '1d64b4113e1d402746d9dd65f28a2c6f'
         '0eebe9d13065bec4b5d7ccf3bf46c509')

build() {
  cd $startdir/src/$pkgname-$pkgver

  #Ugh, force this to build shared libraries, for god's sake
  sed -i "s/noinst_LTLIBRARIES/lib_LTLIBRARIES/g" lib/Makefile.am
  libtoolize
  autoreconf
  export LDFLAGS="$LDFLAGS -lcrypt"

  patch -Np1 -i $startdir/src/xstrdup.patch || return 1

  # supress etc/pam.d/*, we provide our own
  sed -i '/^SUBDIRS/s/pam.d//' etc/Makefile.in

  ./configure \
  	--prefix=/usr --libdir=/lib \
	--mandir=/usr/share/man --sysconfdir=/etc \
    --enable-shared --disable-static \
    --with-libpam --without-selinux
  make || return 1
  make DESTDIR=$startdir/pkg install

  # license
  install -D -m644 COPYING $startdir/pkg/usr/share/licenses/shadow/COPYING

  # interactive useradd
  install -D -m755 $startdir/src/adduser $startdir/pkg/usr/sbin/adduser

  # useradd defaults
  install -D -m644 $startdir/src/useradd.defaults $startdir/pkg/etc/default/useradd

  # cron job
  install -D -m744 $startdir/src/shadow.cron.daily $startdir/pkg/etc/cron.daily/shadow

  # login.defs
  install -D -m644 $startdir/src/login.defs $startdir/pkg/etc/login.defs

  # PAM config - cutsom
  install -D -m644 $startdir/src/login $startdir/pkg/etc/pam.d/login
  install -D -m644 $startdir/src/passwd $startdir/pkg/etc/pam.d/passwd
  # PAM config - from tarball
  install -D -m644 etc/pam.d/chgpasswd $startdir/pkg/etc/pam.d/chgpasswd
  install -D -m644 etc/pam.d/groupmems $startdir/pkg/etc/pam.d/groupmems

  # we use the 'useradd' PAM file for other similar utilities
  for file in chage chpasswd chfn chsh groupadd groupdel groupmod \
      newusers shadow useradd usermod userdel; do
    install -D -m644 $startdir/src/defaults.pam $startdir/pkg/etc/pam.d/$file
  done

  # Remove su - using su from coreutils instead
  rm -v $startdir/pkg/bin/su
  find $startdir/pkg/usr/share/man -name 'su.1' -exec rm -v {} \;
}
