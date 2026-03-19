---
title: Management
description: Management stack
published: true
date: 2026-03-19T12:29:56.339Z
tags: 
editor: markdown
dateCreated: 2026-03-19T12:29:56.339Z
---

# Management stack
This stack is for the management of your homelab.
It has a pterodactyl panel container along with its dependencies, and has a HomePage container so you can get a quick overview of your homelab.
It's written in YAML and is used for docker compose.

```yaml
version: "3.8"
services:
  homepage:
    image: ghcr.io/gethomepage/homepage:latest
    container_name: homepage
    restart: always
    ports:
      - "3000:3000"
    volumes:
      - ./homepage/config:/app/config
      - /var/run/docker.sock:/var/run/docker.sock
    networks:
      - homelab-network
    environment:
      - HOMEPAGE_ALLOWED_HOSTS=homepage.yourdomain.com,192.168.x.x

  pterodactyl-panel:
    image: ghcr.io/pterodactyl/panel:latest
    container_name: pterodactyl-panel
    restart: always
    ports:
      - "8081:80"
    depends_on:
      - pterodactyl-db
      - pterodactyl-redis
    volumes:
      - ./pterodactyl/var:/app/var
      - ./pterodactyl/nginx:/etc/nginx/http.d
    environment:
      - APP_URL=http://your-pi-ip:8081
      - DB_PASSWORD=YOURPASSWORDHERE #must be the same as the one below (MYSQL_PASSWORD)
    networks:
      - homelab-network

  pterodactyl-db:
    image: mariadb:11.4
    container_name: pterodactyl-db
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=YOURROOTPASSWORDHERE #has to be secure
      - MYSQL_DATABASE=panel
      - MYSQL_USER=ptero
      - MYSQL_PASSWORD=YOURPASSWORDHERE #must be the same as DB_PASSWORD shown above
    volumes:
      - ./pterodactyl/db:/var/lib/mysql
    networks:
      - homelab-network

  pterodactyl-redis:
    image: redis:alpine
    container_name: pterodactyl-redis
    restart: always
    networks:
      - homelab-network

networks:
  homelab-network:
    external: true