				**caddy-crowdsec-git**

A Custom build of the Caddy web server with crowdsec-module integration and Appsec 😎

This build is designed for those who want a quick easy way to get up and running with a working caddy/crowdsec config!

CrowdSec Multi-Layer Protection: Built-in HTTP, AppSec (WAF), and Layer 4 bouncers for real-time threat intelligence and automated blocking.

Includes popular DNS provider modules (Cloudflare, DuckDNS, Linode, Godaddy etc.) these modules usually have to be built

Identity & SSO Portal: Built with caddy-security module to provide MFA (Multi-Factor Authentication) and Single Sign-On for all your proxied services.

Layer 4 Networking: Native support for high-performance TCP/UDP stream management—secure your SSH, DNS, or database traffic

Features the cache-handler module and imagefilter module for lightning-fast delivery of media-heavy sites.

providing an all-in-one feature set for all your home lab needs.

**Installation:

You can Install directly from the AUR with the following commands:

	yay -S caddy-crowdsec-git
	
    # or
	
    paru -S caddy-crowdsec-git



Ensure your Caddyfile is configured properly before starting or your going to get errors Right now this comes with a blank Caddyfile because i assume if anyone wants to use it they are going to write thier own thier are examples down below tho to get you started with a working caddy/crowdsec config.



**To confirm the plugins were compiled successfully you can list the number count using the following command!

	caddy list-modules | wc -l
	# Should return 201

Configuration

Next you can enable and start the service using systemctl

	sudo systemctl enable --now caddy or
   	 sudo systemctl enable caddy
    	sudo systemctl start caddy


To connect your Caddy server to your local CrowdSec engine,
add the following global block to the very top of your Caddyfile, figured id provide an example to help someone out!


	{
    	email your-email@example.com # Replace with your email (Optional)
    	crowdsec {
        	api_url http://localhost:8080 # This dont have to be localhost! basically where ever your crowdsec engine is running
        	api_key <API_KEY_HERE> # Replace with your crowdsec api key that you generated. 
        	ticker_interval 15s
        	appsec_url http://127.0.0.1:7422 # same as your crowdsec api!!
    	}
    	log {
        	output file /var/log/caddy/access.log {
            	roll_size 30MiB
            	roll_keep 5
        	}
    	}
	}

    Tip: Generate your API key by running the following command on your host terminal shell (Where ever you installed crowdsec assuming it's on the same server as caddy

    sudo cscli bouncers add caddy-bouncer


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

Updating is done via pacman

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
