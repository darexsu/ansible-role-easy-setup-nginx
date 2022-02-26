# Ansible role Nginx
[![CI Molecule](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/57564?color=blue&label=downloads)

|  Testing         |  Debian            |  Ubuntu         |  Rocky Linux  | Oracle Linux |
| :--------------: | :----------------: | :-------------: | :-----------: | :----------: |
| Distro version   |  10, 11            | 18.04, 20.04    |  8            | 8            |
| Third-party repo |  nginx.org         |   nginx.org     |   nginx.org   |  nginx.org   | 

### 1) Install role from Galaxy
```
ansible-galaxy install darexsu.nginx --force
```

### 2) Example playbooks:
  
  - [full playbook](#full-playbook)  
    - install
      - [official repo](#example-playbook-install-from-official-repo)
      - [third-party repo](#example-playbook-install-from-nginxorg-repo)   
    - config
      - [nginx.conf](#example-playbook-nginxconf)
      - [virtualhost.conf tcp/ip socket](#example-playbook-virtualhostconf-tcpip)
      - [virtualhost.conf unix socket](#example-playbook-virtualhostconf-unix)

Molecule testing:

|  Official repo   |  Third-Party repo   |
| ---------------- | ------------------- | 
| Debian 11        |   nginx.org         |
| Debian 10        |   nginx.org         |
| Ubuntu 20.04     |   nginx.org         |
| Ubuntu 18.04     |   nginx.org         |
| RockyLinux 8     |   nginx.org         |
| OracleLinux 8    |   nginx.org         |

Replace dictionary and Merge dictionary (with "hash_behaviour=replace" in ansible.cfg):
```
[host_vars]           [host_vars]
---                   ---
  vars:                 vars:
    dict:                 merge:  <-- # Enable Merge
      a: "value"            dict: 
      b: "value"              a: "value" 
                              b: "value"
```
Role recursive merge:
```
[host_vars]     [current role]    [include_role]
  
  dict:          dict:              dict:
    a: "1" -->     a: "1"    -->      a: "1"
                   b: "2"    -->      b: "2"
                                      c: "3"
    
```
##### Full playbook
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      nginx:
        enabled: true
      nginx_install:
        enabled: true
        packages: [nginx]
      nginx_repo:  
        epel:
          enabled: true
        nginx:
          enabled: true
      nginx_conf:
        enabled: true
        vars:
          user: "www-data"
          worker_processes: "auto"
          error_log: "/var/log/nginx/error.log notice"
          pidfile: "/var/run/nginx.pid"
          worker_connections: "1024"
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
  
  tasks:
  - name: include role darexsu.nginx
    include_role:
      name: darexsu.nginx
    
```
##### Example playbook: install from official repo
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      nginx:
        enabled: true
      nginx_install:
        enabled: true
        packages: [nginx]
  
  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx

```
##### Example playbook: install from nginx.org repo
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      nginx:
        enabled: true
      nginx_install:
        enabled: true
        packages: [nginx]
      nginx_repo:  
        epel:
          enabled: true
        nginx:
          enabled: true
  
  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx
    
```
##### Example playbook: nginx.conf
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      nginx:
        enabled: true
      nginx_conf:
        enabled: true
        vars:
          user: "www-data"
          worker_processes: "auto"
          error_log: "/var/log/nginx/error.log notice"
          pidfile: "/var/run/nginx.pid"
          worker_connections: "1024"
  
  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx
    

```
##### Example playbook: virtualhost.conf tcp/ip
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      nginx:
        enabled: true
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
  
  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx
    
```
##### Example playbook: virtualhost.conf unix
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      nginx:
        enabled: true
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
              listen: "/var/run/php/php7.4-fpm.sock"
  
  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx    
```
