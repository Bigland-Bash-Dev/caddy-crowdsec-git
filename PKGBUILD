# Maintainer: Nathan Burke (Bigland-Bash-Dev) <nathanburke17@outlook.com>
pkgname=caddy-crowdsec-git
pkgver=2.11.2.r30.g66c5f28
pkgrel=3
pkgdesc="Hardened caddy server with built-in crowdsec, Coraza WAF, and DNS modules"
arch=('x86_64')
url="https://github.com/Bigland-Bash-Dev/caddy-crowdsec-git"
license=('Apache-2.0')
depends=('glibc' 'libcap')
makedepends=('git' 'go')
provides=('caddy')
conflicts=('caddy')
backup=('etc/caddy/Caddyfile')
install=caddy.install

# We pull from your repo as the primary source
source=("${pkgname}::git+${url}.git")
sha256sums=('SKIP')

pkgver() {
    cd "$srcdir/$pkgname"
    # Pulls the version based on your repo's commit history
    printf "2.11.2.r%s.g%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

prepare() {
    cd "$srcdir/$pkgname"

    # Create build directory for the Go module
    mkdir -p build
    cd build

    # Writing the custom Caddy entry point with your specific modules
    cat <<EOF > main.go
package main

import (
    caddycmd "github.com/caddyserver/caddy/v2/cmd"

    // Standard Caddy Distribution
    _ "github.com/caddyserver/caddy/v2/modules/standard"

    // Security & Bouncers
    _ "github.com/hslatman/caddy-crowdsec-bouncer/http"
    _ "github.com/hslatman/caddy-crowdsec-bouncer/appsec"
    _ "github.com/hslatman/caddy-crowdsec-bouncer/layer4"

    // Web Application Firewall (Coraza)
    _ "github.com/corazawaf/coraza-caddy/v2"

    // Traffic Control & Networking
    _ "github.com/mholt/caddy-ratelimit"
    _ "github.com/shift72/caddy-geo-ip"
    _ "github.com/mholt/caddy-l4"
    _ "github.com/mholt/caddy-dynamicdns"

    // DNS Providers
    _ "github.com/caddy-dns/cloudflare"
    _ "github.com/caddy-dns/duckdns"
    _ "github.com/caddy-dns/digitalocean"
    _ "github.com/caddy-dns/godaddy"
    _ "github.com/caddy-dns/linode"

    // Advanced Infrastructure & Performance
    _ "github.com/caddyserver/transform-encoder"
    _ "github.com/caddyserver/replace-response"
    _ "github.com/caddyserver/cache-handler"
    _ "github.com/ueffel/caddy-imagefilter"
    _ "github.com/greenpau/caddy-security"
    _ "github.com/lucaslorentz/caddy-docker-proxy/v2"
)

func main() {
    caddycmd.Main()
}
EOF

    # Initialize module and fetch dependencies
    go mod init caddy
    go get github.com/caddyserver/caddy/v2@v2.11.2
    go mod tidy
}

build() {
    # Arch defaults: CGO is needed for some networking modules
    export CGO_ENABLED=1
    export GOFLAGS="-buildmode=pie -trimpath -modcacherw -v"

    cd "$srcdir/$pkgname/build"

    # Inject the version into the binary metadata
    local v="v${pkgver}"

    go build -o ../caddy \
        -ldflags="-s -w -X github.com/caddyserver/caddy/v2.CustomVersion=$v -linkmode=external -extldflags '${LDFLAGS}'" \
        main.go
}

package() {
    cd "$srcdir/$pkgname"

    # 1. Install Binary
    install -Dm755 caddy "${pkgdir}/usr/bin/caddy"

    # 2. Install Systemd Service (from your repo)
    install -Dm644 "caddy.service" "${pkgdir}/usr/lib/systemd/system/caddy.service"

    # 3. Licenses and Docs
    install -Dm644 "LICENSE" "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
    install -Dm644 "README.md" "${pkgdir}/usr/share/doc/${pkgname}/README.md"

    # 4. Config Directories
    install -dm755 "${pkgdir}/etc/caddy/conf.d"

    # 5. The Caddyfile (Safe Install)
    # We check if a Caddyfile exists in your repo; if not, we create a placeholder.
    # The 'backup' array in this PKGBUILD ensures pacman won't overwrite your active config.
    if [ -f "Caddyfile" ]; then
        install -Dm644 "Caddyfile" "${pkgdir}/etc/caddy/Caddyfile"
    else
        echo "# Caddyfile" > "${pkgdir}/etc/caddy/Caddyfile"
    fi
}
