# Maintainer: Jose Riha <jose1711 gmail com>
# Maintainer: Sebastian J. Bronner <waschtl@sbronner.com>
# Contributor: Patrick Jackson <PatrickSJackson gmail com>
# Contributor: Christoph Vigano <mail@cvigano.de>

pkgname=st
pkgver=0.8.4
pkgrel=2
pkgdesc='A simple virtual terminal emulator for X.'
arch=('i686' 'x86_64' 'armv7h' 'aarch64')
license=('MIT')
depends=(libxft)
url=https://st.suckless.org
_patches=("https://st.suckless.org/patches/scrollback/st-scrollback-0.8.4.diff"
          "https://st.suckless.org/patches/blinking_cursor/st-blinking_cursor-20200531-a2a7044.diff")
source=(https://dl.suckless.org/$pkgname/$pkgname-$pkgver.tar.gz
        ${_patches[@]}
        )
sha256sums=('d42d3ceceb4d6a65e32e90a5336e3d446db612c3fbd9ebc1780bc6c9a03346a6'
            '418e1c5df11105482f13a008218c89eadb974630c25b4a6ff3da763dc2560e44'
            'f05487e7f6d4d0d81f44f5a317b0f9bbe6d340fd80c9326aa4bae8da4127aa2d')
_sourcedir=$pkgname-$pkgver
_makeopts="--directory=$_sourcedir"

prepare() {
  for patch in "${_patches[@]}"; do
      pwd
      echo "Applying patch $(basename $patch)..."
      patch --directory="$_sourcedir" --strip=1 < $(basename $patch)
  done

  # This package provides a mechanism to provide a custom config.h. Multiple
  # configuration states are determined by the presence of two files in
  # $BUILDDIR:
  #
  # config.h  config.def.h  state
  # ========  ============  =====
  # absent    absent        Initial state. The user receives a message on how
  #                         to configure this package.
  # absent    present       The user was previously made aware of the
  #                         configuration options and has not made any
  #                         configuration changes. The package is built using
  #                         default values.
  # present                 The user has supplied his or her configuration. The
  #                         file will be copied to $srcdir and used during
  #                         build.
  #
  # After this test, config.def.h is copied from $srcdir to $BUILDDIR to
  # provide an up to date template for the user.
  if [ -e "$BUILDDIR/config.h" ]
  then
    cp "$BUILDDIR/config.h" "$_sourcedir"
  elif [ ! -e "$BUILDDIR/config.def.h" ]
  then
    msg='This package can be configured in config.h. Copy the config.def.h '
    msg+='that was just placed into the package directory to config.h and '
    msg+='modify it to change the configuration. Or just leave it alone to '
    msg+='continue to use default values.'
    echo "$msg"
  fi
  cp "$_sourcedir/config.def.h" "$BUILDDIR"
}

build() {
  make $_makeopts X11INC=/usr/include/X11 X11LIB=/usr/lib/X11
}

package() {
  local installopts='--mode 0644 -D --target-directory'
  local shrdir="$pkgdir/usr/share"
  local licdir="$shrdir/licenses/$pkgname"
  local docdir="$shrdir/doc/$pkgname"
  make $_makeopts PREFIX=/usr DESTDIR="$pkgdir" install
  install $installopts "$licdir" "$_sourcedir/LICENSE"
  install $installopts "$docdir" "$_sourcedir/README"
  install $installopts "$shrdir/$pkgname" "$_sourcedir/st.info"
}
