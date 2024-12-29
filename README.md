# Docker Compose Setup for AdGuard, WireGuard, and DuckDNS

This repository contains a Docker Compose configuration to set up a network of services including AdGuard, WireGuard (via `wg-easy`), and DuckDNS. The services are configured to run in a private network with custom IP addresses. This setup allows for VPN connections using WireGuard and routing DNS queries through AdGuard.

Once a client connects to the VPN, their individual VPN IP address will be visible in AdGuard. This allows you to apply custom DNS filtering rules for specific VPN clients based on their IP address. This can be helpful for creating personalized ad-blocking or access control policies.

## Services

### 1. AdGuard Home
AdGuard is a network-wide ad blocker and DNS server. It will handle DNS queries from VPN clients and provide DNS filtering.

- **Ports Exposed**:
  - 53: DNS over TCP/UDP
  - 80: Web UI/HTTP
  - 3000: Web UI during setup
  - 443: DNS over HTTPS (DoH)
  - 853: DNS over TLS (DoT)
  
- **Configuration**:
  - `WG_ROUTE`: Static route to direct DNS responses to the VPN clients via the WireGuard container.
  - `TZ`: Timezone setting (`Europe/Zurich` in this case).
  
### 2. WireGuard (wg-easy)
`wg-easy` is a simple web UI for managing WireGuard configurations.

- **Ports Exposed**:
  - Custom port for WireGuard (configured via `WIREGURAD_PORT`).
  - 51821: Web UI (TCP).
  
- **Configuration**:
  - `WG_HOST`: VPN endpoint address (DuckDNS domain).
  - `PASSWORD_HASH`: WireGuard UI password (hashed).
  - `WG_PORT`: Port number for WireGuard (`51829` by default).
  - `WG_DEFAULT_DNS`: Set to the IP of the AdGuard container.
  - `WG_POST_UP`: Configures iptables rules for NAT, routing, and VPN traffic.
  
### 3. DuckDNS
DuckDNS is used to manage a dynamic DNS service for your VPN endpoint. It updates your public IP address with the DuckDNS subdomain.

- **Configuration**:
  - `TOKEN`: Your DuckDNS API token.
  - `SUBDOMAINS`: Subdomain(s) to be updated with the public IP.

## Requirements

- Docker
- Docker Compose

## How to Use

1. **Clone the repository** (or copy the files):
   ```bash
   git clone https://github.com/your-repo/dynamic-dns-vpn-adguard.git
   cd dynamic-dns-vpn-adguard


2. Create a .env file:

 - Copy the .env.example file to .env and update the values:

   ```bash
   cp .env.example .env

 - Set your DuckDNS token, subdomain, WireGuard port, and other environment variables in the .env file.

3. Build and start the services:
   ```bash
   docker-compose up -d --build

4. Accessing the services:
 - AdGuard Home: 
   - Access the web UI at http://<host-ip>:3000 to complete the setup 
   - After the setup access the web UI at http://<host-ip>:80 
 - WireGuard: Access the WireGuard UI at http://<host-ip>:51821.
 - DuckDNS: This service runs in the background to update your DuckDNS subdomain with your current public IP.


## Environment Variables

 - `VPN_ENDPOINT_ADDRESS`: The DuckDNS endpoint for your VPN.
 - `WG_EASY_PASSWORD`: The hashed password for accessing the wg-easy web UI.
 - `WIREGURAD_PORT`: The port for WireGuard UDP traffic.
 - `DUCKDNS_TOKEN`: Your DuckDNS API token.
 - `DUCKDNS_SUBDOMAIN`: The subdomain to update with your public IP.
 - `TIMEZONE`: The timezone for the containers (e.g., Europe/Zurich).


## Notes
 - Add a Port Forwarding (Nat) on your Router for the UDP Port 51829 to the Docker Host IP
 - You can modify the docker-compose.yml file to adjust the port bindings or add more customizations.
 - The dynamic DNS updates will only work if the duckdns service is running and configured correctly.
	