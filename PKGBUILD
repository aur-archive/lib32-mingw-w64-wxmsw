pkgname=lib32-mingw-w64-wxmsw
pkgver=2.8.12
pkgrel=3
pkgdesc="Win32 implementation wxWidgets API for GUI (mingw-w64, 32-bit)"
arch=(any)
url="http://wxwidgets.org"
license=("custom:wxWindows")
makedepends=(mingw-w64-gcc)
depends=(mingw-w64-crt)
conflicts=(lib32-mingw-w64-wxmsw-static)
provides=(lib32-mingw-w64-wxmsw-static)
options=(!libtool !strip !buildflags)
source=("http://downloads.sourceforge.net/wxwindows/wxMSW-$pkgver.tar.bz2")
md5sums=('4bda849b9f7c66b58c4bc92e505ea714')

_architectures="i686-w64-mingw32"

_optimal_make_jobs() {
  if [ -r /proc/cpuinfo ]; then
    local core_count=$(grep -Fc processor /proc/cpuinfo)
  else
    local core_count=0
  fi
  if [ $core_count -gt 1 ]; then
    echo -n $[$core_count-1]
  else
    echo -n 1
  fi
}

build() {
  unset LDFLAGS
  for _arch in ${_architectures}; do
    mkdir -p "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    cd "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    ${srcdir}/wxMSW-${pkgver}/configure \
      --prefix=/usr/${_arch} \
      --build=$CHOST \
      --host=${_arch} \
      --with-msw \
      --enable-unicode \
      --disable-mslu \
      --enable-shared
    make -j$(_optimal_make_jobs)
  done
}

package() {
  for _arch in ${_architectures}; do
    cd "${srcdir}/${pkgname}-${pkgver}-build-${_arch}"
    make DESTDIR="$pkgdir" install
    mv "$pkgdir/usr/${_arch}/lib/"*.dll "$pkgdir/usr/${_arch}/bin"
    find "$pkgdir/usr/${_arch}" -name '*.exe' | xargs -rtl1 rm
    find "$pkgdir/usr/${_arch}" -name '*.dll' | xargs -rtl1 ${_arch}-strip -x
    find "$pkgdir/usr/${_arch}" -name '*.a' -o -name '*.dll' | xargs -rtl1 ${_arch}-strip -g
    rm -r "$pkgdir/usr/${_arch}/share"
  done
}
