# Maintainer: Nathan Burke <nathanburke17@outlook.com>
pkgname=caddy-crowdsec-git
pkgver=2.11.2.r6.gccb4f91
pkgrel=1
pkgdesc="A custom build of the Caddy web server with integrated CrowdSec, AppSec, and L4 support"
arch=('x86_64')
url="https://caddyserver.com/"
license=('Apache-2.0')
depends=('glibc' 'libcap')
makedepends=('git' 'go' 'xcaddy')
provides=('caddy')
conflicts=('caddy')
backup=('etc/caddy/Caddyfile')

source=('caddy.service' 'README.md' 'LICENSE')
sha256sums=('SKIP' 'SKIP' 'SKIP')

pkgver() {
    printf "2.11.2.r%s.g%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

build() {
    # xcaddy will output the binary in the current directory
    xcaddy build latest \
        --with github.com/hslatman/caddy-crowdsec-bouncer/http \
        --with github.com/hslatman/caddy-crowdsec-bouncer/appsec \
        --with github.com/hslatman/caddy-crowdsec-bouncer/layer4 \
        --with github.com/mholt/caddy-l4
}

package() {
    # Install binary from the build dir
    install -Dm755 caddy "${pkgdir}/usr/bin/caddy"

    # Install local files
    install -Dm644 "${srcdir}/caddy.service" "${pkgdir}/usr/lib/systemd/system/caddy.service"
    install -Dm644 "${srcdir}/README.md" "${pkgdir}/usr/share/doc/${pkgname}/README.md"
    install -Dm644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
    
    # Create the config directory
    install -dm755 "${pkgdir}/etc/caddy"
# Create an empty Caddyfile so the backup array is happy
    touch "${pkgdir}/etc/caddy/Caddyfile"
}
