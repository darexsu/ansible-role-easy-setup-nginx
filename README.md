# Ansible role Nginx for Debian
Options:
  - install nginx
  - edit configuration [ ./nginx/nginx.conf ]
  - edit virtualhost [ ./nginx/conf.d ]

Installation:
1) Install from Github (git installed on your server)
```
ansible-galaxy install git+https://github.com/darexsu/ansible-role-ssh.git
```
2) Example playbook
```
---
- hosts: all
  become: yes

  roles:
    - role: ansible-role-nginx
      vars:
        nginx_install: true
        nginx_settings: true
        nginx_settings_vhost: true       
```
