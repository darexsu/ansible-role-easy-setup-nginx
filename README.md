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
      nginx_install__debian_repo_nginx: false
      nginx_install__redhat_repo_nginx: false

  
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
      nginx_install__debian_repo_nginx: true
      nginx_install__redhat_repo_nginx: true
  
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
      nginx_config__conf: true
      nginx_config__conf_template: "nginx_config_conf.j2"
      nginx_config__conf_file: "/etc/nginx/nginx.conf"
      nginx_config__conf_user: "www-data"
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
      nginx_config__vhost: true
      nginx_config__vhost_template: "nginx_config__vhost.j2"
      nginx_config__vhost_file: "/etc/nginx/conf.d/default.conf"
      nginx_config__vhost_listen_port: "80"
      nginx_config__vhost_server_name: "localhost"
      # config -> virtualhost -> php-fpm -> tcp_ip socket (default)
      nginx_config__vhost_php_fpm_tcp_ip_socket: true
      nginx_config__vhost_php_fpm_tcp_ip_socket_listen: "127.0.0.1:9000"
```
##### Example playbook: allow port
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