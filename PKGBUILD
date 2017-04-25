# Maintainer: Conor Anderson <conor@conr.ca>
pkgname=wire-desktop
pkgver=2.13.2741
pkgrel=1
pkgdesc='Modern, private messenger. Based on Electron.'
arch=('x86_64' 'i686')
url='https://wire.com/'
license=('GPL3')
conflicts=('wire-desktop-bin')
depends=('alsa-lib' 'gconf' 'gtk2' 'libxss' 'libxtst' 'nss')
makedepends=('gendesk' 'grunt-cli' 'npm' 'python2' 'git')
optdepends=('hunspell-en: for English spellcheck support')
provides=('wire-desktop')
source=("${pkgver}.tar.gz::https://github.com/wireapp/wire-desktop/archive/release/"$pkgver".tar.gz")        
sha256sums=('4a901dff91a9dc1b2b9886d15d66e252986e6df1a237f61ec30e7a2f1c27f20c')

prepare() {
  gendesk -f -n --name=Wire --pkgname="${pkgname}" --pkgdesc="${pkgdesc}" --exec="${pkgname}" --categories="Network"
}

build() {
  cd "${srcdir}/${pkgname}-release-${pkgver}"
  npm install
  grunt 'clean:linux' 'update-keys' 'release-prod'
  if [ $CARCH == 'x86_64' ]; then
    build_arch="x64"
  elif [ $CARCH == 'i686' ]; then
    build_arch="ia32"
  else
    echo "Unsupported architecture"; exit 1;
  fi
  grunt --arch=${build_arch} --target="dir" "electronbuilder:linux_other"
}

package() {
  # Place files
  install -d "${pkgdir}/usr/lib/${pkgname}"
  if [ $CARCH == 'x86_64' ]; then
    cp -a "${srcdir}/${pkgname}-release-${pkgver}/wrap/dist/linux-unpacked/"* "${pkgdir}/usr/lib/${pkgname}"
  elif [ $CARCH == 'i686' ]; then
    cp -a "${srcdir}/${pkgname}-release-${pkgver}/wrap/dist/linux-ia32-unpacked/"* "${pkgdir}/usr/lib/${pkgname}"
  else
    echo "Unknown architecture"; exit 1;
  fi
  
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
