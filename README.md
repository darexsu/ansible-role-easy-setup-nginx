# Ansible role Nginx
[![CI Molecule](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml)&emsp;![](https://img.shields.io/static/v1?label=idempotence&message=ok&color=success)&emsp;![Ansible Role](https://img.shields.io/ansible/role/d/57564?color=blue&label=downloads)

|  Testing         |  Debian            |  Ubuntu         |  Rocky Linux  | Oracle Linux |
| :--------------: | :----------------: | :-------------: | :-----------: | :----------: |
| Distro release   |  10, 11            | 18.04, 20.04    |  8            | 8            |
| Third-party repo |  nginx.org         |    nginx.org    |  nginx.org    | nginx.org    |

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

Role behaviour: Replace or Merge (with "hash_behaviour=replace" in ansible.cfg):
```
# Replace             # Merge
[host_vars]           [host_vars]
---                   ---
  vars:                 vars:
    dict:                 merge:
      a: "value"            dict: 
      b: "value"              a: "value" 
                              b: "value"

# Role recursive merge:
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
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             Nginx 
      nginx:
        enabled: true
        src: "distribution"
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             install packages
      nginx_install:
        enabled: true
        packages: [nginx]
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             nginx.conf
      nginx_conf:
        enabled: true
        vars:
          user: "www-data"
          worker_processes: "auto"
          error_log: "/var/log/nginx/error.log notice"
          pidfile: "/var/run/nginx.pid"
          worker_connections: "1024"
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             virtualhost
      nginx_virtualhost:
      # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄             delete first config
        default_conf:
          enabled: true
          file: "default.conf"
          state: "absent"
      # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄             add second config
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
            fastcgi_pass: "127.0.0.1:9000"
  
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
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             Nginx 
      nginx:
        enabled: true
        src: "distribution"   # <-- enable official(distro) repo 
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             install packages
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
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             Nginx 
      nginx:
        enabled: true
        src: "third_party"   # <--  enable third-party repo 
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             install packages
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
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             Nginx 
      nginx:
        enabled: true
        src: "distribution"
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             nginx.conf
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
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             Nginx 
      nginx:
        enabled: true
        src: "distribution"
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             virtualhosts
      nginx_virtualhost:
      # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄             add new config
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
            fastcgi_pass: "127.0.0.1:9000"
  
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
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             Nginx 
      nginx:
        enabled: true
        src: "distribution"
    # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄┄             virtualhosts
      nginx_virtualhost:
      # ┌┄┄┄┄┄┄┄┄┄┄┄┄┄┄             add new config
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
            fastcgi_pass: "unix:/var/run/php/php7.4-fpm.sock"
  
  tasks:
  - name: include role darexsu.nginx
    include_role: 
      name: darexsu.nginx    
```
