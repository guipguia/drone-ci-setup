# How to setup nginx virtual server for drone-ci

## Requirements
* Have access to sudo (root) user.
* I will use drone.example.com as our url.

## Getting Started

1. Create a new virtual server:
```sh
nano /etc/nginx/sites-available/drone.example.com
```
Copy and paste this virtual configuration:
```nginx
server {
    listen 80;
    server_name drone.example.com;

    location / {
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Host $http_host;

        proxy_pass http://127.0.0.1:8080; #if you change this port inside docker-compose file, make sure to set this to the same port.
        proxy_redirect off;
        proxy_http_version 1.1;
        proxy_buffering off;

        chunked_transfer_encoding off;
    }
}
```

2. Activate this virtual host:
```sh
ln -s /etc/nginx/sites-available/drone.example.com /etc/nginx/sites-enabled/drone.example.com
```

Restart nginx, this is debian/ubuntu cmd:
```sh
sudo systemctl restart nginx
```

3. Setup certbot to create our https ssl.

``Im assuming you have certbot installed, if you do not, please click`` [here](https://certbot.eff.org/docs/install.html)

```sh
sudo certbot --nginx -d drone.example.com
```

At this point your nginx server is ready to proxy our drone http server using let's encrypt ssl to the web 😆
