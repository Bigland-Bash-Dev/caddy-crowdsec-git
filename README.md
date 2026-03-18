caddy-crowdsec-git

A custom, hardened build of the Caddy web server, featuring integrated CrowdSec security modules, AppSec, and Caddy-L4 support.
Features

CrowdSec Integration: Built with HTTP, AppSec, and Layer4 bouncers for advanced threat prevention.

Caddy-L4 Support: Native support for high-performance TCP/UDP stream management.

Extensible: Compiled with 43+ modules to provide a comprehensive feature set out of the box.

Installation:

You can install this package from the AUR using your preferred helper

    yay -S caddy-crowdsec-git
    paru -S caddy-crowdsec-git

Usage:

The package follows standard Arch Linux file system hierarchy:

Binary: /usr/bin/caddy --replaces the default caddy binary!

Configuration: /etc/caddy/Caddyfile

Service Management: Managed via systemd.

To enable and start the service:

    sudo systemctl enable --now caddy
    sudo systemctl enable caddy
    sudo systemctl start caddy

Verifying the build

To confirm the plugins were compiled successfully, run:

    caddy list-modules | grep -E "crowdsec|layer4"

To list all modules run 

    caddy list-modules

Configuration:

To connect your Caddy instance to your local CrowdSec engine, add the following global block to the top of your Caddyfile:

    {
    email your-email@example.com # Replace with your email for Let's Encrypt
    crowdsec {
        api_url http://localhost:8080
        api_key <API_KEY_HERE>
        ticker_interval 15s
        appsec_url http://127.0.0.1:7422
    }
    log {
        output file /var/log/caddy/access.log {
            roll_size 30MiB
            roll_keep 5
        }
    }
    }

Tip: Generate your API key by running the following command on your host terminal shell

    sudo cscli bouncers add caddy-bouncer 

Usage Example: Protecting a Service

Below is an example of how to protect a service (e.g., Jellyfin) using crowdsec and logging to a json file

Example caddyfile block:

    jellyfin.yourdomain.com {
    tls your-email@example.com
    log
    route {
	crowdsec
        appsec
        reverse_proxy 127.0.0.1:8096
    }
    log {
        output file /var/log/caddy/jellyfin_access.log
        format json
    }
    }

Maintenance:

This package uses xcaddy to build from the latest source. To update your build to the latest version, simply run your system upgrade command:

    yay -Syu

Contributing:

If there are additional modules you would like to see included in this build, please open an Issue on GitHub.
Credits

Special thanks to the following projects!

Caddy Server
CrowdSec
hslatman/caddy-crowdsec-bouncer
mholt/caddy-l4

Maintained by Nathan Burke (Lightcode)
