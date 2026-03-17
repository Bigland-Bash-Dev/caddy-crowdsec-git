# Maintainer: Nathan Burke (Bigland-Bash-Dev) <nathanburke17@outlook.com>
pkgname=caddy-crowdsec-git
pkgver=2.11.2.r12.gce5d942
pkgrel=2
pkgdesc="A custom build of the Caddy web server with integrated CrowdSec, AppSec modules"
arch=('x86_64')
url="https://github.com/Bigland-Bash-Dev/caddy-crowdsec-git"
license=('Apache-2.0')
depends=('glibc' 'libcap')
makedepends=('git' 'go' 'xcaddy')
provides=('caddy')
conflicts=('caddy')
backup=('etc/caddy/Caddyfile')
# Add LICENSE and README.md here so makepkg tracks them
source=('caddy.service' 'LICENSE' 'README.md')
sha256sums=('SKIP' 'SKIP' 'SKIP')

pkgver() {
    # Since you're running this inside your repo, this works great
    printf "2.11.2.r%s.g%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

build() {
    # It's a good idea to make the module cache writable to avoid those "Permission Denied" errors
    export GOFLAGS="-modcacherw"

    xcaddy build latest \
        --with github.com/hslatman/caddy-crowdsec-bouncer/http \
        --with github.com/hslatman/caddy-crowdsec-bouncer/appsec \
        --with github.com/hslatman/caddy-crowdsec-bouncer/layer4 \
        --with github.com/mholt/caddy-l4
}

package() {
    # 1. Install the binary
    install -Dm755 caddy "${pkgdir}/usr/bin/caddy"

    # 2. Install the systemd service
    install -Dm644 "${srcdir}/caddy.service" "${pkgdir}/usr/lib/systemd/system/caddy.service"

    # 3. Install License and Docs using ${srcdir} to be safe
    install -Dm644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
    install -Dm644 "${srcdir}/README.md" "${pkgdir}/usr/share/doc/${pkgname}/README.md"

    # 4. Set up config directory
    install -dm755 "${pkgdir}/etc/caddy"

    # 5. Create a placeholder Caddyfile if it doesn't exist
    # Note: Using 'echo' is safer than 'touch' here to ensure the file is created in $pkgdir
    echo "# Caddyfile" > "${pkgdir}/etc/caddy/Caddyfile"
}
