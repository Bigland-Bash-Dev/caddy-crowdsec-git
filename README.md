### caddy-crowdsec-git

A custom, hardened build of the Caddy web server featuring native CrowdSec integration, AppSec (WAF) support, and a pre-compiled suite of essential modules.

🚀 Features

* **CrowdSec Multi-Layer Protection:** Built-in HTTP, AppSec (WAF), and Layer 4 bouncers for real-time threat intelligence and automated blocking.
* **Built-in DNS Providers:** Includes popular **libdns** modules (cloudflare,DuckDNS,Linode,GoDaddy,etc.) no manual xcaddy builds required.
* **Identity & SSO Portal:** Powered by **caddy-security** to provide MFA (Multi-Factor Authentication) and Single Sign-On for all your proxied services.
* **Layer 4 Networking:** Native support for high-performance TCP/UDP stream management to secure SSH, DNS, or database traffic.
* **Performance Optimization:** Includes cache-handler and imagefilter modules for lightning-fast delivery of media-heavy sites.

## 📦 Installation

You can install this package directly from the AUR using your preferred AUR helper:

# Using yay
yay -S caddy-crowdsec-git

# Using paru
paru -S caddy-crowdsec-git

# [!WARNING]
Always backup your existing Caddyfile before installation. I am not responsible for lost configurations if they are overwritten during the update process.

## Verify Installation:
To confirm that all modules were compiled successfully, check the module count:

    caddy list-modules | wc -l
    
## ⚙️ Configuration:

1. Enable the Service

Manage the Caddy daemon via systemctl:

    sudo systemctl enable --now caddy

2. Global Configuration:

To connect Caddy to your CrowdSec engine, add this global block to the very top of your Caddyfile.

[!TIP]
Generate your API key by running sudo cscli bouncers add caddy-bouncer on the host where CrowdSec is installed.

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

    
    
3. Usage Example (Reverse Proxy)

Below is an example of protecting a service (e.g., Jellyfin) with CrowdSec and AppSec enabled:

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

## Credits & Licensing

This project is a compilation of open-source work. The following core components make this build possible:

* **Caddy Server:** The core extensible web server.
* **CrowdSec bouncer module for caddy (This is not the actual crowdsec bouncer just the module) (hslatman)
* **Caddy-Security (greenpau):** For the SSO, MFA, and Authp capabilities.
* **Caddy-L4 (mholt):** Enabling raw TCP/UDP stream management.
* **Cache-Handler:** High-performance HTTP caching.
* **Libdns Suite:** Dns Modules - Cloudflare, Linode, godaddy, etc....

Maintained by: Nathan Burke (Bigland-Bash-Dev)
