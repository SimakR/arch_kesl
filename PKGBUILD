# Maintainer: copycat <simakr2512 | at | yandex [DOT] ru>
pkgname=('kesl')
pkgver=12.1.0.1274
_pkgverbuild=$(echo ${pkgver} | cut -d "." -f 4)
_pkgver=$(echo ${pkgver} | cut -d "." -f 1-3)
pkgrel=1
arch=('x86_64')
pkgdesc='Kaspersky Endpoint Security 12.1.0 for Linux'
url='https://support.kaspersky.com/help/KES4Linux/12.1.0/en-US/264264.htm'
license=('custom')
noextract=("kesl_${_pkgver}-${_pkgverbuild}_amd64.deb")
depends=('perl')
options=("!strip")
conflicts=( 'eea'
            'esets'
            'eea-dkms'
            'eea7-dkms')
install=${pkgname}.install
changelog=${pkgname}.changelog

#https://products.s.kaspersky-labs.com/endpoints/keslinux10/12.1.0.1274/multilanguage-12.1.0.1274/3834323834377c44454c7c31/kesl_12.1.0-1274_amd64.deb
source=( "https://products.s.kaspersky-labs.com/endpoints/keslinux10/${pkgver}/multilanguage-${pkgver}/3834323834377c44454c7c31/kesl_${_pkgver}-${_pkgverbuild}_amd64.deb"
         "${pkgname}.install"
         "kesl.ini"
         "kesl.start.conf")
sha256sums=('c8841a9b8fe55ee66442e6794b2ca732300f63ffcd046767e165714e81f182e3'
            '1b94a47fd9312cd08652fb960ba4dfa45c7a709a72ea15625685faab67ccd61d'
            '72899f7a8d5c63e1541762603cf6fc1a05a9114c60a529e7b18bac2b334040f1'
            '29efcd166bb0fc5baa5a85dc0f41c6c2e253f6b8fd3ee723862496364281cb4c')
validpgpkeys=('6AFE173577C4CBD621DF217FD093435AA3ED2C4A')

package_kesl() {
    # prepare dirs
    mkdir -p ${pkgdir}/usr/src
    mkdir -p ${pkgdir}/etc/init.d

    KESLIDIR=${pkgdir}/var/opt/kaspersky/kesl/install_${pkgver}

    # uncompress base packages
    bsdtar -xf kesl_${_pkgver}-${_pkgverbuild}_amd64.deb
    bsdtar -xf data.tar.xz -C ${pkgdir}/

    # install deb post/pre scripts
    [ ! -d "${pkgdir}/var/opt/kaspersky/kesl/pkgscripts" ] && mkdir -p ${pkgdir}/var/opt/kaspersky/kesl/pkgscripts
    bsdtar -xf control.tar.gz -C ${pkgdir}/var/opt/kaspersky/kesl/pkgscripts
    cp kesl.ini ${pkgdir}/var/opt/kaspersky/kesl/pkgscripts/

    # /usr/local/share/man is owned by pkg filesystem
    mv ${KESLIDIR}/usr/local/share ${KESLIDIR}/usr/
    chmod 755 -R ${KESLIDIR}/usr/
    rm -rf ${KESLIDIR}/usr/local/ ${pkgdir}/usr/local

    # man pages
    [ ! -d ${KESLIDIR}/usr/local/share/man/man1 ] && mkdir -p ${KESLIDIR}/usr/local/share/man/man1
    for m in $(find ${KESLIDIR}/usr/share/man/man1 -maxdepth 1 -mindepth 1 -type f);do
        ln -s /usr/share/man/man1/${m/*\/} ${KESLIDIR}/usr/local/share/man/man1/${m/*\/}
    done
    [ ! -d ${KESLIDIR}/usr/local/share/man/man5 ] && mkdir -p ${KESLIDIR}/usr/local/share/man/man5
    for m in $(find ${KESLIDIR}/usr/share/man/man5 -maxdepth 1 -mindepth 1 -type f);do
        ln -s /usr/share/man/man5/${m/*\/} ${KESLIDIR}/usr/local/share/man/man5/${m/*\/}
    done

    # install licenses
    for lic in $(find ${pkgdir}/var/opt/kaspersky/kesl/install_${pkgver}/opt/kaspersky/kesl/doc/ -maxdepth 1 -mindepth 1 -type f |grep license);do
        install -Dm644 ${lic} "$pkgdir/usr/share/licenses/$pkgname/${lic/*\/}"
    done

    # install startup config
    cp kesl.start.conf ${pkgdir}/var/opt/kaspersky/kesl/pkgscripts/
    sed -i "s/@PKGVER@/$pkgver/g" ${pkgdir}/var/opt/kaspersky/kesl/pkgscripts/kesl.start.conf
}


