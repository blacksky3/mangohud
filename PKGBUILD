# _     _            _        _          _____
#| |__ | | __ _  ___| | _____| | ___   _|___ /
#| '_ \| |/ _` |/ __| |/ / __| |/ / | | | |_ \
#| |_) | | (_| | (__|   <\__ \   <| |_| |___) |
#|_.__/|_|\__,_|\___|_|\_\___/_|\_\\__, |____/
#                                  |___/

#Maintainer: blacksky3 <https://github.com/blacksky3>
#Credits: Mark Wagie <mark dot wagie at tutanota dot com>
#Credits: Ranieri Althoff <ranisalt+aur at gmail dot com>

pkgbase=lib32-mangohud
pkgname=(lib32-mangohud lib32-mangoapp)
pkgver=0.6.8
pkgrel=1
_commit=efdcc6d2f54e37a3a32475453407f1eb33d1bef2
_imgui_ver=1.81
_spdlog_ver=1.8.5
arch=(x86_64)
url='https://github.com/flightlessmango/MangoHud'
license=(MIT)
makedepends=(git glfw-x11 glslang lib32-dbus lib32-libglvnd libxnvctrl
             meson ninja lib32-libx11 lib32-wayland nlohmann-json python-mako vulkan-headers)
source=(git+https://github.com/flightlessmango/MangoHud.git#commit=${_commit}
        git+https://github.com/flightlessmango/minhook.git
        https://github.com/ocornut/imgui/archive/v${_imgui_ver}/imgui-${_imgui_ver}.tar.gz
        https://wrapdb.mesonbuild.com/v2/imgui_${_imgui_ver}-1/get_patch#/imgui-${_imgui_ver}-1-wrap.zip
        spdlog-${_spdlog_ver}.tar.gz::https://github.com/gabime/spdlog/archive/v${_spdlog_ver}.tar.gz
        spdlog-${_spdlog_ver}-1-wrap.zip::https://wrapdb.mesonbuild.com/v1/projects/spdlog/${_spdlog_ver}/1/get_zip)

prepare(){
  cd $srcdir/MangoHud

  git submodule init modules/minhook
  git config submodule.minhook.url "$srcdir/minhook"
  git -c protocol.file.allow=always submodule update

  ln -sfv \
    "$srcdir/imgui-${_imgui_ver}" \
    "$srcdir/spdlog-${_spdlog_ver}" \
    subprojects
}

build(){
export CC='gcc -m32'
export CXX='g++ -m32'
export ASFLAGS=--32
export CFLAGS=-m32
export CXXFLAGS=-m32
export PKG_CONFIG_PATH=/usr/lib32/pkgconfig

  cd $srcdir/MangoHud

  rm -rf -v build_32

  mkdir build_32

  meson setup build_32/ \
  -D buildtype=release \
  -D prefix=/usr \
  -D libdir=lib32 \
  -Dglibcxx_asserts=false \
  -Duse_system_vulkan=enabled \
  -Duse_system_spdlog=disabled \
  -Dappend_libdir_mangohud=false \
  -Dld_libdir_prefix=false \
  -Dld_libdir_abs=false \
  -Dprepend_libdir_vk=true \
  -Dinclude_doc=false \
  -Dwith_nvml=enabled \
  -Dwith_xnvctrl=enabled \
  -Dwith_x11=enabled \
  -Dwith_wayland=enabled \
  -Dwith_dbus=enabled \
  -Dwith_dlsym=disabled \
  -Dloglevel=off \
  -Dmangoapp=true \
  -Dmangohudctl=true \
  -Dmangoapp_layer=true \
  -Dtests=disabled \

  #meson configure build_32/

  ninja -C build_32/

  meson compile -C build_32/
}

package_lib32-mangohud(){
  pkgdesc='MangoHud 32-bit library'
  depends=(lib32-dbus lib32-gcc-libs lib32-libx11 lib32-wayland lib32-vulkan-icd-loader mangohud-common)
  optdepends=('lib32-libxnvctrl: NVIDIA GPU stats by XNVCtrl (AUR)')
  replaces=("$pkgname-x11" "$pkgname-wayland")

  meson install --tags runtime -C $srcdir/MangoHud/build_32 --destdir "$pkgdir"

  mv "${pkgdir}"/usr/share/vulkan/implicit_layer.d/MangoHud.json "${pkgdir}"/usr/share/vulkan/implicit_layer.d/MangoHud.i686.json

  sed -i 's|VK_LAYER_MANGOHUD_overlay|VK_LAYER_MANGOHUD_overlay_32|g' "${pkgdir}"/usr/share/vulkan/implicit_layer.d/MangoHud.i686.json

  sed -i 's|$LIB|lib32|g' "${pkgdir}"/usr/share/vulkan/implicit_layer.d/MangoHud.i686.json

  install -Dm644 $srcdir/MangoHud/LICENSE -t "$pkgdir/usr/share/licenses/$pkgname/"
}

package_lib32-mangoapp(){
  pkgdesc='MangoApp 32-bit library'
  depends=('lib32-mangohud')

  meson install --tags mangoapp -C $srcdir/MangoHud/build_32 --destdir "$pkgdir"

  mv "${pkgdir}"/usr/share/vulkan/implicit_layer.d/libMangoApp.json "${pkgdir}"/usr/share/vulkan/implicit_layer.d/libMangoApp.i686.json

  sed -i 's|VK_LAYER_MANGOAPP_overlay|VK_LAYER_MANGOAPP_overlay_32|g' "${pkgdir}"/usr/share/vulkan/implicit_layer.d/libMangoApp.i686.json

  sed -i 's|$LIB|lib32|g' "${pkgdir}"/usr/share/vulkan/implicit_layer.d/libMangoApp.i686.json

  install -Dm644 $srcdir/MangoHud/LICENSE -t "$pkgdir/usr/share/licenses/$pkgname/"
}

sha256sums=('SKIP'
            'SKIP'
            'f7c619e03a06c0f25e8f47262dbc32d61fd033d2c91796812bf0f8c94fca78fb'
            '6d00b442690b6a5c5d8f898311daafbce16d370cf64f53294c3b8c5c661e435f'
            '944d0bd7c763ac721398dca2bb0f3b5ed16f67cef36810ede5061f35a543b4b8'
            '3c38f275d5792b1286391102594329e98b17737924b344f98312ab09929b74be')
