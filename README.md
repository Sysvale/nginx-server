# nginx-server

Initially make a copy of `.env.example` and enter the support email that certbot can use:
```
cp .env.example .env
```

Verify the correct configuration with the command:
```
docker-compose config
```

Create the network used by the container with the command:
```
docker network create nginx
```

To start the container in background just use:
```
docker-compose up -d
```

Example of a nginx configuration file:
```
server {
    listen 443 ssl;

    server_name DOMAIN;

    ssl_certificate /etc/letsencrypt/live/DOMAIN/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/DOMAIN/privkey.pem;

    location / {
        proxy_pass http://CONTAINER_NAME:8000/;
    }
}
```

Note: The same domain name used in `server_name` must also be used in the `ssl_certificate` and `ssl_certificate_key` file path.
