# Maintainer: mesa-llvm-mini builder
# shellcheck disable=SC2034

pkgname=mesa-llvm-mini
pkgver=26.1.1
pkgrel=1
pkgdesc='Mesa + vulkan drivers + bundled minimal libLLVM.so'
arch=(x86_64 aarch64)
url='https://www.mesa3d.org/'
license=('MIT' 'BSD-3-Clause' 'SGI-B-2.0' 'Apache-2.0 WITH LLVM-exception')
depends=(
	'glibc' 'libstdc++' 'libdrm' 'libx11' 'libxcb' 'libxshmfence'
	'libxext' 'libxxf86vm' 'libglvnd' 'libffi' 'expat' 'zlib' 'zstd'
	'wayland' 'spirv-tools' 'libedit' 'libxml2' 'lm_sensors' 'libpng'
	'libva' 'libpciaccess' 'libxrandr' 'libinput' 'elfutils' 'libelf'
)
makedepends=('cmake' 'ninja' 'meson' 'python' 'curl' 'rust-bindgen' 'glslang' 'directx-headers')
conflicts=(
	'mesa' 'vulkan-intel' 'vulkan-radeon' 'vulkan-nouveau'
	'vulkan-virtio' 'vulkan-swrast' 'vulkan-dzn' 'vulkan-gfxstream'
	'vulkan-intel-hasvk'
)
provides=(
	'mesa' 'vulkan-intel' 'vulkan-radeon' 'vulkan-nouveau'
	'vulkan-virtio' 'vulkan-swrast' 'vulkan-dzn' 'vulkan-gfxstream'
	'vulkan-intel-hasvk' 'opengl-driver' 'mesa-libgl' 'libva-driver'
)

# Detect version from local pacman repos
_pkgver=$(pacman -Si mesa 2>/dev/null | awk -F': ' '/^Version/{print $2; exit}')
if [ -z "$_pkgver" ]; then
	>&2 echo "Error getting version of Mesa from archlinux"
	exit 1
fi
if echo "$_pkgver" | grep -q ':'; then
	epoch=${_pkgver%%:*}
	_rest=${_pkgver#*:}
else
	_rest=$_pkgver
fi
pkgver=${_rest%-*}
pkgrel=${_rest#*-}

source=(
	"https://archive.mesa3d.org/mesa-$pkgver.tar.xz"
	"build-llvm"
	"build-mesa"
)

sha256sums=('SKIP' 'SKIP' 'SKIP')

prepare() {
	cd "$srcdir"

	if [ -d "mesa-$pkgver" ]; then
		mv "mesa-$pkgver" mesa-src
	fi
}

build() {
	cd "$srcdir"

	chmod +x ./build-llvm ./build-mesa

	./build-llvm
	./build-mesa mesa-src
}

package() {
	# LLVM shared library
	install -Dm755 /tmp/llvm/usr/lib/libLLVM-15.so "$pkgdir"/usr/lib/libLLVM-15.so
	ln -sf libLLVM-15.so "$pkgdir"/usr/lib/libLLVM-15.so.15

	# Mesa — ninja install creates everything (dirs, symlinks, ICDs, GLVND, pkgconfig)
	DESTDIR="$pkgdir" ninja -C "$srcdir"/mesa-build install

	# License files
	install -Dm644 "$srcdir"/llvm-project-15.0.7.src/llvm/LICENSE.TXT \
		"$pkgdir"/usr/share/licenses/"$pkgname"/LICENSE.LLVM 2>/dev/null || true
	install -Dm644 "$srcdir"/mesa-src/docs/license.rst \
		"$pkgdir"/usr/share/licenses/"$pkgname"/license.rst 2>/dev/null || true
}