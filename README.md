# Ansible Collection

> Production-ready Ansible playbooks for infrastructure automation

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Ansible](https://img.shields.io/badge/Ansible-2.14+-red.svg)](https://www.ansible.com/)

## Overview

A comprehensive collection of Ansible playbooks and roles for automating server provisioning, application deployment, and infrastructure management. Battle-tested in production environments.

## Features

- **Production Ready**: Tested playbooks for real-world scenarios
- **Modular Design**: Reusable roles and tasks
- **Multi-Platform**: Support for Ubuntu, Debian, CentOS, RHEL
- **Best Practices**: Following Ansible community guidelines
- **Idempotent**: Safe to run multiple times
- **Well Documented**: Clear examples and usage instructions

## Available Playbooks

### Server Provisioning

| Playbook | Description | Services |
|----------|-------------|----------|
| **LEMP Stack** | Nginx + MySQL + PHP | Nginx, MySQL 8, PHP-FPM, Redis |
| **LAMP Stack** | Apache + MySQL + PHP | Apache, MySQL 8, PHP-FPM |
| **Docker Host** | Docker & Docker Compose setup | Docker, Docker Compose, Portainer |
| **Kubernetes Node** | K8s worker node setup | kubelet, kubectl, containerd |

### Application Deployment

| Playbook | Description | Components |
|----------|-------------|------------|
| **Node.js App** | Deploy Node.js applications | Node.js, PM2, Nginx |
| **Python App** | Deploy Python/Django apps | Python, Gunicorn, Nginx |
| **Static Site** | Deploy static websites | Nginx, SSL/TLS |
| **WordPress** | Deploy WordPress sites | WordPress, MySQL, Nginx |

### DevOps Tools

| Playbook | Description | Tools |
|----------|-------------|-------|
| **Jenkins** | CI/CD server setup | Jenkins, Java, Nginx |
| **GitLab** | GitLab CE installation | GitLab, PostgreSQL, Redis |
| **Monitoring** | Prometheus + Grafana | Prometheus, Grafana, Node Exporter |
| **ELK Stack** | Log aggregation | Elasticsearch, Logstash, Kibana |

### Security & Hardening

| Playbook | Description | Features |
|----------|-------------|----------|
| **Server Hardening** | Security baseline | Firewall, fail2ban, SSH hardening |
| **SSL/TLS Setup** | Let's Encrypt automation | Certbot, auto-renewal |
| **Firewall Config** | UFW/iptables setup | Port management, rules |
| **User Management** | User & SSH key management | sudo users, SSH keys |

### Backup & Recovery

| Playbook | Description | Features |
|----------|-------------|----------|
| **Database Backup** | Automated DB backups | MySQL, PostgreSQL, MongoDB |
| **File Backup** | Server file backups | Rsync, S3 sync |
| **Disaster Recovery** | Recovery procedures | Restore scripts, documentation |

## Quick Start

### Prerequisites

- Ansible 2.14 or higher
- Python 3.8+
- SSH access to target servers
- Sudo privileges on target servers

### Installation

```bash
# Clone the repository
git clone https://github.com/IskandarKurbonov/ansible-collection.git
cd ansible-collection

# Install required collections
ansible-galaxy collection install -r requirements.yml

# Install required roles
ansible-galaxy role install -r requirements.yml
```

### Basic Usage

```bash
# Update inventory file with your servers
cp inventory/hosts.example inventory/hosts
nano inventory/hosts

# Run a playbook
ansible-playbook -i inventory/hosts playbooks/lemp-stack.yml

# Run with specific tags
ansible-playbook -i inventory/hosts playbooks/lemp-stack.yml --tags nginx

# Dry run (check mode)
ansible-playbook -i inventory/hosts playbooks/lemp-stack.yml --check

# With extra variables
ansible-playbook -i inventory/hosts playbooks/node-app.yml -e "app_version=1.2.3"
```

## Directory Structure

```
ansible-collection/
├── playbooks/
│   ├── lemp-stack.yml
│   ├── docker-host.yml
│   ├── jenkins-server.yml
│   ├── monitoring-stack.yml
│   └── server-hardening.yml
├── roles/
│   ├── common/
│   ├── nginx/
│   ├── mysql/
│   ├── php-fpm/
│   ├── docker/
│   ├── jenkins/
│   └── monitoring/
├── inventory/
│   ├── hosts.example
│   ├── group_vars/
│   └── host_vars/
├── group_vars/
│   └── all.yml
└── files/
    └── templates/
```

## Example: LEMP Stack Deployment

### Inventory Configuration

```ini
# inventory/hosts
[web_servers]
web1.example.com ansible_host=192.168.1.10
web2.example.com ansible_host=192.168.1.11

[web_servers:vars]
ansible_user=ubuntu
ansible_python_interpreter=/usr/bin/python3
```

### Group Variables

```yaml
# group_vars/web_servers.yml
nginx_version: latest
php_version: "8.2"
mysql_root_password: "{{ vault_mysql_root_password }}"
mysql_databases:
  - name: myapp
    collation: utf8mb4_unicode_ci
    encoding: utf8mb4

redis_enabled: true
```

### Run Playbook

```bash
ansible-playbook -i inventory/hosts playbooks/lemp-stack.yml
```

## Advanced Usage

### Using Ansible Vault

```bash
# Create encrypted file
ansible-vault create group_vars/vault.yml

# Edit encrypted file
ansible-vault edit group_vars/vault.yml

# Run playbook with vault
ansible-playbook -i inventory/hosts playbooks/lemp-stack.yml --ask-vault-pass
```

### Tags Usage

```bash
# Install only Nginx
ansible-playbook playbooks/lemp-stack.yml --tags nginx

# Skip database installation
ansible-playbook playbooks/lemp-stack.yml --skip-tags database

# Multiple tags
ansible-playbook playbooks/lemp-stack.yml --tags "nginx,php"
```

### Limit to Specific Hosts

```bash
# Run on single host
ansible-playbook -i inventory/hosts playbooks/lemp-stack.yml --limit web1.example.com

# Run on group
ansible-playbook -i inventory/hosts playbooks/lemp-stack.yml --limit web_servers
```

## Role Development

### Creating a New Role

```bash
# Create role structure
ansible-galaxy init roles/myapp

# Role structure
roles/myapp/
├── defaults/
│   └── main.yml
├── tasks/
│   └── main.yml
├── handlers/
│   └── main.yml
├── templates/
├── files/
├── vars/
│   └── main.yml
└── meta/
    └── main.yml
```

### Example Role Task

```yaml
# roles/nginx/tasks/main.yml
---
- name: Install Nginx
  apt:
    name: nginx
    state: present
    update_cache: yes

- name: Configure Nginx
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
    owner: root
    group: root
    mode: '0644'
  notify: restart nginx

- name: Ensure Nginx is running
  service:
    name: nginx
    state: started
    enabled: yes
```

## Best Practices

1. **Use Version Control** - Always commit playbooks to git
2. **Encrypt Secrets** - Use Ansible Vault for sensitive data
3. **Test First** - Run with `--check` before applying changes
4. **Use Tags** - Tag tasks for selective execution
5. **Idempotency** - Ensure playbooks can run multiple times safely
6. **Documentation** - Comment complex tasks and variables
7. **Variables** - Use group_vars and host_vars for configuration
8. **Handlers** - Use handlers for service restarts

## Troubleshooting

### Connection Issues

```bash
# Test connectivity
ansible -i inventory/hosts all -m ping

# Verbose output
ansible-playbook playbooks/lemp-stack.yml -vvv

# Check inventory
ansible-inventory -i inventory/hosts --list
```

### Common Errors

**Error: "Permission denied"**
```bash
# Check SSH key
ssh-copy-id user@server

# Or specify key
ansible-playbook -i inventory/hosts playbooks/lemp-stack.yml --private-key ~/.ssh/id_rsa
```

**Error: "sudo password required"**
```bash
ansible-playbook -i inventory/hosts playbooks/lemp-stack.yml --ask-become-pass
```

## Testing

```bash
# Syntax check
ansible-playbook playbooks/lemp-stack.yml --syntax-check

# Dry run
ansible-playbook playbooks/lemp-stack.yml --check --diff

# Test with Vagrant
vagrant up
ansible-playbook -i inventory/vagrant playbooks/lemp-stack.yml
```

## Contributing

Contributions are welcome! To add a new playbook:

1. Fork the repository
2. Create feature branch
3. Add playbook with proper documentation
4. Test thoroughly
5. Submit pull request

## Support

- **Issues**: [GitHub Issues](https://github.com/IskandarKurbonov/ansible-collection/issues)
- **Email**: kurbonoviskandar23@gmail.com
- **Telegram**: [@iskandar2318](https://t.me/iskandar2318)

## License

MIT License - see [LICENSE](LICENSE) file

## Author

**Iskandar Kurbonov**
- DevOps Engineer
- Infrastructure Automation Specialist
- Location: Tashkent, Uzbekistan

---

⭐ If you find this collection useful, please give it a star!
