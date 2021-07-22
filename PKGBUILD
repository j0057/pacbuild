# Maintainer: Joost Molenaar <jjm@j0057.nl>

pkgname=pacbuild-git
pkgver=0.1
pkgrel=1
pkgdesc='Script that creates packages for a Pacman repository'
arch=('any')
url='https://github.com/j0057/pacbuild'
license=('GPL3')
depends=('devtools')
makedepends=('git')
provides=("${pkgname%-git}")
conflicts=("${pkgname%-git}")
source=(pacbuild)
md5sums=(SKIP)

pkgver() {
    git -C "$srcdir" describe 2>/dev/null | sed 's/-/./g' || awk '$1=="pkgver"{print $2}' FS='=' PKGBUILD
}
[ "$pkgver" != 0.1 ] && unset -f pkgver

package() {
    install -o root -g root -m 755 -d "$pkgdir/usr/bin"
    install -o root -g root -m 755 "$srcdir/pacbuild" -t "$pkgdir/usr/bin"
}
