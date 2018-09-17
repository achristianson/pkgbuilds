# Maintainer: Conor Anderson <conor@conr.ca>
# Maintainer: Maxim Baz <$pkgname at maximbaz dot com>

pkgname=wire-desktop
pkgver=3.3.2872
pkgrel=1
pkgdesc='Modern, private messenger. Based on Electron.'
arch=('x86_64' 'i686')
url='https://wire.com/'
license=('GPL3')
conflicts=('wire-desktop-bin')
depends=('alsa-lib' 'gconf' 'gtk2' 'libxss' 'libxtst' 'nss' 'xdg-utils')
makedepends=('cargo' 'npm' 'patch' 'python2' 'git')
optdepends=('hunspell-en: for English spellcheck support'
            'noto-fonts-emoji: for colorful emoji made by Google'
            'ttf-emojione: for colorful emoji made by EmojiOne')
provides=('wire-desktop')
source=("${pkgver}.tar.gz::https://github.com/wireapp/wire-desktop/archive/release/"$pkgver".tar.gz"
        "${pkgname}.desktop"
        Gruntfile.patch)
sha256sums=('35949658604dbd400f3d86925b5ef3754c1ead285324dd94d2d0ed1e2d485bc4'
            'cc9056cecff2aa49a9ce9c8376d57ec8c7c2cb8174f7966b5cdccbeb2e3751ea'
            '0ab8a14a961502b1333d62e3cb0444e04d1b54c7ca1b36820c1d1d9dba0474a3')

build() {
  cd "${srcdir}/${pkgname}-release-${pkgver}"
  patch -p0 < $startdir/Gruntfile.patch
  npm install
  $(npm bin)/grunt 'linux-other'
}

package() {
  # Place files
  install -d "${pkgdir}/usr/lib/${pkgname}"
  cp -a "${srcdir}/${pkgname}-release-${pkgver}"/wrap/dist/linux*unpacked/* "${pkgdir}/usr/lib/${pkgname}"
  
  # Symlink main binary
  install -d "${pkgdir}/usr/bin"
  ln -s "/usr/lib/${pkgname}/${pkgname}" "${pkgdir}/usr/bin/${pkgname}"
  
  # Place desktop entry and icon
  desktop-file-install -m 644 --dir "${pkgdir}/usr/share/applications/" "${srcdir}/${pkgname}.desktop"
  for res in 32x32 256x256; do
    install -dm755 "${pkgdir}/usr/share/icons/hicolor/${res}/apps"
    install -Dm644 "${srcdir}/${pkgname}-release-${pkgver}/resources/icons/${res}.png" \
      "${pkgdir}/usr/share/icons/hicolor/${res}/apps/${pkgname}.png"
  done

  # Spellcheck dictionaries
  rm -rf "${pkgdir}/usr/lib/${pkgname}/resources/app/node_modules/spellchecker/vendor/hunspell_dictionaries"
  ln -s "/usr/share/hunspell" "${pkgdir}/usr/lib/${pkgname}/resources/app/node_modules/spellchecker/vendor/hunspell_dictionaries"

  # Place license files
  for license in "LICENSE.electron.txt" "LICENSES.chromium.html"; do
    install -Dm644 "${pkgdir}/usr/lib/${pkgname}/${license}" "${pkgdir}/usr/share/licenses/${pkgname}/${license}"
    rm "${pkgdir}/usr/lib/${pkgname}/${license}"
  done
}
