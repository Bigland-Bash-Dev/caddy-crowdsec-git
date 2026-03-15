caddy-crowdsec-git

A custom, hardened build of the Caddy web server, featuring integrated CrowdSec security modules, AppSec, and Caddy-L4 support.
Features

    CrowdSec Integration: Built with http, appsec, and layer4 bouncers for advanced threat prevention.

    Caddy-L4 Support: Native support for high-performance TCP/UDP stream management.

    Arch-Optimized: Native Arch Linux packaging with standardized systemd service files.

    Extensible: Compiled with 43+ modules to provide a comprehensive feature set out of the box.

Installation

You can install this package from the AUR:
Bash

yay -S caddy-crowdsec-git

Usage

The package follows standard Arch Linux file system hierarchy:

    Binary: /usr/bin/caddy

    Configuration: /etc/caddy/Caddyfile

        Note: This package will not overwrite your existing Caddyfile upon update.

    Service Management: Managed via systemd.

Starting the service

To enable and start the service:
Bash

sudo systemctl enable --now caddy

Verifying the build

To confirm the plugins were compiled successfully, you can run:
Bash

caddy list-modules | grep -E "crowdsec|l4"

Contributing / Feature Requests

If there are additional modules you would like to see included in this build, please open an Issue on GitHub.
Credits

    Caddy Server

    CrowdSec

    hslatman/caddy-crowdsec-bouncer

    mholt/caddy-l4
