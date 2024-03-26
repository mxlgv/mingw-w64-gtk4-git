# Maintainer: Maxim Logaev <maxlogaev@proton.me>

_realname=gtk4
pkgbase=mingw-w64-${_realname}-git
pkgname=("${MINGW_PACKAGE_PREFIX}-${_realname}-git"
         "${MINGW_PACKAGE_PREFIX}-${_realname}-media-gstreamer-git")

pkgver=4.14.1.r62.gb1eed1c153
pkgrel=1
pkgdesc="GObject-based multi-platform GUI toolkit (v4) (mingw-w64) (git version)"
arch=('any')
mingw_arch=('mingw32' 'mingw64' 'ucrt64' 'clang64' 'clang32' 'clangarm64')
url="https://www.gtk.org"
license=('spdx:LGPL-2.1-or-later')
makedepends=("${MINGW_PACKAGE_PREFIX}-cc"
             "${MINGW_PACKAGE_PREFIX}-pkgconf"
             "${MINGW_PACKAGE_PREFIX}-gobject-introspection"
             "${MINGW_PACKAGE_PREFIX}-sassc"
             "${MINGW_PACKAGE_PREFIX}-meson"
             "${MINGW_PACKAGE_PREFIX}-python-docutils"
             "${MINGW_PACKAGE_PREFIX}-gi-docgen"
             "${MINGW_PACKAGE_PREFIX}-gst-plugins-bad-libs"
             "${MINGW_PACKAGE_PREFIX}-shaderc")
depends=("${MINGW_PACKAGE_PREFIX}-gcc-libs"
         "${MINGW_PACKAGE_PREFIX}-adwaita-icon-theme"
         "${MINGW_PACKAGE_PREFIX}-cairo"
         "${MINGW_PACKAGE_PREFIX}-gdk-pixbuf2"
         "${MINGW_PACKAGE_PREFIX}-glib2"
         "${MINGW_PACKAGE_PREFIX}-graphene"
         "${MINGW_PACKAGE_PREFIX}-json-glib"
         "${MINGW_PACKAGE_PREFIX}-libepoxy"
         "${MINGW_PACKAGE_PREFIX}-pango"
         "${MINGW_PACKAGE_PREFIX}-gtk-update-icon-cache"
         "${MINGW_PACKAGE_PREFIX}-shared-mime-info")

conflicts=("${MINGW_PACKAGE_PREFIX}-${_realname}"
           "${MINGW_PACKAGE_PREFIX}-${_realname}-media-gstreamer")

source=("git+https://gitlab.gnome.org/GNOME/gtk.git")
sha256sums=('SKIP')

pkgver() {
  cd gtk
  git describe --long --tags | sed 's/\([^-]*-g\)/r\1/;s/-/./g'
}

build() {
  mkdir -p build-${MSYSTEM} && cd build-${MSYSTEM}

  declare -a extra_config
  if check_option "debug" "n"; then
    extra_config+=("--buildtype=release")
  else
    extra_config+=("--buildtype=debug")
  fi

  MSYS2_ARG_CONV_EXCL="--prefix=" \
  ${MINGW_PREFIX}/bin/meson.exe setup \
    --prefix="${MINGW_PREFIX}" \
    --wrap-mode=nodownload \
    --auto-features=enabled \
    "${extra_config[@]}" \
    -Dbuild-examples=false \
    -Dbuild-tests=false \
    -Dman-pages=true \
    -Dmacos-backend=false \
    -Dx11-backend=false \
    -Dwayland-backend=false \
    -Dwin32-backend=true \
    -Dintrospection=enabled \
    ../gtk

  ${MINGW_PREFIX}/bin/meson.exe compile
}

package_gtk4-git() {
  ${MINGW_PREFIX}/bin/meson.exe install -C "${srcdir}/build-${MSYSTEM}" --destdir "${pkgdir}"

  install -Dm644 "${srcdir}/gtk/COPYING" "${pkgdir}${MINGW_PREFIX}/share/licenses/${_realname}/COPYING"

  # split media-gstreamer
  mkdir -p dest${MINGW_PREFIX}/lib
  mv "${pkgdir}${MINGW_PREFIX}"/lib/gtk-4.0 dest${MINGW_PREFIX}/lib/gtk-4.0
}

package_gtk4-media-gstreamer-git() {
  pkgdesc+=" (GStreamer media plugin)"
  depends=("${MINGW_PACKAGE_PREFIX}-gtk4-git"
           "${MINGW_PACKAGE_PREFIX}-gst-plugins-bad-libs")

  mv dest/* "${pkgdir}"
}

# template start; name=mingw-w64-splitpkg-wrappers; version=1.0;
# vim: set ft=bash :

# generate wrappers
for _name in "${pkgname[@]}"; do
  _short="package_${_name#${MINGW_PACKAGE_PREFIX}-}"
  _func="$(declare -f "${_short}")"
  eval "${_func/#${_short}/package_${_name}}"
done
# template end;