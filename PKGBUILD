# Maintainer: Conor Anderson <conor@conr.ca>
pkgname=wire-desktop
_pkgname=wire
pkgver=2.11.2703
pkgrel=1
pkgdesc='Modern, private messenger. Based on Electron.'
arch=('x86_64' 'i686')
url='https://wire.com/'
license=('GPL3')
conflicts=('wire-desktop-bin')
depends=('alsa-lib' 'gconf' 'gtk2' 'libxss' 'libxtst' 'nss')
makedepends=('gendesk' 'grunt-cli' 'npm' 'python2')
optdepends=('hunspell-en: for English spellcheck support')
provides=('wire-desktop')
source=("${pkgver}.tar.gz::https://github.com/wireapp/wire-desktop/archive/release/"$pkgver".tar.gz")        
sha256sums=('1915e25ea92721a7066f277e2ed7dc898bfa4f587fbbe854d8675b68e1b067bf')

prepare() {
  gendesk -f -n --name=Wire --pkgname="${_pkgname}" --pkgdesc="${pkgdesc}" --exec="${_pkgname}" --categories="Network"
}

build() {
  cd "${srcdir}/${pkgname}-release-${pkgver}"
  npm install
  grunt 'clean:linux' 'update-keys' 'release-prod'
  if [ $CARCH == 'x86_64' ]; then
    node_modules/.bin/build --linux --x64 --dir
  elif [ $CARCH == 'i686' ]; then
    node_modules/.bin/build --linux --ia32 --dir
  else
    echo "Unknown architecture"; exit 1;
  fi
}

check() {
  cd "${srcdir}/${pkgname}-release-${pkgver}"
  npm test
}

package() {
  # Place files
  install -d "${pkgdir}/usr/lib/${_pkgname}"
  if [ $CARCH == 'x86_64' ]; then
    cp -a "${srcdir}/${pkgname}-release-${pkgver}/wrap/dist/linux-unpacked/"* "${pkgdir}/usr/lib/${_pkgname}"
  elif [ $CARCH == 'i686' ]; then
    cp -a "${srcdir}/${pkgname}-release-${pkgver}/wrap/dist/linux-ia32-unpacked/"* "${pkgdir}/usr/lib/${_pkgname}"
  else
    echo "Unknown architecture"; exit 1;
  fi
  
  # Symlink main binary
  install -d "${pkgdir}/usr/bin"
  ln -s "/usr/lib/${_pkgname}/${_pkgname}" "${pkgdir}/usr/bin/${_pkgname}"
  
  # Place desktop entry and icon
  desktop-file-install -m 644 --dir "${pkgdir}/usr/share/applications/" "${srcdir}/${_pkgname}.desktop"
  for res in 32x32 256x256; do
    install -dm755 "${pkgdir}/usr/share/icons/hicolor/${res}/apps"
    install -Dm644 "${srcdir}/${pkgname}-release-${pkgver}/resources/icons/${res}.png" \
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
