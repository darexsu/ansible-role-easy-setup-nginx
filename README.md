# Ansible role Nginx
[![CI Molecule](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/57564?color=blue&label=downloads)

Molecule testing:

|  Official repo   | Nginx version      |  Third-Party repo | Nginx version | 
| ---------------- | ------------------ | ----------------- | ------------- |
| Debian 11        |   1.18.0           | nginx.org         |     Latest    | 
| Debian 10        |   1.14.2           | nginx.org         |     Latest    |   
| Ubuntu 20.04     |   1.18.0           | nginx.org         |     Latest    | 
| Ubuntu 18.04     |   1.14.0           | nginx.org         |     Latest    |   
| RockyLinux 8     |   1.14.1           | nginx.org         |     Latest    | 
| OracleLinux 8    |   1.14.1           | nginx.org         |     Latest    | 

### Select all or specific task
```bash
    install
      ├── dependencies
      └── third-party repo
    config
      ├── nginx.conf
      └── virtualhost.conf
                ├── tcp/ip socket 
                └── unix socket 

```
### 1) Install role from Galaxy
```
ansible-galaxy install darexsu.nginx --force
```

### 2) Example playbooks:
  
  - [full playbook](#full-playbook)  
    - install
      - [official repo](#example-playbook-install-from-distros-repo)
      - [third-party repo](#example-playbook-install-from-nginxorg-repo)   
    - config
      - [nginx.conf](#example-playbook-nginxconf)
      - [virtualhost.conf tcp/ip socket](#example-playbook-virtualhostconf-tcpip)
      - [virtualhost.conf unix socket](#example-playbook-virtualhostconf-unix)

##### Full playbook
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.nginx
      # install
      nginx_install: true
      # --- install  repo
      nginx_install__repo: true       
      
      # config 
      nginx_config: true
      # --- config  conf
      nginx_config__conf: true
      # --- config  vhost 
      nginx_config__vhost: true
      # --- config  vhost  php_fpm
      nginx_config__vhost__php_fpm__tcp_ip_socket: true
      nginx_config__vhost__php_fpm__tcp_ip_socket__listen: "127.0.0.1:9000"
```
##### Example playbook: install from distro's repo
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.nginx
      # install
      nginx_install: true
```
##### Example playbook: install from nginx.org repo
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.nginx
      # install
      nginx_install: true
      # --- install  repo
      nginx_install__repo: true
```
##### Example playbook: nginx.conf
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.nginx
      # --- config 
      nginx_config: true
      # --- config  conf
      nginx_config__conf: true
      nginx_config__conf__template: "nginx_config_conf.j2"
      nginx_config__conf__file: "nginx.conf"
```
##### Example playbook: virtualhost.conf tcp/ip
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.nginx
      # config
      nginx_config: true
      # --- config  vhost 
      nginx_config__vhost: true
      nginx_config__vhost__template: "nginx_config__vhost.j2"
      nginx_config__vhost__file: "default.conf"
      nginx_config__vhost__listen_port: "80"
      nginx_config__vhost__server_name: "localhost"
      # --- config  vhost  php-fpm  tcp_ip socket
      nginx_config__vhost__php_fpm__tcp_ip_socket: true
      nginx_config__vhost__php_fpm__tcp_ip_socket__listen: "127.0.0.1:9000"
```
##### Example playbook: virtualhost.conf unix
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.nginx
      # config
      nginx_config: true
      # --- config  vhost 
      nginx_config__vhost: true
      nginx_config__vhost__template: "nginx_config__vhost.j2"
      nginx_config__vhost__file: "default.conf"
      nginx_config__vhost__listen_port: "80"
      nginx_config__vhost__server_name: "localhost"
      # --- config  vhost  php-fpm  unix_socket
      nginx_config__vhost__php_fpm__unix_socket: false
      nginx_config__vhost__php_fpm__unix_socket__listen: "/var/run/php/php-fpm.sock"
```