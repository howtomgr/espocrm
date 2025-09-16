# espocrm Installation Guide

espocrm is a free and open-source customer relationship management. EspoCRM provides flexible CRM with clean interface

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
  - RAM: 1GB minimum
  - Storage: 2GB for data
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default espocrm port)
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

# Install espocrm
sudo dnf install -y espocrm

# Enable and start service
sudo systemctl enable --now espocrm

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
espocrm --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install espocrm
sudo apt install -y espocrm

# Enable and start service
sudo systemctl enable --now espocrm

# Configure firewall
sudo ufw allow 80

# Verify installation
espocrm --version
```

### Arch Linux

```bash
# Install espocrm
sudo pacman -S espocrm

# Enable and start service
sudo systemctl enable --now espocrm

# Verify installation
espocrm --version
```

### Alpine Linux

```bash
# Install espocrm
apk add --no-cache espocrm

# Enable and start service
rc-update add espocrm default
rc-service espocrm start

# Verify installation
espocrm --version
```

### openSUSE/SLES

```bash
# Install espocrm
sudo zypper install -y espocrm

# Enable and start service
sudo systemctl enable --now espocrm

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
espocrm --version
```

### macOS

```bash
# Using Homebrew
brew install espocrm

# Start service
brew services start espocrm

# Verify installation
espocrm --version
```

### FreeBSD

```bash
# Using pkg
pkg install espocrm

# Enable in rc.conf
echo 'espocrm_enable="YES"' >> /etc/rc.conf

# Start service
service espocrm start

# Verify installation
espocrm --version
```

### Windows

```bash
# Using Chocolatey
choco install espocrm

# Or using Scoop
scoop install espocrm

# Verify installation
espocrm --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/espocrm

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
espocrm --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable espocrm

# Start service
sudo systemctl start espocrm

# Stop service
sudo systemctl stop espocrm

# Restart service
sudo systemctl restart espocrm

# Check status
sudo systemctl status espocrm

# View logs
sudo journalctl -u espocrm -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add espocrm default

# Start service
rc-service espocrm start

# Stop service
rc-service espocrm stop

# Restart service
rc-service espocrm restart

# Check status
rc-service espocrm status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'espocrm_enable="YES"' >> /etc/rc.conf

# Start service
service espocrm start

# Stop service
service espocrm stop

# Restart service
service espocrm restart

# Check status
service espocrm status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start espocrm
brew services stop espocrm
brew services restart espocrm

# Check status
brew services list | grep espocrm
```

### Windows Service Manager

```powershell
# Start service
net start espocrm

# Stop service
net stop espocrm

# Using PowerShell
Start-Service espocrm
Stop-Service espocrm
Restart-Service espocrm

# Check status
Get-Service espocrm
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream espocrm_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name espocrm.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name espocrm.example.com;

    ssl_certificate /etc/ssl/certs/espocrm.example.com.crt;
    ssl_certificate_key /etc/ssl/private/espocrm.example.com.key;

    location / {
        proxy_pass http://espocrm_backend;
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
    ServerName espocrm.example.com
    Redirect permanent / https://espocrm.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName espocrm.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/espocrm.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/espocrm.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend espocrm_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/espocrm.pem
    redirect scheme https if !{ ssl_fc }
    default_backend espocrm_backend

backend espocrm_backend
    balance roundrobin
    server espocrm1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R espocrm:espocrm /etc/espocrm
sudo chmod 750 /etc/espocrm

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
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
sudo systemctl status espocrm

# View logs
sudo journalctl -u espocrm -f

# Monitor resource usage
top -p $(pgrep espocrm)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/espocrm"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/espocrm-backup-$DATE.tar.gz" /etc/espocrm /var/lib/espocrm

echo "Backup completed: $BACKUP_DIR/espocrm-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop espocrm

# Restore from backup
tar -xzf /backup/espocrm/espocrm-backup-*.tar.gz -C /

# Start service
sudo systemctl start espocrm
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u espocrm -n 100
sudo tail -f /var/log/espocrm/espocrm.log

# Check configuration
espocrm --version

# Check permissions
ls -la /etc/espocrm
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 80

# Test connectivity
telnet localhost 80

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep espocrm)

# Check disk I/O
iotop -p $(pgrep espocrm)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  espocrm:
    image: espocrm:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/espocrm
      - ./data:/var/lib/espocrm
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update espocrm

# Debian/Ubuntu
sudo apt update && sudo apt upgrade espocrm

# Arch Linux
sudo pacman -Syu espocrm

# Alpine Linux
apk update && apk upgrade espocrm

# openSUSE
sudo zypper update espocrm

# FreeBSD
pkg update && pkg upgrade espocrm

# Always backup before updates
tar -czf /backup/espocrm-pre-update-$(date +%Y%m%d).tar.gz /etc/espocrm

# Restart after updates
sudo systemctl restart espocrm
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/espocrm

# Clean old logs
find /var/log/espocrm -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/espocrm
```

## Additional Resources

- Official Documentation: https://docs.espocrm.org/
- GitHub Repository: https://github.com/espocrm/espocrm
- Community Forum: https://forum.espocrm.org/
- Best Practices Guide: https://docs.espocrm.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
