**caddy-crowdsec-git *Author/Maintainer Nathan burke (lightcode)

A custom, hardened build of the Caddy web server, featuring integrated CrowdSec security modules, AppSec, and Caddy-L4 support.
Features

    CrowdSec Integration: Built with http, appsec, and layer4 bouncers for advanced threat prevention.

    Caddy-L4 Support: Native support for high-performance TCP/UDP stream management.

    Arch-Optimized: Native Arch Linux packaging with standardized systemd service files.

    Extensible: Compiled with 43+ modules to provide a comprehensive feature set out of the box.

**Installation

You can install this package from the AUR:
Bash

yay -S caddy-crowdsec-git

**Usage

The package follows standard Arch Linux file system hierarchy:

    Binary: /usr/bin/caddy

    Configuration: /etc/caddy/Caddyfile

       Note: This package will not overwrite your existing Caddyfile upon installation. Also right now this dont provide a default Caddyfile it will in the future tho.

    Service Management: Managed via systemd.

Starting the service

To enable and start the service:
Bash

sudo systemctl enable --now caddy

Verifying the build

To confirm the plugins were compiled successfully, you can run:
Bash

caddy list-modules | grep -E "crowdsec|l4"

Installation

You can install this package from the AUR using your preferred helper: yay, paru, etc....
Bash

yay -S caddy-crowdsec-git

**Configuration

*To connect your Caddy instance to your local CrowdSec engine, add the following global block to the top of your Caddyfile:
Code snippet

{
     # debug      #uncomment this to enable debug support
  email example@outlook.com # Replace with your email for Let's Encrypt
 
     crowdsec {
         api_url http://localhost:8080
         api_key <API KEY HERE>
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

Note: You can generate your API key by running sudo cscli bouncers add caddy-bouncer on your host.
Usage Example

Below is an example of how to protect a service (e.g., Jellyfin) using the security modules:
Code snippet

jellyfin.yourdomain.com {
	tls example@outlook.com # This allows lets encrypt to send you emails when they issue ssl certs! Totally optional tho
     	log
     	route {
         	crowdsec
         	appsec
         	reverse_proxy local-ip:8096
     }
 
     	log {
         	output file /var/log/caddy/jellyfin_access.log
         	format json
     	}
}


Maintenance

This package uses xcaddy to build from the latest source. To update your build, simply run your system upgrade command:

yay -Syu

Contributing / Feature Requests

If there are additional modules you would like to see included in this build, please open an Issue on GitHub.
Credits: Shoutout to the following projects! :)

    Caddy Server

    CrowdSec

    hslatman/caddy-crowdsec-bouncer

    mholt/caddy-l4
