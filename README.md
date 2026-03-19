caddy-crowdsec-git

A custom, hardened build of the Caddy web server, featuring integrated CrowdSec security, enterprise-grade WAF capabilities, and advanced Layer 4 traffic management.
Features

    CrowdSec Integration: Built-in HTTP, AppSec (WAF), and Layer 4 bouncers for real-time threat intelligence and automated blocking.

    Enterprise Security Stack: Includes Coraza WAF (OWASP compatible), Rate Limiting, and Geo-IP filtering for deep packet inspection and regional access control.

    Caddy-L4 Support: Native support for high-performance TCP/UDP stream management, allowing you to proxy and secure non-HTTP traffic like SSH, DNS, or databases.

    Extensible & Comprehensive: Compiled with 180 modules to provide an all-in-one feature set for advanced home labs and production environments.

    Hardened Build: Manually compiled with the native Go toolchain to ensure full compatibility with Arch Linux security flags (PIE, RELRO, Stack Protector).

Installation:

You can install this package from the AUR using your preferred helper:


	yay -S caddy-crowdsec-git
	# or
	paru -S caddy-crowdsec-git

Usage:

To enable and start the service:


	sudo systemctl enable --now caddy or
	sudo systemctl enable caddy
	sudo systemctl start caddy

 Make sure you have your Caddyfile setup properly before you try to run caddy,  most of the hard work is done tho usually you would need to build this manually using xcaddy,
not anymore!! Hopefully someone

Verifying the build

To confirm the plugins were compiled successfully, run:

	caddy list-modules | grep -E "crowdsec|layer4"


Configuration

To connect your Caddy instance to your local CrowdSec engine, 
 add the following global block to the top of your Caddyfile:


	{
    	email your-email@example.com # Replace with your email so Let's Encrypt can send you updates when your certs expire!
    	crowdsec {
        	api_url http://localhost:8080
        	api_key <API_KEY_HERE> #Replace with your crowdsec api key
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

Tip: Generate your API key by running the following command on your host terminal shell:
sudo cscli bouncers add caddy-bouncer
Usage Example: Protecting a Service

Below is an example of how to protect a service (e.g., Jellyfin) using crowdsec and logging to a json file.

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

Maintenance

	yay -Syu 

Credits

    Caddy Server

    CrowdSec

    hslatman/caddy-crowdsec-bouncer

    mholt/caddy-l4

Maintained by: Nathan Burke (Bigland-Bash-Dev)
