# AdGuard Home Lab

A lightweight, self-hosted DNS filtering and privacy solution powered by **AdGuard Home** running in Docker.
Ideal for home networks, testing environments, or as a foundation for your self-hosted stack.

---

## Features

- Network-wide ad blocking and tracking protection
- Privacy protection via DNS-level filtering
- Customizable blocklists and allowlists
- Built-in DHCP server (optional)
- DNS-over-HTTPS (DoH) and DNS-over-TLS (DoT) support
- Detailed query logs and statistics
- Easy web-based administration interface

---

## Prerequisites

- Docker and Docker Compose installed
- Ports 53, 80, 443, and 3000 available on your host
- Basic understanding of DNS and networking concepts

---

## Quick Start

### 1. Clone the repository
```bash
git clone https://github.com/rsimples/adguard-home-lab.git
cd adguard-home-lab
```

### 2. Start the container
```bash
docker-compose up -d
```

### 3. Access the setup wizard
Open your browser and navigate to:
```
http://<your-server-ip>:3000
```

### 4. Complete the initial setup
- Follow the on-screen wizard to configure AdGuard Home
- Set your admin credentials (replace the default password)
- Configure upstream DNS servers if needed

### 5. Access the web interface
After setup, the web interface will be available at:
```
http://<your-server-ip>:80
```

---

## Configuration

### Environment Variables

Edit the `.env` file to customize your timezone:
```bash
TZ=America/Sao_Paulo
```

### Persistent Data

All configuration and data are stored in:
- `./volumes/adguard_conf/` - AdGuard Home configuration files
- Configuration file: `./volumes/adguard_conf/AdGuardHome.yaml`

### Ports

| Port | Protocol | Description |
|------|----------|-------------|
| 53   | TCP/UDP  | DNS server |
| 80   | TCP      | Web UI (after initial setup) |
| 443  | TCP      | HTTPS (optional) |
| 3000 | TCP      | Initial setup wizard |

---

## Usage

### Setting DNS on Client Devices

Point your device's DNS settings to your AdGuard Home server IP address.

**Router Configuration (Recommended)**
- Configure your router's DHCP settings to use AdGuard Home as the primary DNS
- This automatically applies DNS filtering to all devices on your network

**Per-Device Configuration**
- Windows: Network Adapter Settings → Internet Protocol Version 4 (TCP/IPv4)
- macOS: System Preferences → Network → Advanced → DNS
- Linux: `/etc/resolv.conf` or Network Manager
- Mobile: WiFi Settings → DNS Configuration

### Managing Filters

1. Access the web interface
2. Navigate to **Filters → DNS blocklists**
3. Add custom blocklists or enable pre-configured ones
4. Popular lists:
   - AdGuard DNS filter (enabled by default)
   - AdAway Default Blocklist
   - Steven Black's hosts

### Viewing Statistics

- Dashboard: Overview of blocked queries and top clients
- Query Log: Detailed view of all DNS queries
- Statistics: Historical data and trends

---

## Advanced Configuration

### Custom Upstream DNS

Edit `volumes/adguard_conf/AdGuardHome.yaml` and modify the `upstream_dns` section:
```yaml
upstream_dns:
  - 1.1.1.1  # Cloudflare
  - 8.8.8.8  # Google
  - 9.9.9.9  # Quad9
```

### Enable DNS-over-HTTPS (DoH)

```yaml
upstream_dns:
  - https://dns.cloudflare.com/dns-query
  - https://dns.google/dns-query
```

### Enable DNSSEC

```yaml
dns:
  enable_dnssec: true
```

---

## Maintenance

### Update AdGuard Home

```bash
docker-compose pull
docker-compose up -d
```

### View Logs

```bash
docker logs -f adguard-home
```

### Backup Configuration

```bash
cp -r ./volumes/adguard_conf ./backup-$(date +%Y%m%d)
```

### Restore Configuration

```bash
docker-compose down
cp -r ./backup-YYYYMMDD/adguard_conf ./volumes/
docker-compose up -d
```

---

## Troubleshooting

### Port 53 already in use

**Linux (systemd-resolved)**
```bash
sudo systemctl disable systemd-resolved
sudo systemctl stop systemd-resolved
```

**Check what's using port 53**
```bash
sudo lsof -i :53
```

### Cannot access web interface

- Ensure the container is running: `docker ps`
- Check firewall rules
- Verify port 80 is not in use by another service

### DNS queries not being blocked

- Verify client devices are using the correct DNS server
- Check if protection is enabled in Settings → General settings
- Review enabled filters in Filters → DNS blocklists

---

## Security Considerations

- Change the default admin password immediately after setup
- Consider running the web interface behind a reverse proxy (Nginx/Traefik)
- Use HTTPS for the web interface in production environments
- Restrict access to the web interface using firewall rules
- Regularly update AdGuard Home to the latest version

---

## Useful Links

- [AdGuard Home GitHub](https://github.com/AdguardTeam/AdGuardHome)
- [Official Documentation](https://github.com/AdguardTeam/AdGuardHome/wiki)
- [Recommended Blocklists](https://firebog.net/)
- [DNS Privacy Project](https://dnsprivacy.org/)

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## Contributing

Contributions are welcome! Feel free to:
- Report bugs and issues
- Suggest new features
- Submit pull requests

---

## Author

**Rafael Simples**

---

## Acknowledgments

- AdGuard Team for the excellent AdGuard Home software
- The open-source community for maintaining filter lists
