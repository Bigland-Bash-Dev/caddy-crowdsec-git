# Maintainer: Nathan Burke (Bigland-Bash-Dev) <nathanburke17@outlook.com>
pkgname=caddy-crowdsec-git
pkgver=2.11.2.r30.g66c5f28
pkgrel=2
install=caddy.install
pkgdesc="Hardened caddy server with built in crowdsec and popular dns modules"
arch=('x86_64')
url="https://github.com/Bigland-Bash-Dev/caddy-crowdsec-git"
license=('Apache-2.0')
depends=('glibc' 'libcap')
makedepends=('git' 'go') # Removed xcaddy no longer needed
provides=('caddy')
conflicts=('caddy')
backup=('etc/caddy/Caddyfile')

source=('caddy.service' 'LICENSE' 'README.md' 'NOTICE' 'caddy.install')
sha256sums=('SKIP' 'SKIP' 'SKIP' 'SKIP' 'SKIP')

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
	// DNS
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
    go mod init caddy
    go get github.com/caddyserver/caddy/v2@latest
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
    # Create the default config directory
    install -dm755 "${pkgdir}/etc/caddy"
    # Create the /etc/caddy/conf.d directory
    install -dm755 "${pkgdir}/etc/caddy/conf.d"
    # Echos a empty caddyfile into /etc/caddy/ Will not overwrite your existing one.
    echo "# Caddyfile" > "${pkgdir}/etc/caddy/Caddyfile"
}
