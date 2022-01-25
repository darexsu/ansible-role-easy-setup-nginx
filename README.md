# Ansible role Nginx
[![CI Molecule](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml)

Molecule testing:

| Platforms |    Debian     |    Ubuntu     |    CentOS     |  Rocky Linux |
| --------- | ------------- | ------------- | ------------- | ------------ |
|  Version  |   10, 11      | 18.04, 20.04  |     7, 8      |      8       |
| repository |  nginx.org    | nginx.org   | Epel, nginx.org | Epel, nginx.org |

[Install from Galaxy](#install-from-galaxy)

Example playbooks: 
  
  - [full playbook](#full-playbook)  
    - install
      - [from standart repo](#install-from-standart-repo-playbook)
      - [from nginx repo](#install-from-nginxorg-repo-playbook)   
    - config
      - [nginx.conf](#nginxconf-playbook)
      - [virtualhost.conf](#virtualhostconf-playbook)
    - actions
      - [allow port (firewalld)](#allow-port-playbook)

##### Install from Galaxy
```
ansible-galaxy install darexsu.nginx --force
```
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
##### install from standart repo playbook
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.nginx
      # install
      nginx_install: true
      nginx_install__debian_nginx_repo: false                                      
      nginx_install__redhat_nginx_repo: false 
  
```
##### install from nginx.org repo playbook
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.nginx
      # install
      nginx_install: true
      nginx_install__debian_nginx_repo: true                                      
      nginx_install__redhat_nginx_repo: true 
  
```
##### nginx.conf playbook
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.nginx
      # config 
      nginx_config: true
      # config -> nginx.conf
      nginx_config__conf: true
      nginx_config__conf_template: "nginx_config_conf.j2"
      nginx_config__conf_user: "www-data"
      nginx_config__conf_worker_processes: "auto"
      nginx_config__conf_error_log: "/var/log/nginx/error.log notice"
      nginx_config__conf_pidfile: "/var/run/nginx.pid"
      nginx_config__conf_worker_connections: "1024"
      nginx_config__conf_multi_accept: "off"
      nginx_config__conf_mime_file_path: "/etc/nginx/mime.types"
      nginx_config__conf_access_log: "/var/log/nginx/access.log"
      nginx_config__conf_sendfile: "on"
      nginx_config__conf_tcp_nopush: "on"
      nginx_config__conf_tcp_nodelay: "on"
      nginx_config__conf_keepalive_timeout: "65"
      nginx_config__conf_keepalive_requests: "100"
      nginx_config__conf_file_path: "/etc/nginx/nginx.conf"
```
##### virtualhost.conf playbook
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.nginx
      # config
      nginx_config: true
      # config -> virtualhost ( /nginx/conf.d/*.conf )
      nginx_config__vhost: true
      nginx_config__vhost_template: "nginx_config__vhost.j2"
      nginx_config__vhost_file_name: "my.conf"
      nginx_config__vhost_listen_port: "80"
      nginx_config__vhost_listen_ipv6: true
      nginx_config__vhost_server_name: "localhost"
      nginx_config__vhost_root: "/usr/share/nginx/html"
      nginx_config__vhost_index: "index.html index.htm index.php"
      nginx_config__vhost_error_page: ""
      nginx_config__vhost_access_log: false
      nginx_config__vhost_error_log: false
      # config -> virtualhost -> php-fpm -> tcp_ip socket (default)
      nginx_config__vhost_php_fpm_tcp_ip_socket: true
      nginx_config__vhost_php_fpm_tcp_ip_socket_listen: "127.0.0.1:9000"
      # config -> virtualhost -> php-fpm -> unix socket 
      nginx_config__vhost_php_fpm_unix_socket: false
      nginx_config__vhost_php_fpm_unix_socket_php_version: "8.0"
      nginx_config__vhost_php_fpm_unix_socket_user: "username"
      nginx_config__vhost_php_fpm_unix_socket_listen: "unix:/run/php/php{{ nginx_config__vhost_php_fpm_unix_socket_php_version }}-{{ nginx_config__vhost_php_fpm_unix_socket_user }}.sock"
      # config -> virtualhost -> remove default.conf
      nginx_config__vhost_remove_default_conf: true  
```
##### Allow port playbook
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.nginx
      # actions
      nginx_actions: true
      # actions -> firewalld
      nginx_actions__firewalld_open_port: [80, 443]
```