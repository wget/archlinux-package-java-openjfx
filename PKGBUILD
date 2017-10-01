# Maintainer: Guillaume ALAUX <guillaume@archlinux.org>
# Contributor: William Gathoye <william + archlinux at gathoye dot be>
# Contributor: Emanuel Couto <emanuel dot amaral dot couto at gmail dot com>
# Contributor: Richard Jackson <rdjack21 at gmail dot com>
# Contributor: Tinx <arch at tinx dot eu>
# Contributor: Jens Kapitza <j dot kapitza at schwarze-allianz dot de>
# Contributor: Olli <olli at coderkun dot de>

# Demos available in `apps/samples`
# To build them: `ant -Dplatforms.JDK_1.8.home=/usr/lib/jvm/default jar`

pkgbase=java-openjfx
pkgname=('java-openjfx' 'java-openjfx-doc' 'java-openjfx-src')
_java_ver=8
_jdk_update=172
_jdk_build=00
_hgtag=${_java_ver}u${_jdk_update}-b${_jdk_build}
pkgver=${_java_ver}.u${_jdk_update}
pkgrel=1
pkgdesc='Java OpenJFX 8 client application platform (open-source implementation of JavaFX)'
arch=('i686' 'x86_64')

# http://hg.openjdk.java.net/openjfx/8u/rt/tags
url='https://wiki.openjdk.java.net/display/OpenJFX/Main'
license=('GPL')

makedepends=(
    'java-environment>=8'
    'bison'
    'gperf'
    'gtk2'
    'libxtst'
    'ffmpeg'
    'python2'
    'qt5-base'
    'webkit2gtk'
    'ruby' 
    'cmake'
    'unzip'
)

source=(
    "http://hg.openjdk.java.net/openjfx/8u-dev/rt/archive/${_hgtag}.tar.bz2"
    'gradle.properties'
    'https://services.gradle.org/distributions/gradle-1.8-bin.zip'
    # https://anonscm.debian.org/cgit/pkg-java/openjfx.git/tree/debian/patches/17-gcc-compatibility.patch
    # Not fixed upstream. Still need to be applied, but line numbers have changed.
    fix-gcc-compatibility.patch
)
sha512sums=(
    'c60c84627e0263aff6749894bd376620585d1e4e59dd22a7a687c1db5490cec83bb3638d5614eca9b49fd83b6a36e935a076241cb3f4a18120e6662ec41aae39'
    'bdef90f0bd989b7902ce5c8078163460c2668c18c49a0781ae33bc28cfa27ab11ecba00fafe5c24988bcb043bd45d7a909a3101da6b35345eac637e59cc470e2'
    '2692f36626576a511e688ff42fc3381f8ecdf4fdf537939ab59de6c807eb17f4b4df722c4912e373ad3d9fe95be8516841ccb78a00111b5a7d38b848e632337b'
    'd4e6a9631fe999f393d7afe23a9f8289cf57404676cfb9b6c75a57bfe1308461052f103b03aac1a52cc2cc7572c0ff7707f0625829ea915ae33db660f93094ff'
)

_openjdk8dir="/usr/lib/jvm/java-8-openjdk"

case $CARCH in
  'i686') _CARCH='i386' ;;
  'x86_64')_CARCH='amd64' ;;
esac

prepare() {
  cd "rt-${_hgtag}"
  for p in fix-gcc-compatibility.patch; do
    patch -p1 < "${srcdir}/${p}"
  done
}

build() {
  cd "rt-${_hgtag}"

  ln -sf "${srcdir}/gradle.properties" .
  export GRADLE_USER_HOME="${srcdir}/gradle_home"
  mkdir -p ${GRADLE_USER_HOME}

  # Please make sure to not have any previous openjfx verion installed when
  # building this packages, otherwise it will fail with this error message.
  #   Execution failed for task ':checkJfxrtJar'.
  # > FAIL: /usr/lib/jvm/java-8-openjdk/jre/lib/ext/jfxrt.jar must be removed before building sdk
  "${srcdir}"/gradle-1.8/bin/gradle --info
}

package_java-openjfx() {
  pkgdesc='Java OpenJFX 8 client application platform (open-source implementation of JavaFX)'
  depends=('java-runtime-openjdk=8' 'gstreamer' 'libxtst' 'webkit2gtk' 'ffmpeg' 'qt5-base')
  conflicts=('openjfx')
  replaces=('openjfx')

  local _builddir="${srcdir}/rt-${_hgtag}/build"
  local _sdkdir="${_builddir}/sdk"

  install -d "${pkgdir}${_openjdk8dir}/jre/lib/${_CARCH}"
  install -m755 "${_sdkdir}/rt/lib/${_CARCH}"/*.* "${pkgdir}${_openjdk8dir}/jre/lib/${_CARCH}"

  install -d "${pkgdir}${_openjdk8dir}/jre/lib/ext"
  install -m644 "${_sdkdir}/rt/lib/ext"/*.* "${pkgdir}${_openjdk8dir}/jre/lib/ext"
  install -m644 "${_sdkdir}/rt/lib"/*.* "${pkgdir}${_openjdk8dir}/jre/lib"

  install -d "${pkgdir}${_openjdk8dir}/lib"
  install -m644 "${_sdkdir}/lib"/*.* "${pkgdir}${_openjdk8dir}/lib"

  install -d "${pkgdir}${_openjdk8dir}/bin"
  install -m755 "${_sdkdir}/bin"/* "${pkgdir}${_openjdk8dir}/bin"

  install -m644 -D "${_sdkdir}/man/man1/javapackager.1" "${pkgdir}/usr/share/man/man1/javapackager.1"
}

package_java-openjfx-doc() {
  pkgdesc='Java OpenJFX 8 client application platform (open-source implementation of JavaFX) - documentation'
  conflicts=('openjfx-doc')
  replaces=('openjfx-doc')

  local _builddir="${srcdir}/rt-${_hgtag}/build"
  local _sdkdir="${_builddir}/sdk"
  local docdir="/usr/share/doc"

  install -d "${pkgdir}${docdir}/openjfx"
  cp -dr --no-preserve=ownership "${_builddir}/javadoc"/* "${pkgdir}${docdir}/openjfx"
}

package_java-openjfx-src() {
  pkgdesc='Java OpenJFX 8 client application platform (open-source implementation of JavaFX) - sources'
  conflicts=('openjfx-src')
  replaces=('openjfx-src')

  local _builddir="${srcdir}/rt-${_hgtag}/build"
  local _sdkdir="${_builddir}/sdk"

  install -d "${pkgdir}${_openjdk8dir}"
  install -m644 "${_builddir}/javafx-src.zip" "${pkgdir}${_openjdk8dir}"
}
