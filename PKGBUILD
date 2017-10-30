# Maintainer: Versus Void <chaoskeeper@mail.ru>

_source_package=$(curl -s https://www.archlinux.org/packages/core/$CARCH/linux/download/ --write-out '%{redirect_url}')
if [ ! "$_source_package" ]; then
	echo "Can't find current kernel package"
	return 1
fi

_full_version=${_source_package##*linux-}
_full_version=${_full_version%%-$CARCH.pkg.tar*}
pkgver=${_full_version%-*}
pkgrel=${_full_version##*-}
_kernel_version=${pkgver%.*}

pkgname=('linux-rolling-transitional' "linux-rolling_${_full_version//[.-]/_}")
pkgbase=linux-rolling
pkgdesc="Arch's kernel how it meant to be"
arch=('i686' 'x86_64')
url="https://bugs.archlinux.org/task/16702"
license=('GPL2')
replaces=()
options=('!strip')
source=(
	$_source_package{,.sig}
	"91-grub.hook"
	"rolling.conf"
	"clean-old-kernels"
)
sha256sums=(
	"SKIP" "SKIP"
	"c6df9eee87a9c2f7dbd91106d1aed1efb93872d7f492fef3f021513e51abc8d9"
	"2a05e4b8b0f285168863415c49d7259fd4c51bb1e4312b1e9dbe9e4823e61981"
	"eb087e424d1309edeb0ca9e367d7220dbc309f768aac9d2e3de495a3ad04fb6c"
)
validpgpkeys=('5B7E3FB71B7F10329A1C03AB771DF6627EDF681F') # Tobias Powalowski <tobias.powalowski@googlemail.com>

package_linux-rolling-transitional() {
	arch=('any')
	depends=("linux-rolling_${_full_version//[.-]/_}=$_full_version")
	backup=("usr/lib/modules/extramodules-$_kernel_version-ARCH/version")
	provides=("linux=$pkgver-$pkgrel")
	conflicts=('linux')
	optdepends=('bash: cleanup script')

	mkdir -p "$pkgdir/usr/lib/modules/extramodules-$_kernel_version-ARCH"
	mv usr/lib/modules/extramodules-$_kernel_version-ARCH/version "$pkgdir/usr/lib/modules/extramodules-$_kernel_version-ARCH/"
	rmdir usr/lib/modules/extramodules-$_kernel_version-ARCH

	mkdir -p "$pkgdir/usr/share/libalpm/hooks"
	mv 91-grub.hook "$pkgdir/usr/share/libalpm/hooks/"

	mkdir -p "$pkgdir/etc"
	mv rolling.conf "$pkgdir/etc/"

	mkdir -p "$pkgdir/usr/bin"
	mv clean-old-kernels "$pkgdir/usr/bin/"
}

_package_linux-rolling() {
	pkgdesc="The Linux kernel and modules"
	backup=("etc/mkinitcpio.d/linux-$pkgver-$pkgrel.preset")
	depends=('coreutils' 'linux-firmware' 'kmod' 'mkinitcpio>=0.7')
	sed "{
		s#boot/initramfs-linux.img#boot/initramfs-linux-$pkgver-$pkgrel.img#g;
		s#boot/initramfs-linux-fallback.img#boot/initramfs-linux-fallback-$pkgver-$pkgrel.img#g;
	}" .INSTALL > ${startdir}/linux.install.pkg
	true && install=linux.install.pkg

	mkdir -p "$pkgdir/boot"
	mv boot/vmlinuz-linux "$pkgdir/boot/vmlinuz-linux-$pkgver-$pkgrel"

	sed -i "{
		s#/boot/vmlinuz-linux#/boot/vmlinuz-linux-$pkgver-$pkgrel#g;
		s#/boot/initramfs-linux.img#/boot/initramfs-linux-$pkgver-$pkgrel.img#g;
		s#/boot/initramfs-linux-fallback.img#/boot/initramfs-linux-fallback-$pkgver-$pkgrel.img#g;
	}" etc/mkinitcpio.d/linux.preset
	mkdir -p "$pkgdir/etc/mkinitcpio.d"
	mv etc/mkinitcpio.d/linux.preset "$pkgdir/etc/mkinitcpio.d/linux-$pkgver-$pkgrel.preset"

	sed -i "{
		s#boot/vmlinuz-linux#boot/vmlinuz-linux-$pkgver-$pkgrel#g;
		s#mkinitcpio -p linux#mkinitcpio -p linux-$pkgver-$pkgrel#g;
	}" usr/share/libalpm/hooks/90-linux.hook
	mkdir -p "$pkgdir/usr/share/libalpm/hooks"
	mv usr/share/libalpm/hooks/90-linux.hook "$pkgdir/usr/share/libalpm/hooks/90-linux-$pkgver-$pkgrel.hook"

	mv usr/lib "$pkgdir/usr/"

	mkdir -p "$pkgdir/usr/lib/modules/extramodules-$_kernel_version-ARCH"
}

eval "package_${pkgname[1]}() {
	$(declare -f _package_linux-rolling)
	_package_linux-rolling
}"