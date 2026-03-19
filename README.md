caddy-crowdsec-git

A custom, powerhouse build of the Caddy web server, featuring integrated CrowdSec security, enterprise-grade WAF capabilities, and a universal DNS-01 challenge suite.
Features

    CrowdSec Multi-Layer Protection: Built-in HTTP, AppSec (WAF), and Layer 4 bouncers for real-time threat intelligence and automated blocking.

    Universal DNS Suite: Includes over 10+ DNS providers (Cloudflare, DuckDNS, Linode, Porkbun, etc.) for seamless wildcard SSL certificates via DNS-01 challenges.

    Identity & SSO Portal: Built with caddy-security to provide MFA (Multi-Factor Authentication) and Single Sign-On for all your proxied services.

    Layer 4 Networking: Native support for high-performance TCP/UDP stream management—secure your SSH, DNS, or database traffic alongside your web apps.

    Performance Cache & Media: Features cache-handler and imagefilter for lightning-fast delivery of media-heavy sites (like photography portfolios).

    The "Double Century" Build: Compiled with 207 modules, providing an all-in-one feature set for advanced home labs and Proxmox environments.

Installation

Install directly from the AUR:

	yay -S caddy-crowdsec-git
	# or
	paru -S caddy-crowdsec-git

Usage

Enable and start the service:

	sudo systemctl enable --now caddy

Note: Ensure your Caddyfile is configured before starting. This package replaces the need for xcaddy—all 207 modules are pre-baked into the binary.

To confirm the plugins were compiled successfully:

	caddy list-modules | wc -l
	# Should return 200+

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

## Credits & Licensing

This project is a compilation of open-source work. The following core components make this build possible:

* **Caddy Server:** The core extensible web server.
* **CrowdSec (hslatman):** The logic behind our real-time threat remediation.
* **Caddy-Security (greenpau):** For the SSO, MFA, and Authp capabilities.
* **Caddy-L4 (mholt):** Enabling raw TCP/UDP stream management.
* **Cache-Handler:** High-performance HTTP caching.
* **Libdns Suite:** Powering our 10+ DNS-01 provider integrations.

For a full list of bundled dependencies and legal notices, please see the [NOTICE](./NOTICE) file.
Maintained by: Nathan Burke (Bigland-Bash-Dev)
