caddy-crowdsec-git

A Highly customzed build of the Caddy web server!! 😎, featuring integrated CrowdSec modules, and enterprise-grade WAF capabilities.
This build is designed for those who want a quick easy way to get up and running without having to build plugins using xcaddy manually

CrowdSec Multi-Layer Protection: Built-in HTTP, AppSec (WAF), and Layer 4 bouncers for real-time threat intelligence and automated blocking.

Universal DNS Suite: Includes popular DNS providers (Cloudflare, DuckDNS, Linode, etc.) for seamless wildcard SSL certificates.

Identity & SSO Portal: Built with caddy-security to provide MFA (Multi-Factor Authentication) and Single Sign-On for all your proxied services.

Layer 4 Networking: Native support for high-performance TCP/UDP stream management—secure your SSH, DNS, or database traffic alongside your web apps.

Performance Cache & Media: Features cache-handler and imagefilter for lightning-fast delivery of media-heavy sites.

Compiled with 201 modules, providing an all-in-one feature set for all your home lab needs.

**Installation:

You can Install directly from the AUR with the following commands:

	yay -S caddy-crowdsec-git
	# or
	paru -S caddy-crowdsec-git

    Usage:

**Enable and start the service:

	sudo systemctl enable --now caddy #or you can use
    sudo systemctl enable caddy
    sudo systemctl start caddy

Note: Ensure your Caddyfile is configured before starting. This package replaces the need for xcaddy—all 201 modules are pre-baked into the binary out of the box also this builds from source!! and is compiled using some extra go flags check out the pkgbuild if your curious!

 You can use checksec to see how the binary is compiled
 
    sudo pacman -Syy checksec
    
    checksec file $(which caddy)

  _____ _    _ ______ _____ _  __ _____ ______ _____
 / ____| |  | |  ____/ ____| |/ // ____|  ____/ ____|
| |    | |__| | |__ | |    | ' /| (___ | |__ | |
| |    |  __  |  __|| |    |  <  \___ \|  __|| |
| |____| |  | | |___| |____| . \ ____) | |___| |____
 \_____|_|  |_|______\_____|_|\_\_____/|______\_____|

RELRO           Stack Canary      CFI               NX            PIE             RPATH      RUNPATH      Symbols         FORTIFY    Fortified   Fortifiable      Name                            
Full RELRO      No Canary Found   NO SHSTK & NO IBT NX enabled    PIE Enabled     No RPATH   No RUNPATH   No Symbols      No         0           2                /usr/bin/caddy 

**To confirm the plugins were compiled successfully:

	caddy list-modules | wc -l
	# Should return 207

Configuration

To connect your Caddy instance to your local CrowdSec engine, 
 add the following global block to the very top of your Caddyfile:


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
* **CrowdSec bouncer module for caddy (This is not the actual crowdsec bouncer just the module) (hslatman)
* **Caddy-Security (greenpau):** For the SSO, MFA, and Authp capabilities.
* **Caddy-L4 (mholt):** Enabling raw TCP/UDP stream management.
* **Cache-Handler:** High-performance HTTP caching.
* **Libdns Suite:** Dns Modules - Cloudflare, Linode, godaddy, etc....

Maintained by: Nathan Burke (Bigland-Bash-Dev)
