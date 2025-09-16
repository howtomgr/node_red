# node-red Installation Guide

node-red is a free and open-source flow-based programming. Node-RED provides flow-based development for IoT

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 512MB minimum
  - Storage: 1GB for flows
  - Network: HTTP/MQTT
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 1880 (default node-red port)
  - None
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install node-red
sudo dnf install -y node_red

# Enable and start service
sudo systemctl enable --now node-red

# Configure firewall
sudo firewall-cmd --permanent --add-port=1880/tcp
sudo firewall-cmd --reload

# Verify installation
node-red --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install node-red
sudo apt install -y node_red

# Enable and start service
sudo systemctl enable --now node-red

# Configure firewall
sudo ufw allow 1880

# Verify installation
node-red --version
```

### Arch Linux

```bash
# Install node-red
sudo pacman -S node_red

# Enable and start service
sudo systemctl enable --now node-red

# Verify installation
node-red --version
```

### Alpine Linux

```bash
# Install node-red
apk add --no-cache node_red

# Enable and start service
rc-update add node-red default
rc-service node-red start

# Verify installation
node-red --version
```

### openSUSE/SLES

```bash
# Install node-red
sudo zypper install -y node_red

# Enable and start service
sudo systemctl enable --now node-red

# Configure firewall
sudo firewall-cmd --permanent --add-port=1880/tcp
sudo firewall-cmd --reload

# Verify installation
node-red --version
```

### macOS

```bash
# Using Homebrew
brew install node_red

# Start service
brew services start node_red

# Verify installation
node-red --version
```

### FreeBSD

```bash
# Using pkg
pkg install node_red

# Enable in rc.conf
echo 'node-red_enable="YES"' >> /etc/rc.conf

# Start service
service node-red start

# Verify installation
node-red --version
```

### Windows

```bash
# Using Chocolatey
choco install node_red

# Or using Scoop
scoop install node_red

# Verify installation
node-red --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/node_red

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
node-red --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable node-red

# Start service
sudo systemctl start node-red

# Stop service
sudo systemctl stop node-red

# Restart service
sudo systemctl restart node-red

# Check status
sudo systemctl status node-red

# View logs
sudo journalctl -u node-red -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add node-red default

# Start service
rc-service node-red start

# Stop service
rc-service node-red stop

# Restart service
rc-service node-red restart

# Check status
rc-service node-red status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'node-red_enable="YES"' >> /etc/rc.conf

# Start service
service node-red start

# Stop service
service node-red stop

# Restart service
service node-red restart

# Check status
service node-red status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start node_red
brew services stop node_red
brew services restart node_red

# Check status
brew services list | grep node_red
```

### Windows Service Manager

```powershell
# Start service
net start node-red

# Stop service
net stop node-red

# Using PowerShell
Start-Service node-red
Stop-Service node-red
Restart-Service node-red

# Check status
Get-Service node-red
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream node_red_backend {
    server 127.0.0.1:1880;
}

server {
    listen 80;
    server_name node_red.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name node_red.example.com;

    ssl_certificate /etc/ssl/certs/node_red.example.com.crt;
    ssl_certificate_key /etc/ssl/private/node_red.example.com.key;

    location / {
        proxy_pass http://node_red_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName node_red.example.com
    Redirect permanent / https://node_red.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName node_red.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/node_red.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/node_red.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:1880/
    ProxyPassReverse / http://127.0.0.1:1880/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend node_red_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/node_red.pem
    redirect scheme https if !{ ssl_fc }
    default_backend node_red_backend

backend node_red_backend
    balance roundrobin
    server node_red1 127.0.0.1:1880 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R node_red:node_red /etc/node_red
sudo chmod 750 /etc/node_red

# Configure firewall
sudo firewall-cmd --permanent --add-port=1880/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status node-red

# View logs
sudo journalctl -u node-red -f

# Monitor resource usage
top -p $(pgrep node_red)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/node_red"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/node_red-backup-$DATE.tar.gz" /etc/node_red /var/lib/node_red

echo "Backup completed: $BACKUP_DIR/node_red-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop node-red

# Restore from backup
tar -xzf /backup/node_red/node_red-backup-*.tar.gz -C /

# Start service
sudo systemctl start node-red
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u node-red -n 100
sudo tail -f /var/log/node_red/node_red.log

# Check configuration
node-red --version

# Check permissions
ls -la /etc/node_red
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 1880

# Test connectivity
telnet localhost 1880

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep node_red)

# Check disk I/O
iotop -p $(pgrep node_red)

# Check connections
ss -an | grep 1880
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  node_red:
    image: node_red:latest
    ports:
      - "1880:1880"
    volumes:
      - ./config:/etc/node_red
      - ./data:/var/lib/node_red
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update node_red

# Debian/Ubuntu
sudo apt update && sudo apt upgrade node_red

# Arch Linux
sudo pacman -Syu node_red

# Alpine Linux
apk update && apk upgrade node_red

# openSUSE
sudo zypper update node_red

# FreeBSD
pkg update && pkg upgrade node_red

# Always backup before updates
tar -czf /backup/node_red-pre-update-$(date +%Y%m%d).tar.gz /etc/node_red

# Restart after updates
sudo systemctl restart node-red
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/node_red

# Clean old logs
find /var/log/node_red -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/node_red
```

## Additional Resources

- Official Documentation: https://docs.node_red.org/
- GitHub Repository: https://github.com/node_red/node_red
- Community Forum: https://forum.node_red.org/
- Best Practices Guide: https://docs.node_red.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
