<p align="center">
  <img src=".img/logo.png" alt="Socks anonymizer Logo" width="300"/>
</p>

# SOCKS anonymizer - VPN SOCKS5 Proxy

SOCKS anonymizer is a Docker-based solution that provides a secure SOCKS5 proxy (powered by Dante) routing all your traffic through NordVPN (OpenVPN).

## Features

* SOCKS5 proxy with username/password authentication (PAM)
* Traffic routed securely via NordVPN servers (OpenVPN)
* DNS leak protection
* Automatic route handling for external access
* Lightweight Debian-based Docker image

## Project structure

````
.
├── README.md
├── LICENSE
├── .env.example
├── docker-compose.yml
├── .img
    ├── logo.png
└── service
    ├── Dockerfile
    ├── sockd.conf
    ├── startup.sh
    └── configs
        ├── at108.nordvpn.com.tcp.ovpn
        ├── au663.nordvpn.com.tcp.ovpn
        ├── hk211.nordvpn.com.tcp.ovpn
        ├── no149.nordvpn.com.tcp.ovpn
        ├── us6914.nordvpn.com.tcp.ovpn
        ├── us6918.nordvpn.com.tcp.ovpn
        ├── de345.nordvpn.com.tcp.ovpn
        ├── fr456.nordvpn.com.tcp.ovpn
        └── gb789.nordvpn.com.tcp.ovpn
```bash
git clone <repo-url>
cd socks-anonymizer
````

2. Create a `.env` file based on the provided example

```bash
cp .env.example .env
```

Edit `.env` and insert your credentials:

```ini
OPENVPN_USERNAME=your_nordvpn_username
OPENVPN_PASSWORD=your_nordvpn_password
NORDVPN_CONFIG=us6918.nordvpn.com.tcp.ovpn
PROXY_PASSWORD=socks-anonymizer-password
```

3. Build and run the container

```bash
docker-compose build
docker-compose up -d
```

## Usage

### Test the proxy (local or remote)

```bash
# From the local machine:
curl -x socks5h://proxyuser:socks-anonymizer-password@localhost:1080 https://ip.me

# From a remote machine:
curl -x socks5h://proxyuser:socks-anonymizer-password@<your_server_public_ip>:1080 https://ip.me
```

You can use either `localhost` or your server's public IP, allowing remote devices to use the SOCKS5 proxy securely.

## Customization

* Change VPN server: edit `NORDVPN_CONFIG` in `.env`
* Change proxy password: edit `PROXY_PASSWORD` in `.env`
* Proxy port: default is 1080 (see `docker-compose.yml`)

## Troubleshooting

### Logs

```bash
docker compose logs -f socks-anonymizer
```

### Check container external IP

```bash
docker compose exec socks-anonymizer curl https://ip.me
```

## Healthcheck

The container uses this healthcheck to verify proxy functionality:

```yaml
healthcheck:
  test: ["CMD-SHELL", "curl --fail -s --socks5 127.0.0.1:1080 --proxy-user proxyuser:${PROXY_PASSWORD} https://www.google.com"]
  interval: 120s
  timeout: 10s
  retries: 3
  start_period: 30s
```

## Future Improvements

Here are some ideas for future enhancements:

* Parametrize proxy username (currently fixed as `proxyuser`)
* Add support for NordVPN UDP configs
* Implement automatic VPN server rotation or failover
* Integrate support for other VPN providers (e.g. ProtonVPN, Mullvad)
* Add kill-switch mode with `iptables` to block non-VPN traffic
* Add multi-user authentication with PAM or a user list
* Add support for Docker health status propagation to host monitoring tools

## License

MIT License
