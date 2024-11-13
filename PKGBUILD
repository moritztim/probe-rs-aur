# -*- mode: Shell-script; eval: (setq indent-tabs-mode 't); eval: (setq tab-width 4) -*-
# Maintainer: Dominic Meiser [git at msrd0 dot de]

_crate="probe-rs-tools"
pkgname="probe-rs"
pkgver=0.24.0
pkgrel=3
pkgdesc='A collection of on chip debugging tools to communicate with microchips.'
url='https://probe.rs'
license=('Apache-2.0' 'MIT')

depends=('gcc-libs' 'systemd-libs' 'udev')
makedepends=('cargo' 'cmake')
optdepends=('rtthost: Real-time transfer client')
conflicts=('cargo-embed' 'cargo-flash')

source=("$_crate-$pkgver.tar.gz::https://crates.io/api/v1/crates/probe-rs-tools/$pkgver/download"
		"69-probe-rs.rules::https://probe.rs/files/69-probe-rs.rules"
		"udev-reload.sh"
		"udev-reload.hook")
sha512sums=('659b5b5c8d91728d192e32342206760d1441f44de622881930afdcc25d6f8a2dcf07ed336b34bf4ceb4ef4d0a497d074627a206bf9a038a134cd15598dacfec6'
			'04cc7011120b0c04006f15a2b924f63db2a4425aa30d5a4363a15e70b902b3cfea12b5201d3e11f496707026f7c0d8827556223174c0c9b44148aa56a81f615f'
			'27d4583ccf1ebbf8571672074e8820d9f744db894a6e2b7a1f930532ea02b4dd7ef2a92e40de26f985abc38c307fa11c791e8b2ba4d72dd210cadad02e9633b0'
			'9d7df8348903a4c23e9c006a3bd416ce64bcf67c8952f5aa4b726a9bfdd4a0cf505d1c4199eeae0816254dfdf270422cee335d39052f38f72a373c7cb919f238')

# Tier 1 architectures supported by Rust (https://doc.rust-lang.org/nightly/rustc/platform-support.html#tier-1)
arch=('aarch64' 'i686' 'x86_64')

prepare() {
	cd "$srcdir/$_crate-$pkgver"
	export RUSTUP_TOOLCHAIN=stable
	cargo fetch --locked --target "$(rustc -vV | sed -n 's/host: //p')"
}

build() {
	cd "$srcdir/$_crate-$pkgver"
	export RUSTUP_TOOLCHAIN=stable
	export CARGO_TARGET_DIR=target
	CFLAGS+=" -ffat-lto-objects"

	cargo build \
		--offline \
		--locked \
		--release
}

package() {
	cd "$srcdir/$_crate-$pkgver"
	
	# Install binaries
	install -Dm755 "target/release/cargo-flash" -t "$pkgdir/usr/bin"
	install -Dm755 "target/release/cargo-embed" -t "$pkgdir/usr/bin"
	install -Dm755 "target/release/probe-rs" -t "$pkgdir/usr/bin"

	# Install udev rules
	install -Dm644 "$srcdir/69-probe-rs.rules" "$pkgdir/usr/lib/udev/rules.d/69-probe-rs.rules"

	# Install post-install hook script
	install -Dm755 "$srcdir/udev-reload.sh" "$pkgdir/usr/share/libalpm/scripts/$pkgname.sh"

	# Install hook
	install -Dm644 "$srcdir/udev-reload.hook" "$pkgdir/usr/share/libalpm/hooks/$pkgname.hook"
}
