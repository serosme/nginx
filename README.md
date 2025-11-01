# Nginx

- [Nginx](#nginx)
  - [Command](#command)
    - [logrotate](#logrotate)
  - [Official build of Nginx](#official-build-of-nginx)
    - [How to use this image](#how-to-use-this-image)
    - [Customize configuration](#customize-configuration)
      - [Export default configuration](#export-default-configuration)
      - [Mount your configuration](#mount-your-configuration)
  - [Nginx Proxy Manager](#nginx-proxy-manager)
    - [Running the App](#running-the-app)
      - [Run with Docker](#run-with-docker)
      - [Run with Docker Compose](#run-with-docker-compose)
    - [Default Administrator User](#default-administrator-user)
    - [Best Practice: Use a Docker network](#best-practice-use-a-docker-network)

## Command

### logrotate

```bash
logrotate /etc/logrotate.d/nginx
```

## Official build of Nginx

[Docker Hub](https://hub.docker.com/_/nginx)

### How to use this image

```shell
docker run -d -p 8080:80 --name nginx nginx
```

### Customize configuration

#### Export default configuration

```shell
docker run --rm -v /data/nginx/nginx_config:/tmp/copy nginx:latest sh -c "cp -r /etc/nginx/. /tmp/copy/"
```

#### Mount your configuration

```shell
docker run -d -p 80:80 -v /data/nginx/nginx_config:/etc/nginx -v /data/nginx/nginx_data:/data --name nginx nginx:latest
```

## Nginx Proxy Manager

[Nginx Proxy Manager](https://nginxproxymanager.com/)

[Docker Hub](https://hub.docker.com/r/jc21/nginx-proxy-manager)

### Running the App

#### Run with Docker

```shell
docker run -d -p 80:80 -p 443:443 -p 81:81 -v ./nginx_data:/data -v ./nginx_letsencrypt:/etc/letsencrypt --name nginx-proxy-manager jc21/nginx-proxy-manager:latest
```

#### Run with Docker Compose

```yaml
services:
  nginx-proxy-manager:
    image: jc21/nginx-proxy-manager:latest
    restart: unless-stopped
    ports:
      - 80:80
      - 443:443
      - 80:81
    volumes:
      - ./nginx_data:/data
      - ./nginx_letsencrypt:/etc/letsencrypt
```

### Default Administrator User

```txt
Email:    admin@example.com
Password: changeme
```

### Best Practice: Use a Docker network

```shell
docker network create nginx-network
```

Look at a Portainer example

```yaml
services:
  nginx-proxy-manager:
    image: jc21/nginx-proxy-manager:latest
    restart: unless-stopped
    ports:
      - 80:80
      - 443:443
      - 80:81
    volumes:
      - ./nginx_data:/data
      - ./nginx_letsencrypt:/etc/letsencrypt

  portainer:
    image: portainer/portainer
    restart: unless-stopped
    privileged: true
    volumes:
      - ./portainer_data:/data
      - /var/run/docker.sock:/var/run/docker.sock

networks:
  default:
    external: true
    name: nginx-network
```
