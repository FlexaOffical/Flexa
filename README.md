![alt text](https://raw.githubusercontent.com/FlexaCP/core/main/flexa-v2.png)

# [Flexa V2.0](http://flexacp.uk.to)

Flexa is a high-performance client area for the Pterodactyl Panel. It allows your users to create, edit and delete servers, and also earn coins which can be used to upgrade their servers.

All features:
- Coins (AFK Page Earning)
- Servers (create, view, edit servers)
- Login Queue (prevent overload)
- User System (auth, regen password)
- Store (buy resources with coins)
- Dashboard (view resources)
- API (for bots & other things)


## Install Guide

Ubuntu 22.04 - 20.04 ✅

  - Install Nodejs 16, it's recommended to install it with nvm `sudo apt install curl && curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash`
  - reopen a new ssh session (eg restart putty)
  - `nvm install 16`
  - check the node version with `node -v`
  - Clone the repo: `git clone https://github.com/FlexaCP/Flexa.git /var/www/flexa`
  - Run `cd /var/www/flexa && npm i`
  - To run Flexa, use `node .`
  - To Running in background, use `npm install pm2 -g` after that `cd /var/www/flexa && pm2 start node .`

Windows ✅

  - Download Nodejs: [Download x64](https://nodejs.org/dist/v18.20.2/node-v18.20.2-x64.msi)
  - Download Flexa: [Download](https://cuty.io/dgNtP)
  - `npm install`
  - To run Flexa, use `node .`

## NGINX reverse proxy

 - Run `nano /etc/nginx/sites-enabled/default`
 - Remove Everything and
 - Paste the configuration at the bottom of this and replace with the IP of the pterodactyl server including the port and with the domain you want your dashboard to be hosted on

```nginx
server {
    listen 80;
    server_name <domain>;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;

    location /ws {
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";
      proxy_pass "http://localhost:<port>/ws";
    }

    server_name <domain>;

    ssl_certificate /etc/letsencrypt/live/<domain>/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/<domain>/privkey.pem;
    ssl_session_cache shared:SSL:10m;
    ssl_protocols SSLv3 TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers  HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    location / {
      proxy_pass http://localhost:<port>/;
      proxy_buffering off;
      proxy_set_header X-Real-IP $remote_addr;
    }
}
```
### Create SSL certificates

 - Run `sudo apt update`
 - Run `apt install nginx && apt install certbot -y`
 - Run `ufw allow 80 && ufw allow 443`
 - Run `certbot certonly -d <Your Flexa Domain>`
 - Select `1`


 - Run `systemctl restart nginx` and try open your domain.
