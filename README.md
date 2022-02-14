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

### You can Replace dictionaries
```yaml
---
- hosts: all
  become: yes

  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx
    vars:
    some_dict:              # <-- Replace dictionary
      a: value
      b: value
      c: value
```
### You can Merge dictionaries (like hash_behaviour=merge)
```yaml
---
- hosts: all
  become: yes

  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx
    vars:
      merge_dictionaries:    # <-- Enable Merge dictionary
        some_dict:
          a: value
          b: value
          c: value

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
  become: true

  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx
    vars:
      merge_dictionaries:
        # Install -> packages
        nginx_install:
          enabled: true
          packages: [nginx]
        # Install -> repository
        nginx_repo:  
          epel:
            enabled: true
          nginx:
            enabled: true
        # Config -> nginx.conf
        nginx_conf:
          enabled: true
          vars:
            user: "www-data"
            worker_processes: "auto"
            error_log: "/var/log/nginx/error.log notice"
            pidfile: "/var/run/nginx.pid"
            worker_connections: "1024"
        # Config -> {virtualhost}.conf
        nginx_virtualhost: 
          enabled: true
          file: [virtualhost.conf]
          vars:
            listen_port: "80"
            listen_ipv6: false
            server_name: "localhost"
            root: "/usr/share/nginx/html"
            index: "index.html index.htm index.php"
            error_page: ""
            access_log: false
            error_log: false
            php_fpm:
              tcp_ip_socket:
                listen: "127.0.0.1:9000"
```
##### Example playbook: install from distro's repo
```yaml
---
- hosts: all
  become: yes

  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx
    vars:
      merge_dictionaries:
         # Install -> packages
        nginx_install:
          enabled: true
          packages: [nginx]
```
##### Example playbook: install from nginx.org repo
```yaml
---
- hosts: all
  become: yes

  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx
    vars:
      merge_dictionaries:
        # Install -> packages
        nginx_install:
          enabled: true
          packages: [nginx]
        # Install -> repository
        nginx_repo:  
          epel:
            enabled: true
          nginx:
            enabled: true
```
##### Example playbook: nginx.conf
```yaml
---
- hosts: all
  become: yes

  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx
    vars:
      merge_dictionaries:
        # Config -> nginx.conf
        nginx_conf:
          enabled: true
          vars:
            user: "www-data"
            worker_processes: "auto"
            error_log: "/var/log/nginx/error.log notice"
            pidfile: "/var/run/nginx.pid"
            worker_connections: "1024"

```
##### Example playbook: virtualhost.conf tcp/ip
```yaml
---
- hosts: all
  become: yes

  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx
    vars:
      merge_dictionaries:
        # Config -> {virtualhost}.conf
        nginx_virtualhost: 
          enabled: true
          file: [virtualhost.conf]
          state: "present"
          src: "nginx_virtualhost.j2"
          vars:
            listen_port: "80"
            listen_ipv6: false
            server_name: "localhost"
            root: "/usr/share/nginx/html"
            index: "index.html index.htm index.php"
            error_page: ""
            access_log: false
            error_log: false
            php_fpm:
              tcp_ip_socket:
                listen: "127.0.0.1:9000"
```
##### Example playbook: virtualhost.conf unix
```yaml
---
- hosts: all
  become: yes

  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx
    vars:
      merge_dictionaries:
        # Config -> {virtualhost}.conf
        nginx_virtualhost: 
          enabled: true
          file: [virtualhost.conf]
          state: "present"
          src: "nginx_virtualhost.j2"
          vars:
            listen_port: "80"
            listen_ipv6: false
            server_name: "localhost"
            root: "/usr/share/nginx/html"
            index: "index.html index.htm index.php"
            error_page: ""
            access_log: false
            error_log: false
            php_fpm:
              unix_socket:
                listen: "/var/run/php7.4-fpm.sock"
```