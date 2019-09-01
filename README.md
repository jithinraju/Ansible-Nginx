# Ansible playbook to setup simple NGINX website

Installs Nginx on Debian/Ubuntu servers.

Playbook installs and configures the Nginx from the Nginx apt repository and configures the website as per the custom NGINX configuration.


## Creating Configuration file

Here I created a simple NGINX configuration file in the master server with basic codes to run a website. You can edit the configuration with the needed options. 

- root option defines the document root of the website

NGINX configuration is copied to the clients from the master to clients

```bash

server {
        listen 80 default_server;
        listen [::]:80 default_server;
        root /home/site; <<Document root>>
        server_name _;
        location / {
                try_files $uri $uri/ =404;
        }
}

```


# Playbook

```bash

---
- name: 'install nginx'
  hosts: ubuntu
  become: yes
  tasks:

    - name: 'nginx'
      apt:
       name: nginx
       state: present

    - name: 'copy conf'
      copy:
        src: /nginx.conf
        dest: /etc/nginx/sites-available/site.cfg

    - name: 'create symlink'
      file:
        src: /etc/nginx/sites-available/site.cfg
        dest: /etc/nginx/sites-enabled/default
        state: link

    - name: 'create directory'
      file:
         path: /home/site
         state: directory

    - name: 'add web contents'
      copy:
        content: "<h1>it works</h1>"
        dest: /home/site/index.html

    - name: 'restart'
      service:
          name: nginx
          state: restarted


```
