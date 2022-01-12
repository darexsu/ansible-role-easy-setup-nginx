# Ansible role Nginx
[![CI Molecule](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml/badge.svg)](https://github.com/darexsu/ansible-role-nginx/actions/workflows/ci.yml)
Optional:
  - install nginx
  - set configuration [ ./nginx/nginx.conf ]
  - set virtualhost [ ./nginx/conf.d ]
  - set port [ firewalld ]

Platforms: Debian, Ubuntu, RHEL, CentOS, Rocky, Oracle

Ansible: ansible.bultin

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

check "host ip" on browser