# $Id: PKGBUILD 56223 2011-10-03 14:27:18Z arodseth $
# Contributor: Giorgio Lando <lando at imap dot cc>
# Contributor: Sergej Pupykin
# Contributor: Thomas Bächler
# Maintainer: Thorsten Töpper <atsutane-tu@freethoughts.de>

pkgname=fcron
pkgver=3.0.6
pkgrel=4
pkgdesc="feature-rich cron implementation"
arch=(i686 x86_64)
url="http://fcron.free.fr"
license=('GPL')
depends=('sh')
makedepends=('smtp-server')
optdepends=('smtp-server: to receive mails from cron jobs')
provides=('cron')
conflicts=('dcron')
backup=(etc/fcron/fcron.conf etc/fcron/fcron.allow etc/fcron/fcron.deny \
        var/spool/fcron/systab var/spool/fcron/systab.orig)
install=fcron.install
options=('emptydirs' '!makeflags')
source=(http://fcron.free.fr/archives/$pkgname-$pkgver.src.tar.gz fcron.rc \
        systab systab.orig run-cron)
md5sums=('69ebcb41921e2a282f41ebecb3a27053'
         'e0c3f0bdc3c98fbbe46eff19001c18f2'
         '938722c6654ef7b07f4aa10001905ba1'
         'bfb7daa22ebe22b9917e455c1ca4a382'
         '5ff0cdcb9ec99778938ac6ef26800327')

build() {
  cd "$srcdir/$pkgname-$pkgver"
  ./configure --prefix=/usr \
              --sysconfdir=/etc/fcron \
              --with-answer-all=no \
              --with-boot-install=no \
              --with-username=cron \
              --with-groupname=cron \
              --datarootdir=/usr/share \
	            --datadir=/usr/share \
              --with-docdir=/usr/share/doc \
              --localstatedir=/var \
	            --with-editor=/usr/bin/vi \
              --with-sendmail=/usr/sbin/sendmail
  make
}

package() {
  # Create necessary user and group
  getent group cron || /usr/sbin/groupadd -g 22 cron
  getent passwd cron || /usr/sbin/useradd -d / -g cron -u 22 -s /bin/false cron

  cd "$srcdir/$pkgname-$pkgver"
  mkdir -p "$pkgdir/var/spool"
  make DESTDIR="$pkgdir/" install
  install -D -m755 "$srcdir/fcron.rc" "$pkgdir/etc/rc.d/fcron"
  install -D -m644 "$srcdir/$pkgname-$pkgver/files/fcron.pam" "$pkgdir/etc/pam.d/fcron"
  install -D -m644 "$srcdir/$pkgname-$pkgver/files/fcrontab.pam" "$pkgdir/etc/pam.d/fcrontab"
  # Install default fcrontab so that fcron can completely replace dcron
  install -D -m600 "$srcdir/systab" "$pkgdir/var/spool/fcron/systab"
  # In order to preserve the systab crontab in any case it is better to have
  # it in non-binary form too
  install -D -m600 "$srcdir/systab.orig" "$pkgdir/var/spool/fcron/systab.orig"
  # Add cron.* directories
  install -d -m755 "$pkgdir/etc/cron.daily"
  install -d -m755 "$pkgdir/etc/cron.hourly"
  install -d -m755 "$pkgdir/etc/cron.monthly"
  install -d -m755 "$pkgdir/etc/cron.weekly"
  # Install run-cron script to make fcron run without dcron
  install -D -m755 "$srcdir/run-cron" "$pkgdir/usr/sbin/run-cron"
}

# vim:set ts=2 sw=2 et:
