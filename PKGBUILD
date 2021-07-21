# Maintainer: Joost Molenaar <jjm@j0057.nl>

pkgname=pacbuild-git
pkgver=git
pkgrel=1
pkgdesc='Script that creates packages for a Pacman repository'
arch=('any')
url='https://github.com/j0057/pacbuild'
license=('GPL3')
depends=('devtools')
makedepends=('git')
provides=("${pkgname%-git}")
conflicts=("${pkgname%-git}")
source=()
md5sums=()

pkgver() {
    cd "$srcdir"
    git describe | sed 's/-/./g'
}

package() {
    install -o root -g root -m 755 -d "$pkgdir/usr/bin"
    install -o root -g root -m 755 '../pacbuild' -t "$pkgdir/usr/bin"
}
