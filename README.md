# Ansible role Nginx
[![CI Molecule](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml)

Molecule testing:

| Platforms |    Debian     |    Ubuntu     |    CentOS     |  Rocky Linux |
| --------- | ------------- | ------------- | ------------- | ------------ |
|  Version  |   10, 11      | 18.04, 20.04  |     7, 8      |      8       |
| repository |  nginx.org    | nginx.org   | Epel, nginx.org | Epel, nginx.org |

### 1) Install role from Galaxy
```
ansible-galaxy install darexsu.nginx --force
```

### 2) Example playbooks: 
  
  - [full playbook](#full-playbook)  
    - install
      - [from standart repo](#example-playbook-install-from-standart-repo)
      - [from nginx repo](#example-playbook-install-from-nginxorg-repo)   
    - config
      - [nginx.conf](#example-playbook-nginxconf)
      - [virtualhost.conf](#example-playbook-virtualhostconf)
    - actions
      - [allow port (firewalld)](#example-playbook-allow-port)


##### Full playbook
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.nginx
      # install
      nginx_install: true
      
      # config 
      nginx_config: true
      # config -> nginx.conf
      nginx_config__conf: true
      # config -> virtualhost ( /nginx/conf.d/*.conf )
      nginx_config__vhost: true
      
      # actions
      nginx_actions: true    
```
##### Example playbook: install from standart repo
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.nginx
      # install
      nginx_install: true
      nginx_install__debian_repo_nginx: false                                      # Enable\disble nginx repo for debian
      nginx_install__redhat_repo_nginx: false                                      # Enable\disble nginx repo for redhat

  
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
      nginx_install__debian_repo_nginx: true                                      # Enable\disble nginx repo for debian
      nginx_install__redhat_repo_nginx: true                                      # Enable\disble nginx repo for redhat
  
```
##### Example playbook: nginx.conf
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.nginx
      # config 
      nginx_config: true
      # config -> nginx.conf
      nginx_config__conf: true                                                    # Enable\disable "conf vars" below
      nginx_config__conf_template: "nginx_config_conf.j2"                         # Copy template
      nginx_config__conf_file: "/etc/nginx/nginx.conf"                            # Paste template to
      nginx_config__conf_user: "www-data"                                         # Default: "nginx". User who start nginx
      nginx_config__conf_worker_processes: "auto"                                 # Default: "1".
      nginx_config__conf_error_log: "/var/log/nginx/error.log notice"             # Default: "logs/error.log error".
      nginx_config__conf_pidfile: "/var/run/nginx.pid"                            # Default: "logs/nginx.pid".
      nginx_config__conf_worker_connections: "1024"                               # Default: "512".
      nginx_config__conf_multi_accept: "off"                                      # Default: "off".
      nginx_config__conf_mime_file_path: "/etc/nginx/mime.types"                  # Default: "".
      nginx_config__conf_access_log: "/var/log/nginx/access.log"                  # Default: "logs/access.log combined".
      nginx_config__conf_sendfile: "off"                                          # Default: "off".
      nginx_config__conf_tcp_nopush: "off"                                        # Default: "off".
      nginx_config__conf_tcp_nodelay: "on"                                        # Default: "on".
      nginx_config__conf_keepalive_timeout: "75s"                                 # Default: "75s".
      nginx_config__conf_keepalive_requests: "1000"                               # Default: "1000".
```
##### Example playbook: virtualhost.conf
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.nginx
      # config
      nginx_config: true
      # config -> virtualhost 
      nginx_config__vhost: true                                                   # Enable\disable "vhost vars" below
      nginx_config__vhost_template: "nginx_config__vhost.j2"                      # Template path
      nginx_config__vhost_file: "/etc/nginx/conf.d/default.conf"                  # Config path
      nginx_config__vhost_listen_port: "80"                                       # Default: "80". Listen port virtual host
      nginx_config__vhost_listen_ipv6: false                                      # Enable\disable ipv6
      nginx_config__vhost_server_name: "localhost"                                # Domen name
      nginx_config__vhost_root: "/usr/share/nginx/html"                           # Usually site directory
      nginx_config__vhost_index: "index.html index.htm index.php"                 # Default: "index index.html".
      nginx_config__vhost_error_page: ""                                          # Set error page
      nginx_config__vhost_access_log: false                                       # Enable\disable access log
      nginx_config__vhost_error_log: false                                        # Enable\disable error log
      # config -> virtualhost -> php-fpm -> tcp_ip socket (default)
      nginx_config__vhost_php_fpm_tcp_ip_socket: true                             # Enable\disable "tcp_ip vars" below
      nginx_config__vhost_php_fpm_tcp_ip_socket_listen: "127.0.0.1:9000"          # TCP\IP socket
```
##### Example playbook: allow port
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.nginx
      # actions
      nginx_actions: true                                                        # Enable\disable "actions vars" below
      # actions -> firewalld
      nginx_actions__firewalld_open_port: [80, 443]                               # allow port for firewalld
```