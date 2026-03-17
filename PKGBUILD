# Maintainer: Nathan Burke (Bigland-Bash-Dev) <nathanburke17@outlook.com>
pkgname=caddy-crowdsec-git
pkgver=2.11.2.r12.gce5d942
pkgrel=3  # Bumped this to 3 since we are adding new source files
pkgdesc="A custom build of the Caddy web server with integrated CrowdSec, AppSec modules"
arch=('x86_64')
url="https://github.com/Bigland-Bash-Dev/caddy-crowdsec-git"
license=('Apache-2.0')
depends=('glibc' 'libcap')
makedepends=('git' 'go' 'xcaddy')
provides=('caddy')
conflicts=('caddy')
backup=('etc/caddy/Caddyfile')

# Add LICENSE and README.md here so they are tracked and moved to srcdir
source=('caddy.service' 'LICENSE' 'README.md')
sha256sums=('SKIP' 'SKIP' 'SKIP')

pkgver() {
    printf "2.11.2.r%s.g%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)"
}

build() {
    # This flag prevents the "Permission Denied" errors in the Go cache later
    export GOFLAGS="-modcacherw"

    xcaddy build latest \
        --with github.com/hslatman/caddy-crowdsec-bouncer/http \
        --with github.com/hslatman/caddy-crowdsec-bouncer/appsec \
        --with github.com/hslatman/caddy-crowdsec-bouncer/layer4 \
        --with github.com/mholt/caddy-l4
}

package() {
    # Install binary
    install -Dm755 caddy "${pkgdir}/usr/bin/caddy"

    # Install systemd service
    install -Dm644 "${srcdir}/caddy.service" "${pkgdir}/usr/lib/systemd/system/caddy.service"

    # Use ${srcdir} to find the license and readme
    install -Dm644 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
    install -Dm644 "${srcdir}/README.md" "${pkgdir}/usr/share/doc/${pkgname}/README.md"

    # Setup config dir
    install -dm755 "${pkgdir}/etc/caddy"

    # Create an empty Caddyfile if it doesn't exist
    if [ ! -f "${pkgdir}/etc/caddy/Caddyfile" ]; then
        echo "# Caddyfile" > "${pkgdir}/etc/caddy/Caddyfile"
    fi
}
