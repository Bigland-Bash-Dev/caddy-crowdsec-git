# Maintainer: Nathan Burke (Bigland-Bash-Dev) <nathanburke17@outlook.com>
pkgname=caddy-crowdsec-git
pkgver=2.11.2.r18.g7c9788f
pkgrel=14
pkgdesc="Caddy web server with integrated CrowdSec, AppSec, and Layer 4 modules"
arch=('x86_64')
url="https://github.com/Bigland-Bash-Dev/caddy-crowdsec-git"
license=('Apache-2.0')
depends=('glibc' 'libcap')
makedepends=('git' 'go') # Removed xcaddy
provides=('caddy')
conflicts=('caddy')
backup=('etc/caddy/Caddyfile')

source=('caddy.service' 'LICENSE' 'README.md' 'NOTICE')
sha256sums=('SKIP' 'SKIP' 'SKIP' 'SKIP')

pkgver() {
    # Since this is a -git package, we pull the version from the local source
    # If you are building from a specific repo, ensure you 'cd' into it here.
    printf "2.11.2.r%s.g%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

build() {
    export CGO_ENABLED=1
    export GOFLAGS="-buildmode=pie -trimpath -modcacherw"
    
    # Ensure clean build environment
    rm -rf "$srcdir/build"
    mkdir -p "$srcdir/build"
    cd "$srcdir/build"

    # Writing the custom Caddy entry point
    cat <<EOF > main.go
package main
import (
    caddycmd "github.com/caddyserver/caddy/v2/cmd"
    _ "github.com/caddyserver/caddy/v2/modules/standard"
    
    // Security & Bouncers
    _ "github.com/hslatman/caddy-crowdsec-bouncer/http"
    _ "github.com/hslatman/caddy-crowdsec-bouncer/appsec"
    _ "github.com/hslatman/caddy-crowdsec-bouncer/layer4"
    
    // Web Application Firewall (Coraza)
    _ "github.com/corazawaf/coraza-caddy/v2"
    
    // Traffic Control
    _ "github.com/mholt/caddy-ratelimit"
    _ "github.com/shift72/caddy-geo-ip"
    
    // Layer 4 Networking
    _ "github.com/mholt/caddy-l4"
)
func main() {
    caddycmd.Main()
}
EOF
    go mod init caddy
    go mod tidy

    # Build with system LDFLAGS to ensure PIE and hardening
    go build -o ../caddy \
        -ldflags="-linkmode=external -extldflags '${LDFLAGS}'" \
        main.go
}

package() {
    cd "$srcdir"

    # Install binary
    install -Dm755 caddy "${pkgdir}/usr/bin/caddy"

    # Install systemd service
    install -Dm644 "caddy.service" "${pkgdir}/usr/lib/systemd/system/caddy.service"

    # Licenses and Docs
    install -Dm644 "LICENSE" "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
    install -Dm644 "README.md" "${pkgdir}/usr/share/doc/${pkgname}/README.md"
    install -Dm644 "NOTICE" "${pkgdir}/usr/share/doc/${pkgname}/NOTICE"

    # Configuration directory and default Caddyfile
    install -dm755 "${pkgdir}/etc/caddy"
    echo "# Caddyfile" > "${pkgdir}/etc/caddy/Caddyfile"
}
