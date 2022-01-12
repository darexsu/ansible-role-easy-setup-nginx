# Ansible role Nginx for Debian
Optional:
  - install nginx
  - edit configuration [ ./nginx/nginx.conf ]
  - edit virtualhost [ ./nginx/conf.d ]

Installation:
1) Install from Galaxy
```
ansible-galaxy install darexsu.nginx
```
2) Example playbook
```yaml
---
- hosts: all
  become: yes

  roles:
    - role: darexsu.nginx
      vars:
        nginx_install: true
        nginx_settings: true
        nginx_settings__vhost: true       
```
