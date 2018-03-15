---
layout: post
title:  "Django with uWSGI and Nginx on Ubuntu 16.04"
date:   2018-01-03
excerpt: "Django Applications with uWSGI and Nginx on Ubuntu 16.04"
tag:
- Django
- NGINX
- ubuntu16.04
- python3
comments: true
---

### Python3 Version - shell
```
sudo apt-get update
sudo apt-get install python3-pip

sudo -H pip3 install --upgrade pip
sudo -H pip3 install virtualenv virtualenvwrapper

echo "export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3" >> ~/.bashrc

echo "export WORKON_HOME=~/Env" >> ~/.bashrc
echo "source /usr/local/bin/virtualenvwrapper.sh" >> ~/.bashrc

source ~/.bashrc
```


### virtualenv and pip install requirements

```
mkvirtualenv firstsite

pip install -r requirements.txt
```



### Setting up the uWSGI Application Server
```
sudo apt-get install python3-dev
sudo -H pip3 install uwsgi

sudo mkdir -p /etc/uwsgi/sites
sudo nano /etc/uwsgi/sites/<project>.ini

```

```
[uwsgi]
project = <project>
uid = sammy
base = /home/%(uid)

chdir = %(base)/%(project)
home = %(base)/Env/%(project)
module = %(project).wsgi:application

master = true
processes = 5

socket = /run/uwsgi/%(project).sock
chown-socket = %(uid):www-data
chmod-socket = 660
vacuum = true
```


### Create a systemd Unit File for uWSGI
```
sudo nano /etc/systemd/system/uwsgi.service
```

```
[Unit]
Description=uWSGI Emperor service

[Service]
ExecStartPre=/bin/bash -c 'mkdir -p /run/uwsgi; chown sammy:www-data /run/uwsgi'
ExecStart=/usr/local/bin/uwsgi --emperor /etc/uwsgi/sites
Restart=always
KillSignal=SIGQUIT
Type=notify
NotifyAccess=all

[Install]
WantedBy=multi-user.target
```


### Install and Configure Nginx as a Reverse Proxy

```
sudo apt-get install nginx
sudo nano /etc/nginx/sites-available/<project>
```


```
server {
    listen 80;
    server_name <domain>;

    location = /favicon.ico { access_log off; log_not_found off; }
    location /static/ {
        root /home/<path to>/<project>;
    }

    location / {
        include         uwsgi_params;
        uwsgi_pass      unix:/run/uwsgi/<project>.sock;
    }
}
```

```
sudo ln -s /etc/nginx/sites-available/<project> /etc/nginx/sites-enabled
sudo nginx -t
sudo systemctl restart nginx
sudo systemctl start uwsgi
```


### Further Troubleshooting


```
# If you update your Django application, you can restart the uWSGI process to pick up the changes by typing:

sudo systemctl restart uwsgi


# If you change uwsgi systemd service file, reload the daemon and restart the process by typing:
sudo systemctl daemon-reload

# If you change the Nginx server block configuration, test the configuration and then Nginx by typing:
sudo nginx -t && sudo systemctl restart nginx
```

