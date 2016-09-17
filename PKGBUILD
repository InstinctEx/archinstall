#!/usr/bin/env bash
# -*- coding: utf-8 -*-
# region header
# Copyright Torben Sickert (info["~at~"]torben.website) 16.12.2012

# License
# -------

# This library written by Torben Sickert stand under a creative commons naming
# 3.0 unported license. see http://creativecommons.org/licenses/by/3.0/deed.de
# endregion
pkgname=arch-install
pkgver=VERSION
pkgrel=2
pkgdesc='automate your installation process'
arch=('any')
url='http://torben.website/archInstall'
license=('CC-BY-3.0')
depends=('pacman' 'yaourt')
provides=(arch-install)
source=('https://raw.githubusercontent.com/thaibault/archInstall/master/archInstall.sh')
md5sums=('SKIP')

pkgver() {
    printf "1.0.$(git rev-list --count HEAD)"
}

package() {
    install -D --mode 755 "${srcdir}/archInstall.sh" \
        "${pkgdir}/usr/bin/arch-install"
}
# region vim modline
# vim: set tabstop=4 shiftwidth=4 expandtab:
# vim: foldmethod=marker foldmarker=region,endregion:
# endregion
