# Setup

[link](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-20-04)

1. Install `certbot`

```
apt install certbot python3-certbot-nginx
```

2. Update `/etc/nginx/default.conf`

For each domain, we need two `server` blocks:

```
server {
    listen 80;
    server_name {DOMAIN};

    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }

    location / {
        return 301 https://{DOMAIN}$request_uri;
    }
}


server {
    listen 443 ssl;
    server_name {DOMAIN};

    location / {
        proxy_pass http://localhost:{PORT}/;
    }
}
```

3. Obtain a SSL certificate by running `certbot`:

```
certbot --nginx -d <DOMAIN>
```

4. `default.conf` will be updated:

```
server {
    listen 80;
    server_name {DOMAIN};

    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }

    location / {
        return 301 https://{DOMAIN}$request_uri;
    }
}


server {
    listen 443 ssl;
    server_name {DOMAIN};

    ssl_certificate /etc/letsencrypt/live/{DOMAIN}/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/{DOMAIN}/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

    location / {
        proxy_pass http://localhost:{PORT}/;
    }
}
```
