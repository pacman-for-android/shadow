# Maintainer: David Runge <dvzrv@archlinux.org>
# Contributor: Dave Reisner <dreisner@archlinux.org>
# Contributor: Aaron Griffin <aaron@archlinux.org>

pkgname=shadow
pkgver=4.13
pkgrel=3.1
pkgdesc="Password and account management tool suite with support for shadow files and PAM"
arch=(x86_64 aarch64)
url="https://github.com/shadow-maint/shadow"
license=(BSD)
depends=(
  acl libacl.so
  attr libattr.so
  audit libaudit.so
  glibc
  libxcrypt libcrypt.so
  pam libpam.so libpam_misc.so
)
makedepends=(docbook-xsl itstool libcap libxslt)
backup=(
  data/etc/default/useradd
  data/etc/login.defs
  data/etc/pam.d/{chage,{,ch,chg}passwd,group{add,del,mems,mod},newusers,shadow,user{add,del,mod}}
)
options=(!emptydirs)
# NOTE: distribution patches are taken from https://gitlab.archlinux.org/archlinux/packaging/upstream/shadow/-/commits/v4.13.0.arch1
source=(
  https://github.com/shadow-maint/shadow/releases/download/$pkgver/shadow-$pkgver.tar.xz{,.asc}
  0001-Disable-replaced-tools-and-man-pages.patch
  0002-Adapt-login.defs-for-PAM-and-util-linux.patch
  0003-Add-Arch-Linux-defaults-for-login.defs.patch
  0004-Add-Arch-Linux-defaults-for-etc-pam.d.patch
  shadow.{timer,service}
  useradd.defaults
  shadow-change-prefix.diff
)
sha512sums=('2949a728c3312bef13d23138d6b79caf402781b1cb179e33b5be546c1790971ec20778d0e9cd3dbe09691d928ffcbe88e60da42fab58c69a90d5ebe5e3e2ab8e'
            'SKIP'
            '23215dbc4efa5cb321f32442be30b92f79f1e008c7418ee5daac27540785c1674e790a5e4ee755e9a5a086589be8437e25efbee4a4668918b14337b86309192b'
            '26160ba1bc42619077dd826fc6e472196e47f4f2e29f9a70d68373a73df9d6187e3a2671369a223e230b05b42af113c38aacf24cd6cb99fbc00b8baca71ab6b7'
            '3b8bec1dc5dfdc5a3b7b3a4579c05d7fc71ac80c87bdb35031820c2442efcae5dfcc97c763ca9430c1dc3f5d3827dc391999cb67e89d3758d31bdc694dff4601'
            'fcedd59f0c1294ca03ff2553591058295073e9c795500f66e571e34635016898b999afa816c5994846e459bf743d2c7a358a5be1f561a86a75846df2112194e1'
            'e4edf705dd04e088c6b561713eaa1afeb92f42ac13722bff037aede6ac5ad7d4d00828cfb677f7b1ff048db8b6788238c1ab6a71dfcfd3e02ef6cb78ae09a621'
            '2c8689b52029f6aa27d75b8b05b0b36e2fc322cab40fdfbb50cdbe331f61bc84e8db20f012cf9af3de8c4e7fdb10c2d5a4925ca1ba3b70eb5627772b94da84b3'
            'c4515086464bf846214d20ef0a7f2dbdfe22a7abb5cf71dc8c05d73ecbbda9edeffb78da5173d0a64851093c05468e7b844c710f83e37e6922f20da6213b1d67'
            '15828a1e4bb5fb9cb28c500f3039950cb34db96dd8588d7dc981743bce3b44e2e3df2e09213d158e78bf71e6f0464042b16fbc9c063b9be8a7bf35315bcd2ae3')
