# Maintainer: ThatOneCalculator <kainoa@t1c.dev>

_pkgname="hyprland"
pkgname="${_pkgname}-git"
pkgver=r996.gbf8bd87
pkgrel=1
pkgdesc="A dynamic tiling Wayland compositor based on wlroots that doesn't sacrifice on its looks."
arch=(any)
url="https://github.com/hyprwm/Hyprland"
license=('BSD')
depends=(
	libxcb
	xcb-proto
	xcb-util
	xcb-util-keysyms
	libxfixes
	libx11
	libxcomposite
	xorg-xinput
	libxrender
	pixman
	wayland-protocols
	cairo
	pango
	polkit
	glslang
	libinput
	libxcb
	libxkbcommon
	opengl-driver
	pixman
	wayland
	xcb-util-errors
	xcb-util-renderutil
	xcb-util-wm
	seatd
	vulkan-icd-loader
	vulkan-validation-layers
	xorg-xwayland)
makedepends=(
	git
	cmake
	ninja
	gcc
	gdb
	meson
	vulkan-headers
	wayland-protocols
	xorgproto)
source=("${_pkgname}::git+https://github.com/hyprwm/Hyprland.git")
conflicts=("${_pkgname}")
provides=(hyprland)
sha256sums=('SKIP')
options=(!makeflags !buildflags !strip)

pkgver() {
  cd "$_pkgname"
  ( set -o pipefail
    git describe --long 2>/dev/null | sed 's/\([^-]*-g\)/r\1/;s/-/./g' ||
    printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
  )
}

build() {
	cd "${srcdir}/${_pkgname}"
	git submodule update --init
	make fixwlr
	cd "./subprojects/wlroots/" && meson build/ --prefix="${srcdir}/tmpwlr" --buildtype=release && ninja -C build/ && mkdir -p "${srcdir}/tmpwlr" && ninja -C build/ install && cd ../../
	make protocols
    make release
	cd ./hyprctl && make all && cd ..
}

package() {
	cd "${srcdir}/${_pkgname}"
	mkdir -p "${pkgdir}/usr/share/wayland-sessions"
	mkdir -p "${pkgdir}/usr/share/hyprland"
	install -Dm755 build/Hyprland -t "${pkgdir}/usr/bin"
	install -Dm755 hyprctl/hyprctl -t "${pkgdir}/usr/bin"
	install -Dm644 assets/*.png -t "${pkgdir}/usr/share/hyprland"
	install -Dm644 example/hyprland.desktop -t "${pkgdir}/usr/share/wayland-sessions"
	install -Dm644 example/hyprland.conf -t "${pkgdir}/usr/share/hyprland"
	install -Dm644 LICENSE -t "${pkgdir}/usr/share/licenses/${_pkgname}"
	install -Dm755 ../tmpwlr/lib/libwlroots.so.11032 -t "${pkgdir}/usr/lib"
}
