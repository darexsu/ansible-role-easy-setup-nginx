# Ansible role Nginx
[![CI Molecule](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/57564?color=blue&label=downloads)

  - Role:
      - [platforms](#platforms)
      - [install](#install)
      - [behaviour](#behaviour)
  - Playbooks (short version):
      - [install and configure: Nginx](#install-and-configure-nginx-short-version)
          - [install: Nginx from official repo](#install-nginx-from-official-repo-short-version)
          - [install: Nginx from third-party repo](#install-nginx-from-third-party-repo-short-version)
          - [configure: nginx.conf](#configure-nginxconf-short-version)
          - [configure: virtualhost.conf tcp/ip socket](#configure-virtualhostconf-tcpip-socket-short-version)
          - [configure: virtualhost.conf unix socket](#configure-virtualhostconf-unix-short-version)
          - [configure: add multiple virtualhost.conf](#configure-add-multiple-virtualhostconf-short-version)
  - Playbooks (full version):
      - [install and configure: Nginx](#install-and-configure-nginx-full-version)
          - [install: Nginx from official repo](#install-nginx-from-official-repo-full-version)
          - [install: Nginx from third-party repo](#install-nginx-from-third-party-repo-full-version)
          - [configure: nginx.conf](#configure-nginxconf-full-version)
          - [configure: virtualhost.conf tcp/ip socket](#configure-virtualhostconf-tcpip-socket-full-version)
          - [configure: virtualhost.conf unix socket](#configure-virtualhostconf-unix-full-version)
          - [configure: add multiple virtualhost.conf](#configure-add-multiple-virtualhostconf-full-version)

### Platforms

|  Testing         |  Official repo     |  Third-party repo |
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

### Behaviour

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

##### Install and configure: Nginx (short version)
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
      # Nginx -> config -> nginx.conf
      nginx_conf:
        enabled: true
        vars:
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
##### Install: Nginx from official repo (short version)
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
  
  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx

```
##### Install: Nginx from third-party repo (short version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
        src: "third_party"
      # Nginx -> install
      nginx_install:
        enabled: true
  
  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx
    
```
##### Configure: nginx.conf (short version)
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
        file: "nginx.conf"
        src: "nginx__conf.j2"
        backup: false
        vars:
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
##### Configure: virtualhost.conf tcpip-socket (short version)
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
##### Configure: virtualhost.conf unix-socket (short version)
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
##### Configure: add multiple virtualhost.conf (short version)
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
        src: "distribution"
        service:
          enabled: true
          state: "started"
      # Nginx -> install
      nginx_install:
        enabled: true
        packages: [nginx]
        dependencies:
          Debian: [apt-transport-https, ca-certificates, curl, gnupg2, lsb-release, debian-archive-keyring]
          RedHat: []
      # Nginx -> config -> nginx.conf
      nginx_conf:
        enabled: true
        file: "nginx.conf"
        src: "nginx__conf.j2"
        backup: false
        vars:
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
##### Install: Nginx from official repo (full version)
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
        service:
          enabled: true
          state: "started"
      # Nginx -> install
      nginx_install:
        enabled: true
        packages: [nginx]
        dependencies:
          Debian: [apt-transport-https, ca-certificates, curl, gnupg2, lsb-release, debian-archive-keyring]
          RedHat: []
  
  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx

```
##### Install: Nginx from third-party repo (full version)
```yaml
---
- hosts: all
  become: true

  vars:
    merge:
      # Nginx
      nginx:
        enabled: true
        src: "third_party"
        service:
          enabled: true
          state: "started"
      # Nginx -> install
      nginx_install:
        enabled: true
        packages: [nginx]
        dependencies:
          Debian: [apt-transport-https, ca-certificates, curl, gnupg2, lsb-release, debian-archive-keyring]
          RedHat: []
  
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
        src: "distribution"
        service:
          enabled: true
          state: "started"
      # Nginx -> config -> nginx.conf
      nginx_conf:
        enabled: true
        file: "nginx.conf"
        src: "nginx__conf.j2"
        backup: false
        vars:
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
        src: "distribution"
        service:
          enabled: true
          state: "started"
      # Nginx -> config -> {virtualhost}.conf
      nginx_virtualhost:
        default_conf:
          enabled: true
          file: "default.conf"
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
        src: "distribution"
        service:
          enabled: true
          state: "started"
      # Nginx -> config -> {virtualhost}.conf
      nginx_virtualhost:
        default_conf:
          enabled: true
          file: "default.conf"
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
        src: "distribution"
        service:
          enabled: true
          state: "started"
      # Nginx -> config -> default.conf
      nginx_virtualhost:
        default_conf:
          enabled: true
          file: "default.conf"
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