b2sums=('315ab8a7e598aeefb50c11293e20cfa0982c3c3ae21c35ae243d09a4facf97a13c1d672990876e74ef94f5284402acf14997663743e2aaefa6cfc4369b7d24dc'
        'SKIP'
        'e109e09f7709270e6042389f74ee59f44d95c3bd02aa57fedbe27f1e111d36fdb2fc4bb9f837916bfd83ebfa7d1d0859a50d6fefe573da3fd6f849cfd61a0187'
        '9d3490810bc94c8809442e9e3928fd4dfc62a22e7134ecc63098a1e2ab5db6c64867f6f067641bb7bccf712a7269b67c36434d2ae3ed3e0a206ac66eef299dc9'
        '92474c0a9cd8bc4df08984a304c73122a9711f1e4c036361e1dcbc027b1e43e007d1e35cdd5db4295829603a097ab360adb66289c4b479a5d5ccee4947f72da7'
        'aee9aaadae6d49872b4eb98334fbffee7a49b1625b81019927908ac79753364fdac4d87433fcd5d2d2327d7b65eddcfc2edabe7c6a2a67ad7b101ab0bf6deaad'
        '5cfc936555aa2b2e15f8830ff83764dad6e11a80e2a102c5f2bd3b7c83db22a5457a3afdd182e3648c9d7d5bca90fa550f59576d0ac47a11a31dfb636cb18f2b'
        'a69191ab966f146c35e7e911e7e57c29fffd54436ea014aa8ffe0dd46aaf57c635d0a652b35916745c75d82b3fca7234366ea5f810b622e94730b45ec86f122c'
        '4b1aa9359b9bcd94ef89ba9e0eb1fc15d073ea70c654c1691407ab27a8bf14e6c68170a8316c94ce4be66cf3896707661422f9989ee3798d02f88c719de3fd62'
        'ec1fcb31c33645e37a998c44a4a83f046c1ca23526cb14ee5a47e6f410e87c1a6f98a8773d1b82370bc46bdc346ec2084a551ad8c63fc4b92ba84421d17cd11d')
validpgpkeys=(66D0387DB85D320F8408166DB175CFA98F192AF2)  # Serge Hallyn <sergeh@kernel.org>

prepare() {
  local filename

  cd $pkgname-$pkgver
  for filename in "${source[@]}"; do
    if [[ "$filename" =~ \.patch$ ]]; then
      printf "Applying patch %s\n" "${filename##*/}"
      patch -Np1 -i "$srcdir/${filename##*/}"
    fi
  done

  patch -Np2 -i ../shadow-change-prefix.diff

  autoreconf -fiv
}

build() {
  local configure_options=(
    --prefix=/data/usr
    --bindir=/data/usr/bin
    --sbindir=/data/usr/bin
    --libdir=/data/usr/lib
    --mandir=/data/usr/share/man
    --sysconfdir=/data/etc
    --disable-account-tools-setuid
    --enable-man
    --with-fcaps
    --with-libpam
    --with-group-name-max-length=32
    --with-audit
    --with-bcrypt
    --with-yescrypt
    --without-selinux
    --without-su
  )

  cd $pkgname-$pkgver
  ./configure "${configure_options[@]}"

  # prevent excessive overlinking due to libtool
  sed -i -e 's/ -shared / -Wl,-O1,--as-needed\0/g' libtool
  make
}

package() {
  cd $pkgname-$pkgver

  make DESTDIR="$pkgdir" install SHELL=/data/usr/bin/sh
  make DESTDIR="$pkgdir" -C man install SHELL=/data/usr/bin/sh

  # license
  install -vDm 644 COPYING -t "$pkgdir/data/usr/share/licenses/$pkgname/"

  # custom useradd(8) defaults (not provided by upstream)
  install -vDm 600 ../useradd.defaults "$pkgdir/data/etc/default/useradd"

  # systemd units
  install -vDm 644 ../shadow.timer -t "$pkgdir/data/usr/lib/systemd/system/"
  install -vDm 644 ../shadow.service -t "$pkgdir/data/usr/lib/systemd/system/"
  install -vdm 755 "$pkgdir/data/usr/lib/systemd/system/timers.target.wants"
  ln -s ../shadow.timer "$pkgdir/data/usr/lib/systemd/system/timers.target.wants/shadow.timer"
}
