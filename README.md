# is2_configuracion
Configuración de PIPENV para proyecto en IS2

# /etc/systemd/system/gunicorn.service

[Unit]
Description=gunicorn daemon
Requires=gunicorn.socket
After=network.target

[Service]
User=user
Group=www-data
WorkingDirectory=/home/user/Desktop/produccion/cms_is2_7
ExecStart=/usr/local/bin/pipenv run gunicorn \
          --access-logfile - \
          --workers 3 \
          --bind unix:/run/gunicorn.sock \
          cms.wsgi:application

[Install]
WantedBy=multi-user.target


#/etc/systemd/system/gunicorn.socket

[Unit]
Description=gunicorn socket

[Socket]
ListenStream=/run/gunicorn.sock

[Install]
WantedBy=sockets.target

# /etc/nginx/sites-available/myproject

server {
    listen 80;
    server_name localhost;

    location = /favicon.ico { access_log off; log_not_found off; }
    location /static/ {
        alias /home/user/Desktop/produccion/cms_is2_7/staticfiles/;
    }

    location / {
        include proxy_params;
        proxy_pass http://unix:/run/gunicorn.sock;
    }
}


# La mayor parte saque de aca : 
https://www.digitalocean.com/community/tutorials/how-to-set-up-django-with-postgres-nginx-and-gunicorn-on-ubuntu

