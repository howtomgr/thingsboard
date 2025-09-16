# thingsboard Installation Guide

thingsboard is a free and open-source IoT platform. ThingsBoard provides open-source IoT platform

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
  - CPU: 2+ cores
  - RAM: 4GB minimum
  - Storage: 20GB for data
  - Network: HTTP/MQTT/CoAP
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 8080 (default thingsboard port)
  - MQTT on 1883
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

# Install thingsboard
sudo dnf install -y thingsboard

# Enable and start service
sudo systemctl enable --now thingsboard

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify installation
thingsboard --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install thingsboard
sudo apt install -y thingsboard

# Enable and start service
sudo systemctl enable --now thingsboard

# Configure firewall
sudo ufw allow 8080

# Verify installation
thingsboard --version
```

### Arch Linux

```bash
# Install thingsboard
sudo pacman -S thingsboard

# Enable and start service
sudo systemctl enable --now thingsboard

# Verify installation
thingsboard --version
```

### Alpine Linux

```bash
# Install thingsboard
apk add --no-cache thingsboard

# Enable and start service
rc-update add thingsboard default
rc-service thingsboard start

# Verify installation
thingsboard --version
```

### openSUSE/SLES

```bash
# Install thingsboard
sudo zypper install -y thingsboard

# Enable and start service
sudo systemctl enable --now thingsboard

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify installation
thingsboard --version
```

### macOS

```bash
# Using Homebrew
brew install thingsboard

# Start service
brew services start thingsboard

# Verify installation
thingsboard --version
```

### FreeBSD

```bash
# Using pkg
pkg install thingsboard

# Enable in rc.conf
echo 'thingsboard_enable="YES"' >> /etc/rc.conf

# Start service
service thingsboard start

# Verify installation
thingsboard --version
```

### Windows

```bash
# Using Chocolatey
choco install thingsboard

# Or using Scoop
scoop install thingsboard

# Verify installation
thingsboard --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/thingsboard

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
thingsboard --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable thingsboard

# Start service
sudo systemctl start thingsboard

# Stop service
sudo systemctl stop thingsboard

# Restart service
sudo systemctl restart thingsboard

# Check status
sudo systemctl status thingsboard

# View logs
sudo journalctl -u thingsboard -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add thingsboard default

# Start service
rc-service thingsboard start

# Stop service
rc-service thingsboard stop

# Restart service
rc-service thingsboard restart

# Check status
rc-service thingsboard status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'thingsboard_enable="YES"' >> /etc/rc.conf

# Start service
service thingsboard start

# Stop service
service thingsboard stop

# Restart service
service thingsboard restart

# Check status
service thingsboard status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start thingsboard
brew services stop thingsboard
brew services restart thingsboard

# Check status
brew services list | grep thingsboard
```

### Windows Service Manager

```powershell
# Start service
net start thingsboard

# Stop service
net stop thingsboard

# Using PowerShell
Start-Service thingsboard
Stop-Service thingsboard
Restart-Service thingsboard

# Check status
Get-Service thingsboard
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream thingsboard_backend {
    server 127.0.0.1:8080;
}

server {
    listen 80;
    server_name thingsboard.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name thingsboard.example.com;

    ssl_certificate /etc/ssl/certs/thingsboard.example.com.crt;
    ssl_certificate_key /etc/ssl/private/thingsboard.example.com.key;

    location / {
        proxy_pass http://thingsboard_backend;
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
    ServerName thingsboard.example.com
    Redirect permanent / https://thingsboard.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName thingsboard.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/thingsboard.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/thingsboard.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:8080/
    ProxyPassReverse / http://127.0.0.1:8080/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend thingsboard_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/thingsboard.pem
    redirect scheme https if !{ ssl_fc }
    default_backend thingsboard_backend

backend thingsboard_backend
    balance roundrobin
    server thingsboard1 127.0.0.1:8080 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R thingsboard:thingsboard /etc/thingsboard
sudo chmod 750 /etc/thingsboard

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
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
sudo systemctl status thingsboard

# View logs
sudo journalctl -u thingsboard -f

# Monitor resource usage
top -p $(pgrep thingsboard)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/thingsboard"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/thingsboard-backup-$DATE.tar.gz" /etc/thingsboard /var/lib/thingsboard

echo "Backup completed: $BACKUP_DIR/thingsboard-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop thingsboard

# Restore from backup
tar -xzf /backup/thingsboard/thingsboard-backup-*.tar.gz -C /

# Start service
sudo systemctl start thingsboard
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u thingsboard -n 100
sudo tail -f /var/log/thingsboard/thingsboard.log

# Check configuration
thingsboard --version

# Check permissions
ls -la /etc/thingsboard
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 8080

# Test connectivity
telnet localhost 8080

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep thingsboard)

# Check disk I/O
iotop -p $(pgrep thingsboard)

# Check connections
ss -an | grep 8080
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  thingsboard:
    image: thingsboard:latest
    ports:
      - "8080:8080"
    volumes:
      - ./config:/etc/thingsboard
      - ./data:/var/lib/thingsboard
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update thingsboard

# Debian/Ubuntu
sudo apt update && sudo apt upgrade thingsboard

# Arch Linux
sudo pacman -Syu thingsboard

# Alpine Linux
apk update && apk upgrade thingsboard

# openSUSE
sudo zypper update thingsboard

# FreeBSD
pkg update && pkg upgrade thingsboard

# Always backup before updates
tar -czf /backup/thingsboard-pre-update-$(date +%Y%m%d).tar.gz /etc/thingsboard

# Restart after updates
sudo systemctl restart thingsboard
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/thingsboard

# Clean old logs
find /var/log/thingsboard -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/thingsboard
```

## Additional Resources

- Official Documentation: https://docs.thingsboard.org/
- GitHub Repository: https://github.com/thingsboard/thingsboard
- Community Forum: https://forum.thingsboard.org/
- Best Practices Guide: https://docs.thingsboard.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
