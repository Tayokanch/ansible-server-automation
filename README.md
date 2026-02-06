# Ansible Server Configuration Project

A modular, production-ready **Ansible project** for managing servers. This project follows **best practices** for reusable, maintainable roles, focusing on automating server setup and service management.

A **Master/Control Node** is used to configure two remote nodes:

- **Web Server** – runs Nginx with templated configurations.  
- **Database Server** – runs PostgreSQL with customized configuration.

Roles are fully modular, including **tasks, handlers, templates, defaults, and variables**, enabling idempotent and testable deployments.  

This project assumes the servers already exist and focuses on:

- Bootstrapping SSH access  
- Installing and configuring services (Nginx, PostgreSQL)  
- Managing service states and configuration files  

Ideal for **multi-server environments** on-premises or in the cloud.

---


## Inventory Example (`hosts.ini`)

```ini
[webservers]
node01 ansible_host=192.168.x.x

[dbservers]
node02 ansible_host=192.168.x.x

[all_servers:children]
webservers
dbservers

[all_servers:vars]
ansible_user=xxxx
ansible_ssh_private_key_file=~/.ssh/mysshkey
```

## Playbooks

## 1. SSH Bootstrap (`ssh_bootstraps.yaml`)

```yaml
- name: Apply SSH Public to my servers
  hosts: all_servers
  become: yes
  tasks:
    - name: Add my public SSH key to my remote servers
      authorized_key:
        user: xxx
        state: present
        key: "{{ lookup('file', '~/.ssh/publickey.pub') }}"
```

## 2. Web Server Setup (`webservers.yaml`)
```yaml
- name: Configure Web Servers
  hosts: webservers
  become: yes
  roles:
    - nginx
```

## 3. Database Server Setup (`dbservers.yaml`)
```yaml
- name: Configure DB Servers
  hosts: dbservers
  become: yes
  roles:
    - postgresql
```
## Roles

## 4. Start Nginx  (`nginx/tasks/start_nginx.yaml`)

```yaml
- name: Install nginx
  apt:
    name: nginx
    state: present
```

## 5. Enable Nginx (`nginx/tasks/enable_nginx.yaml`)

```yaml
- name: Enable Nginx is running
  service:
    name: nginx
    state: started
    enabled: yes
```

## 6. Start Postgresql  (`nginx/tasks/start_postgresql.yaml`)

```yaml
- name: Install postgresql
  apt:
    name: postgresql
    state: present
```

## 7. Enable postgresql (`nginx/tasks/enable_postgresql.yaml`)

```yaml
- name: Enable postgresql is running
  service:
    name: postgresql
    state: started
    enabled: yes
```
