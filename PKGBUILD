# Maintainer: Gustavo Alvarez <s1lpkn07@gmail.com>

pkgname=mplayer2-lachs0r-git
pkgver=20120730
pkgrel=1
pkgdesc="A movie player for linux (uses statically linked libav). lachs0r's Fork (GIT version)"
arch=('i686' 'x86_64')
depends=('libgl' 'libxss' 'fribidi' 'libxxf86dga' 'ttf-dejavu' 'desktop-file-utils' 'libbluray-git'
         'libdvdnav-svn' 'libdvdread-svn' 'libdv' 'jack' 'speex' 'enca' 'a52dec' 'libmng' 'libdca'
         'aalib' 'mpg123' 'directfb' 'libpulse' 'rsound' 'smbclient' 'xvidcore' 'libmad' 'libquvi-git'
         'libtheora' 'libcaca' 'faad2' 'libxxf86vm' 'libvdpau' 'libxinerama' 'libcdio' 'lcms2')
license=('GPL')
url="http://www.mplayer2.org/"
makedepends=('git' 'mesa' 'python' 'yasm' 'live-media')
backup=('etc/mplayer/codecs.conf'
        'etc/mplayer/input.conf')
provides=('mplayer' 'mplayer2')
conflicts=('mplayer' 'mplayer2')
replaces=('mplayer' 'mplayer2')
options=('!emptydirs') 
source=('http://ftp.mplayer2.org/pub/release/mplayer2-2.0.tar.xz'
        "$pkgname.install")
noextract=('mplayer2-2.0.tar.xz')
md5sums=('b880ae4be0e5b9693cdecf97c84b74f3'
         'cbc234e5e789e30c624741173992a225')
install="$pkgname.install"

_gitroot="git://git.mplayer2.org/mplayer2-build.git"
_gitname="mplayer2-build"

build() {
  cd "${srcdir}"

  if [ -d "${_gitname}" ]; then
    msg "Clean and Update repository"
    cd "${_gitname}"
    rm -fr mplayer
    ./clean
    ./update
  else
    msg "Clone Repository"
    git clone "${_gitroot}"
    ./init --shallow
  fi

  cd "${srcdir}"/"${_gitname}"

  msg "Get lachs0r's mplayer2 fork"
  rm -fr mplayer
  git clone --depth=1 git://github.com/lachs0r/mplayer2-lachs0r.git mplayer
  cd mplayer
  git fetch --depth=1 origin
  git checkout testing
  cd "${srcdir}"
  msg2 "Done"

  rm -fr "${_gitname}-build"
  cp -R "${_gitname}" "${_gitname}-build"
  cd "${_gitname}-build"

  msg "Add language PO files from mplayer2-2.0.tar.xz"
  bsdtar -xf "${srcdir}/mplayer2-2.0.tar.xz" mplayer2-2.0/po
  mv mplayer2-2.0/po mplayer && rm -fr mplayer2-2.0
  msg2 "Done"

  msg "Configure Mplayer2"
  # Make Mplayer2 build flags
  echo "--confdir=/etc/mplayer
--disable-lirc
--disable-lircc
--disable-live
--disable-mga
--disable-vstream
--enable-translation
--language=all
--language-doc=all
--language-man=all
--language-msg=all
--prefix=/usr
" > mplayer_options
  msg2 "Done"

  msg "Starting Make Mplayer2"
  make
  msg2 "Done"
}

package() {
  msg "Install Mplayer2"
  cd "${srcdir}/${_gitname}-build"
  make DESTDIR="${pkgdir}" install
  install -Dm664 mplayer/etc/{codecs,input,example}.conf "${pkgdir}/etc/mplayer/"
  install -d "${pkgdir}"/usr/share/{mplayer,applications}
  ln -s /usr/share/fonts/TTF/DejaVuSans.ttf "${pkgdir}/usr/share/mplayer/subfont.ttf"
  install -Dm644 mplayer/etc/mplayer.{desktop,xpm} "${pkgdir}/usr/share/applications/"
  sed -e 's|gmplayer|mplayer|' -e 's|GTK|X11|' -i "${pkgdir}/usr/share/applications/mplayer.desktop"
  msg2 "Done"
}
