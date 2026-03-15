# Maintainer: Nathan burke <nathanburke17@outlook.com>
pkgname=caddy-crowdsec-git
pkgver=2.11.2.r1.g8e6a09b
pkgrel=1
pkgdesc="My custom build of the Caddy web server with integrated CrowdSec module, appsec support and L4 support"
arch=('x86_64')
url="https://caddyserver.com/"
license=('Apache-2.0')
makedepends=('git' 'go')
provides=('caddy')
conflicts=('caddy')

source=("caddy.service")
sha256sums=('SKIP')

pkgver() {
    printf "2.11.2.r%s.g%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

build() {
    # Install xcaddy
    go install github.com/caddyserver/xcaddy/cmd/xcaddy@latest

    # Build with the security-focused modules
    ~/go/bin/xcaddy build latest \
        --with github.com/hslatman/caddy-crowdsec-bouncer/http \
        --with github.com/hslatman/caddy-crowdsec-bouncer/appsec \
        --with github.com/hslatman/caddy-crowdsec-bouncer/layer4 \
        --with github.com/mholt/caddy-l4
}

package() {
    # Install binary and service
    install -Dm755 caddy "${pkgdir}/usr/bin/caddy"
    install -Dm644 "${srcdir}/caddy.service" "${pkgdir}/usr/lib/systemd/system/caddy.service"
    install -Dm644 README.md "${pkgdir}/usr/share/doc/${pkgname}/README.md"
    install -Dm644 LICENSE "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
    # Create a place for the config, but don't overwrite user files!
    install -dm755 "${pkgdir}/etc/caddy"
}
