---
title: "Development notes on safely deploying a streamlit app on VM"
layout: default
---


install docker: https://docs.docker.com/engine/install/ubuntu/
note not compatible with ufw


man skal bruge ngnix til at redirecte fra port 8501 e.g. (over 1024) til port 80, fordi kræver ellers admin. Muligt at køre med admin #sudo -E /home/RUN/miniconda3/envs/mask/bin/python -m streamlit run app.py --server.port 80 --server.address 0.0.0.0
men ikke recommended - også docker problemer. 

###
opret app registration.

Vælg platform: web og sæt DNS URL/auth/callback ###faktisk ikke /auth/callback åbenbart
som redirect url

##
installer ngnix og cerbot:

sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d {{domain}}



app/.streamlit/sectrets.toml
sæt:
[browser]
enableCORS = false
gem også azure secrets her. bemærk at det skal være inden for app folderen


sudo nano /etc/nginx/sites-available/default
indsæt:
server {
    listen 80;
    server_name [DNS DOMAIN];
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name [DNS DOMAIN];

    ssl_certificate /etc/letsencrypt/live/[DNS DOMAIN]/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/[DNS DOMAIN]/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

    location / {
        proxy_pass http://localhost:8501;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # WebSocket settings for Streamlit
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_buffering off;
        proxy_read_timeout 86400;
    }
}

test at det virker:
sudo nginx -t
sudo docker run -p 8501:8501 [image:version]
^bemærk at du ikke længere kan mappe -p 80:850
