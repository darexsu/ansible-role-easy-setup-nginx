# Ansible role Nginx
[![CI Molecule](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/57564?color=blue&label=downloads)

|  Testing         |  Official repo     |  Third-party repo |
| :--------------: | :----------------: | :-------------:   |
| Debian 11        |  Nginx 1.18        |    nginx.org      |
| Debian 10        |  Nginx 1.14        |    nginx.org      |
| Ubuntu 20.04     |  Nginx 1.18-1.20   |    nginx.org      |
| Ubuntu 18.04     |  Nginx 1.18-1.20   |    nginx.org      |
| Oracle Linux 8   |  Nginx 1.14-1.20   |    nginx.org      |
| Rocky Linux 8    |  Nginx 1.14-1.20   |    nginx.org      |

### 1) Install role from Galaxy
```
ansible-galaxy install darexsu.nginx --force
```

### 2) Example playbooks:
  
  - [full playbook](#full-playbook)  
    - install
      - [official repo](#install-from-official-repo)
      - [third-party repo](#install-from-nginxorg-repo)   
    - config
      - [nginx.conf](#configure-nginxconf)
      - [virtualhost.conf tcp/ip socket](#configure-virtualhostconf-tcpip)
      - [virtualhost.conf unix socket](#configure-virtualhostconf-unix)

Replace or Merge dictionaries (with "hash_behaviour=replace" in ansible.cfg):
```
# Replace             # Merge
---                   ---
  vars:                 vars:
    dict:                 merge:
      a: "value"            dict: 
      b: "value"              a: "value" 
                              b: "value"

# How does merge work?
Your vars [host_vars]  -->  default vars [current role] --> default vars [include role]
  
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
      # Nginx
      nginx:
        enabled: true
        src: "distribution"
      # Nginx -> install
      nginx_install:
        enabled: true
        packages: [nginx]
      # Nginx -> config -> nginx.conf
      nginx_conf:
        enabled: true
        vars:
          user: "www-data"
          worker_processes: "auto"
          error_log: "/var/log/nginx/error.log notice"
          pidfile: "/var/run/nginx.pid"
          worker_connections: "1024"
      # Nginx -> config -> {virtualhost}.conf
      nginx_virtualhost:
      # Nginx -> config -> delete default.conf
        default_conf:
          enabled: true
          file: "default.conf"
          state: "absent"
      # Nginx -> config -> add new.conf
        new_conf:
          enabled: true
          file: "new.conf"
          state: "present"
          src: "nginx__virtualhost.j2"
          backup: false
          vars:
            listen_port: "80"
            listen_ipv6: false
            server_name: "localhost"
            root: "/usr/share/nginx/html"
            index: "index.html index.htm index.php"
            error_page: ""
            access_log: false
            error_log: false
            tcp_ip_socket:
              enabled: false
              listen: "127.0.0.1:9000"
            unix_socket:
              enabled: true
              file: "php-fpm.sock"
 
  tasks:
  - name: include role darexsu.nginx
    include_role:
      name: darexsu.nginx
    
```
##### install from official repo
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
      # Nginx -> enable distribution repo
        src: "distribution"
      # Nginx -> install
      nginx_install:
        enabled: true
        packages: [nginx]
  
  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx

```
##### install from nginx.org repo
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
      # Nginx -> enable third-party repo
        src: "third_party"
      # Nginx -> install
      nginx_install:
        enabled: true
        packages: [nginx]
  
  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx
    
```
##### configure nginx.conf
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx 
      nginx:
        enabled: true
        src: "distribution"
      # Nginx -> config -> nginx.conf
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
##### configure virtualhost.conf tcp/ip
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
        src: "distribution"
      # Nginx -> config -> {virtualhost}.conf
      nginx_virtualhost:
      # Nginx -> config -> add new.conf
        new_conf:
          enabled: true
          file: "new.conf"
          state: "present"
          src: "nginx__virtualhost.j2"
          backup: false
          vars:
            listen_port: "80"
            listen_ipv6: false
            server_name: "localhost"
            root: "/usr/share/nginx/html"
            index: "index.html index.htm index.php"
            error_page: ""
            access_log: false
            error_log: false
      # Nginx -> config -> new.conf -> enable tcp/ip
            tcp_ip_socket:
              enabled: true
              listen: "127.0.0.1:9000"  
  
  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx
    
```
##### configure virtualhost.conf unix
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
        src: "distribution"
      # Nginx -> config -> {virtualhost}.conf
      nginx_virtualhost:
      # Nginx -> config -> add new.con
        new_conf:
          enabled: true
          file: "new.conf"
          state: "present"
          src: "nginx__virtualhost.j2"
          backup: false
          vars:
            listen_port: "80"
            listen_ipv6: false
            server_name: "localhost"
            root: "/usr/share/nginx/html"
            index: "index.html index.htm index.php"
            error_page: ""
            access_log: false
            error_log: false
      # Nginx -> config -> new.conf -> enable unix socket
            unix_socket:
              enabled: true
              file: "php-fpm.sock"  
  
  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx    
```
