# Maintainer: Conor Anderson <conor@conr.ca>
pkgname=wire-desktop-git
_pkgname=${pkgname%-git}
pkgver=3.0.2816.r5.g0f9719d
pkgrel=1
pkgdesc='Modern, private messenger'
arch=('x86_64' 'i686')
url='https://wire.com/'
license=('GPL3')
conflicts=('wire-desktop-bin' 'wire-desktop')
depends=('alsa-lib' 'gconf' 'gtk2' 'libxss' 'libxtst' 'nss' 'xdg-utils')
makedepends=('cargo' 'npm' 'python2')
optdepends=('hunspell-en: for English spellcheck support')
provides=('wire-desktop')
source=("git://github.com/wireapp/wire-desktop.git"
        "wire-desktop.desktop")
sha256sums=('SKIP'
            '5f2fb2747bc57e3ad5ff62c2a409982560bd8a16be9f48ddc364e64b90046c8a')

pkgver() {
  cd "${srcdir}/${_pkgname}"
  git describe --tags | sed 's/release\///g;s/\([^-]*-g\)/r\1/;s/-/./g'
}

build() {
  cd "${srcdir}/${_pkgname}"
  npm install
  $(npm bin)/grunt 'linux-other'
}

package() {
  # Place files
  install -d "${pkgdir}/usr/lib/${_pkgname}"
  cp -a "${srcdir}/${_pkgname}"/wrap/dist/linux*unpacked/* "${pkgdir}/usr/lib/${_pkgname}"
  
  # Symlink main binary
  install -d "${pkgdir}/usr/bin"
  ln -s "/usr/lib/${_pkgname}/${_pkgname}" "${pkgdir}/usr/bin/${_pkgname}"
  
  # Place desktop entry and icon
  desktop-file-install -m 644 --dir "${pkgdir}/usr/share/applications/" "${srcdir}/${_pkgname}.desktop"
  for res in 32x32 256x256; do
    install -dm755 "${pkgdir}/usr/share/icons/hicolor/${res}/apps"
    install -Dm644 "${srcdir}/${_pkgname}/resources/icons/${res}.png" \
      "${pkgdir}/usr/share/icons/hicolor/${res}/apps/${_pkgname}.png"
  done

  # Spellcheck dictionaries
  rm -rf "${pkgdir}/usr/lib/${_pkgname}/resources/app/node_modules/spellchecker/vendor/hunspell_dictionaries"
  ln -s "/usr/share/hunspell" "${pkgdir}/usr/lib/${_pkgname}/resources/app/node_modules/spellchecker/vendor/hunspell_dictionaries"

  # Place license files
  for license in "LICENSE.electron.txt" "LICENSES.chromium.html"; do
    install -Dm644 "${pkgdir}/usr/lib/${_pkgname}/${license}" "${pkgdir}/usr/share/licenses/${_pkgname}/${license}"
    rm "${pkgdir}/usr/lib/${_pkgname}/${license}"
  done
}
