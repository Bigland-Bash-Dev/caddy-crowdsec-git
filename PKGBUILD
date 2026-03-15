# Maintainer: Nathan burke <nathanburke17@outlook.com>
pkgname=caddy-crowdsec-git
pkgver=2.11.2.r2.g99df529
pkgrel=1
pkgdesc="My custom build of the Caddy web server with integrated CrowdSec module, appsec support and L4 support"
arch=('x86_64')
url="https://caddyserver.com/"
license=('Apache-2.0')
makedepends=('git' 'go')
provides=('caddy')
conflicts=('caddy')

# These are the files sitting in your directory next to this PKGBUILD
source=("caddy.service" "README.md" "LICENSE")
sha256sums=('SKIP' 'SKIP' 'SKIP')

pkgver() {
    printf "2.11.2.r%s.g%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

build() {
    # This installs xcaddy specifically for this build
    export GOPATH="${srcdir}/go"
    go install github.com/caddyserver/xcaddy/cmd/xcaddy@latest

    # Use the local copy we just installed
    "${srcdir}/go/bin/xcaddy" build latest \
        --with github.com/hslatman/caddy-crowdsec-bouncer/http \
        --with github.com/hslatman/caddy-crowdsec-bouncer/appsec \
        --with github.com/hslatman/caddy-crowdsec-bouncer/layer4 \
        --with github.com/mholt/caddy-l4
}


package() {
    # Install binary
    install -Dm755 caddy "${pkgdir}/usr/bin/caddy"

    # Install local files from the build sandbox ($srcdir)
    install -Dm644 "${srcdir}/caddy.service" "${pkgdir}/usr/lib/systemd/system/caddy.service"
    install -Dm644 "${srcdir}/README.md" "${pkgdir}/usr/share/doc/${pkgname}/README.md"
    install -Dm644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
    
    # Create the config directory
    install -dm755 "${pkgdir}/etc/caddy"
}
