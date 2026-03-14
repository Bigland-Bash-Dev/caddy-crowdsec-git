# caddy-crowdsec-git

A custom, hardened build of the Caddy web server, featuring integrated CrowdSec security modules, appsec and Caddy-L4 support.

## Features
- **CrowdSec Integration**: Includes `http`, `appsec`, and `layer4` bouncers for advanced threat prevention.
- **Caddy-L4**: Full Layer 4 support enabled for high-performance TCP/UDP stream management.
- **Arch-Optimized**: Native Arch Linux packaging with systemd support.
- **43+ Modules**: Compiled with an extensive selection of standard and non-standard Caddy modules.

## Installation
You can install this package from the [Arch User Repository (AUR)](https://aur.archlinux.org/packages/caddy-crowdsec-git/):

```bash
yay -S caddy-crowdsec-git

Usage

The package installs to standard paths:

    Binary: /usr/bin/caddy

    Configuration: /etc/caddy/Caddyfile this does not overwrite your existing Caddyfile!! this assumes your using your own caddyfile :)

    Service: Managed via systemctl

To start the service:

     sudo systemctl enable --now caddy
     
     Credits

    Caddy Server

    CrowdSec

    hslatman/caddy-crowdsec-bouncer

    mholt/caddy-l4
