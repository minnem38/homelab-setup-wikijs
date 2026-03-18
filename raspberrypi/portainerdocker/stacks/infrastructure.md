---
title: Infrastructure
description: The infrastructure stack
published: true
date: 2026-03-18T20:38:07.719Z
tags: 
editor: markdown
dateCreated: 2026-03-18T20:38:07.719Z
---

# Infrastructure stack
This stack is for your networking and stuff of your homelab.
If you don't have TP-Link Omada devices, you can leave out that part.
It has NPM for a reverse proxy, Pi-Hole for DNS, Omada for controlling your network, and a cloudflare tunnel for remote access.
It's written in YAML and is used for docker compose.

```---
version: "3.8"
services:
  nginx-proxy-manager:
    image: jc21/nginx-proxy-manager:latest
    container_name: nginx-proxy-manager
    restart: always
    ports:
      - 80:80
      - 81:81
      - 443:443
    volumes:
      - ./npm/data:/data
      - ./npm/letsencrypt:/etc/letsencrypt
    networks:
      - homelab-network
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    restart: always
    ports:
      - 53:53/tcp
      - 53:53/udp
      - 8080:80/tcp
    environment:
      TZ: Europe/Amsterdam
      WEBPASSWORD: YOURPIHOLEPASSWORDHERE
    volumes:
      - ./pihole/etc-pihole:/etc/pihole
      - ./pihole/etc-dnsmasq.d:/etc/dnsmasq.d
    cap_add:
      - NET_ADMIN
    networks:
      - homelab-network
  omada-controller:
    container_name: omada-controller
    image: mbentley/omada-controller:latest
    restart: always
    environment:
      - TZ=Europe/Amsterdam
      - PUID=1000
      - PGID=1000
    ports:
      - 8043:8043
      - 29810:29810/tcp
      - 29811:29811/tcp
      - 29812:29812/tcp
      - 29813:29813/tcp
      - 29814:29814/tcp
    volumes:
      - ./omada/data:/opt/tplink/EAPController/data
      - ./omada/work:/opt/tplink/EAPController/work
      - ./omada/logs:/opt/tplink/EAPController/logs
    networks:
      - homelab-network
  tunnel:
    container_name: cloudflared-tunnel
    image: cloudflare/cloudflared
    restart: unless-stopped
    command: tunnel run
    environment:
      - TUNNEL_TOKEN=CloudflareTokenHere
networks:
  homelab-network:
    external: true