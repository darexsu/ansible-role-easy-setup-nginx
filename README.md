# Ansible role Nginx
[![CI Molecule](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/57564?color=blue&label=downloads)

  - Role:
      - [platforms](#platforms)
      - [install](#install)
      - [Merge behaviour](#merge-behaviour)
  - Playbooks (short version):
      - [install and configure: Nginx](#install-and-configure-nginx-merge-version)
          - [install: Nginx, repository: distribution](#install-nginx-repository-distribution-merge-version)
          - [install: Nginx, repository: nginx](#install-nginx-repository-nginx-merge-version)
          - [configure: nginx.conf](#configure-nginxconf-merge-version)
          - [configure: virtualhost.conf tcp/ip socket](#configure-virtualhostconf-tcpip-socket-merge-version)
          - [configure: virtualhost.conf unix socket](#configure-virtualhostconf-unix-socket-merge-version)
          - [configure: add multiple virtualhost.conf](#configure-add-multiple-virtualhostconf-merge-version)
  - Playbooks (full version):
      - [install and configure: Nginx](#install-and-configure-nginx-full-version)
          - [install: Nginx, repository: distribution](#install-nginx-repository-distribution-full-version)
          - [install: Nginx, repository: nginx](#install-nginx-repository-nginx-full-version)
          - [configure: nginx.conf](#configure-nginxconf-full-version)
          - [configure: virtualhost.conf tcp/ip socket](#configure-virtualhostconf-tcpip-socket-full-version)
          - [configure: virtualhost.conf unix socket](#configure-virtualhostconf-unix-socket-full-version)
          - [configure: add multiple virtualhost.conf](#configure-add-multiple-virtualhostconf-full-version)

### Platforms

|  Testing         | repo: distribution | repo: nginx       |
| :--------------: | :----------------: | :-------------:   |
| Debian 11        |  Nginx 1.18        |    nginx.org      |
| Debian 10        |  Nginx 1.14        |    nginx.org      |
| Ubuntu 20.04     |  Nginx 1.18-1.20   |    nginx.org      |
| Ubuntu 18.04     |  Nginx 1.18-1.20   |    nginx.org      |
| Oracle Linux 8   |  Nginx 1.14-1.20   |    nginx.org      |
| Rocky Linux 8    |  Nginx 1.14-1.20   |    nginx.org      |

### Install

```
ansible-galaxy install darexsu.nginx --force
```

### Merge behaviour

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

##### Install and configure: Nginx (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
        repo: "nginx"
      # Nginx -> install
      nginx_install:
        enabled: true
      # Nginx -> config -> nginx.conf
      nginx_conf:
        enabled: true
        data:
          user: "www-data"
          worker_processes: "auto"
          error_log: "/var/log/nginx/error.log notice"
          pidfile: "/var/run/nginx.pid"
          worker_connections: "1024"
      #   ...
      # Nginx -> config -> {virtualhost}.conf
      nginx_virtualhost:
        default_conf:
          enabled: true
          file: "default.conf"
          state: "present"
          src: "nginx_virtualhost.j2"
          backup: false
          data:
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
##### Install: Nginx, repository: distribution (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
        repo: "distribution"
      # Nginx -> install
      nginx_install:
        enabled: true
  
  tasks:
    - name: include role darexsu.nginx
      include_role: 
        name: darexsu.nginx

```
##### Install: Nginx, repository: nginx (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
        repo: "nginx"
      # Nginx -> install
      nginx_install:
        enabled: true
  
  tasks:
    - name: include role darexsu.nginx
      include_role: 
        name: darexsu.nginx
    
```
##### Configure: nginx.conf (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
        repo: "nginx"
      # Nginx -> config -> nginx.conf
      nginx_conf:
        enabled: true
        file: "nginx.conf"
        src: "nginx_conf.j2"
        backup: false
        data:
          user: "www-data"
          worker_processes: "auto"
          error_log: "/var/log/nginx/error.log notice"
          pidfile: "/var/run/nginx.pid"
          worker_connections: "1024"
          multi_accept: "off"
          mime_file_path: "/etc/nginx/mime.types"
          access_log: "/var/log/nginx/access.log"
          sendfile: "off"
          tcp_nopush: "off"
          tcp_nodelay: "on"
          keepalive_timeout: "75s"
          keepalive_requests: "1000"
  
  tasks:
    - name: include role darexsu.nginx
      include_role: 
        name: darexsu.nginx
    

```
##### Configure: virtualhost.conf tcpip-socket (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
      # Nginx -> config -> {virtualhost}.conf
      nginx_virtualhost:
        default_conf:
          enabled: true
          file: "default.conf"
          state: "present"
          src: "nginx_virtualhost.j2"
          backup: false
          data:
            listen_port: "80"
            listen_ipv6: false
            server_name: "localhost"
            root: "/usr/share/nginx/html"
            index: "index.html index.htm index.php"
            error_page: ""
            access_log: false
            error_log: false
            tcp_ip_socket:
              enabled: true
              listen: "127.0.0.1:9000"
            unix_socket:
              enabled: false
              file: "php-fpm.sock"
  
  tasks:
    - name: include role darexsu.nginx
      include_role: 
        name: darexsu.nginx
    
```
##### Configure: virtualhost.conf unix-socket (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
      # Nginx -> config -> {virtualhost}.conf
      nginx_virtualhost:
        default_conf:
          enabled: true
          file: "default.conf"
          state: "present"
          src: "nginx_virtualhost.j2"
          backup: false
          data:
            listen_port: "80"
            listen_ipv6: false
            server_name: "localhost"
            root: "/usr/share/nginx/html"
            index: "index.html index.htm index.php"
            error_page: ""
            access_log: false
            error_log: false
            tcp_ip_socket:
              enabled: true
              listen: "127.0.0.1:9000"
            unix_socket:
              enabled: false
              file: "php-fpm.sock"
  
  tasks:
    - name: include role darexsu.nginx
      include_role: 
        name: darexsu.nginx    
```
##### Configure: add multiple virtualhost.conf (merge version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
      # Nginx -> config -> default.conf
      nginx_virtualhost:
        default_conf:
          enabled: true
          file: "default.conf"
          state: "present"
          src: "nginx_virtualhost.j2"
          backup: false
          data:
            listen_port: "80"
            listen_ipv6: false
            server_name: "localhost"
            root: "/usr/share/nginx/html"
            index: "index.html index.htm index.php"
            error_page: ""
            access_log: false
            error_log: false
            tcp_ip_socket:
              enabled: true
              listen: "127.0.0.1:9000"
            unix_socket:
              enabled: false
              file: "php-fpm.sock"
      # Nginx -> config -> new.conf
        new_conf:
          enabled: true
          file: "new.conf"
          state: "present"
          src: "nginx_virtualhost.j2"
          backup: false
          data:
            listen_port: "80"
            listen_ipv6: false
            server_name: "localhost"
            root: "/usr/share/nginx/html"
            index: "index.html index.htm index.php"
            error_page: ""
            access_log: false
            error_log: false
            tcp_ip_socket:
              enabled: true
              listen: "127.0.0.1:9001"
            unix_socket:
              enabled: false
              file: "php-fpm.sock"
  
  tasks:
    - name: include role darexsu.nginx
      include_role: 
        name: darexsu.nginx    
```
##### Install and configure: Nginx (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
        repo: "nginx"
        service:
          enabled: true
          state: "started"
      # Nginx -> install
      nginx_install:
        enabled: true
      # Nginx -> config -> nginx.conf
      nginx_conf:
        enabled: true
        file: "nginx.conf"
        src: "nginx_conf.j2"
        backup: false
        data:
          user: "www-data"
          worker_processes: "auto"
          error_log: "/var/log/nginx/error.log notice"
          pidfile: "/var/run/nginx.pid"
          worker_connections: "1024"
          multi_accept: "off"
          mime_file_path: "/etc/nginx/mime.types"
          access_log: "/var/log/nginx/access.log"
          sendfile: "off"
          tcp_nopush: "off"
          tcp_nodelay: "on"
          keepalive_timeout: "75s"
          keepalive_requests: "1000"
      # Nginx -> config -> {virtualhost}.conf
      nginx_virtualhost:
        default_conf:
          enabled: true
          file: "default.conf"
          state: "present"
          src: "nginx_virtualhost.j2"
          backup: false
          data:
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
##### Install: Nginx, repository: distribution (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
        repo: "distribution"
        service:
          enabled: true
          state: "started"
      # Nginx -> install
      nginx_install:
        enabled: true
  
  tasks:
    - name: include role darexsu.nginx
      include_role: 
        name: darexsu.nginx

```
##### Install: Nginx, repository: nginx (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
        repo: "nginx"
        service:
          enabled: true
          state: "started"
      # Nginx -> install
      nginx_install:
        enabled: true
  
  tasks:
    - name: include role darexsu.nginx
      include_role: 
        name: darexsu.nginx
    
```
##### Configure: nginx.conf (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
        repo: "nginx"
        service:
          enabled: true
          state: "started"
      # Nginx -> config -> nginx.conf
      nginx_conf:
        enabled: true
        file: "nginx.conf"
        src: "nginx_conf.j2"
        backup: false
        data:
          user: "www-data"
          worker_processes: "auto"
          error_log: "/var/log/nginx/error.log notice"
          pidfile: "/var/run/nginx.pid"
          worker_connections: "1024"
          multi_accept: "off"
          mime_file_path: "/etc/nginx/mime.types"
          access_log: "/var/log/nginx/access.log"
          sendfile: "off"
          tcp_nopush: "off"
          tcp_nodelay: "on"
          keepalive_timeout: "75s"
          keepalive_requests: "1000"
  
  tasks:
    - name: include role darexsu.nginx
      include_role: 
        name: darexsu.nginx
    

```
##### Configure: virtualhost.conf tcpip-socket (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
        repo: "nginx"
        service:
          enabled: true
          state: "started"
      # Nginx -> config -> {virtualhost}.conf
      nginx_virtualhost:
        default_conf:
          enabled: true
          file: "default.conf"
          state: "present"
          src: "nginx_virtualhost.j2"
          backup: false
          data:
            listen_port: "80"
            listen_ipv6: false
            server_name: "localhost"
            root: "/usr/share/nginx/html"
            index: "index.html index.htm index.php"
            error_page: ""
            access_log: false
            error_log: false
            tcp_ip_socket:
              enabled: true
              listen: "127.0.0.1:9000"
            unix_socket:
              enabled: false
              file: "php-fpm.sock"
  
  tasks:
    - name: include role darexsu.nginx
      include_role: 
        name: darexsu.nginx
    
```
##### Configure: virtualhost.conf unix-socket (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
        repo: "nginx"
        service:
          enabled: true
          state: "started"
      # Nginx -> config -> {virtualhost}.conf
      nginx_virtualhost:
        default_conf:
          enabled: true
          file: "default.conf"
          state: "present"
          src: "nginx_virtualhost.j2"
          backup: false
          data:
            listen_port: "80"
            listen_ipv6: false
            server_name: "localhost"
            root: "/usr/share/nginx/html"
            index: "index.html index.htm index.php"
            error_page: ""
            access_log: false
            error_log: false
            tcp_ip_socket:
              enabled: true
              listen: "127.0.0.1:9000"
            unix_socket:
              enabled: false
              file: "php-fpm.sock"
  
  tasks:
    - name: include role darexsu.nginx
      include_role: 
        name: darexsu.nginx    
```
##### Configure: add multiple virtualhost.conf (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
        repo: "nginx"
        service:
          enabled: true
          state: "started"
      # Nginx -> config -> default.conf
      nginx_virtualhost:
        default_conf:
          enabled: true
          file: "default.conf"
          state: "present"
          src: "nginx_virtualhost.j2"
          backup: false
          data:
            listen_port: "80"
            listen_ipv6: false
            server_name: "localhost"
            root: "/usr/share/nginx/html"
            index: "index.html index.htm index.php"
            error_page: ""
            access_log: false
            error_log: false
            tcp_ip_socket:
              enabled: true
              listen: "127.0.0.1:9000"
            unix_socket:
              enabled: false
              file: "php-fpm.sock"
      # Nginx -> config -> new.conf
        new_conf:
          enabled: true
          file: "new.conf"
          state: "present"
          src: "nginx_virtualhost.j2"
          backup: false
          data:
            listen_port: "80"
            listen_ipv6: false
            server_name: "localhost"
            root: "/usr/share/nginx/html"
            index: "index.html index.htm index.php"
            error_page: ""
            access_log: false
            error_log: false
            tcp_ip_socket:
              enabled: true
              listen: "127.0.0.1:9001"
            unix_socket:
              enabled: false
              file: "php-fpm.sock"
  
  tasks:
    - name: include role darexsu.nginx
      include_role: 
        name: darexsu.nginx    
```
