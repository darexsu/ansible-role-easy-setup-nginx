# Ansible role Nginx
[![CI Molecule](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml)

Molecule testing:

| Platforms |    Debian     |    Ubuntu     |    CentOS     |  Rocky Linux |
| --------- | ------------- | ------------- | ------------- | ------------ |
|  Version  |   10, 11      | 18.04, 20.04  |     7, 8      |      8       |
| Nginx repository |  nginx.org    | nginx.org   | Epel    | Epel  |

Optional:
  - install nginx
  - set ./nginx/nginx.conf
  - set virtualhost ./nginx/conf.d/*.conf
  - set port  ( firewalld )

Fast start:
1) Install from Galaxy
```
ansible-galaxy install darexsu.nginx --force
```
2) Run example playbook
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.nginx
      vars:
        nginx_install: true
        nginx_settings: true
        nginx_actions: true
```
3) Testing nginx:

check "http://host_ip" on browser
