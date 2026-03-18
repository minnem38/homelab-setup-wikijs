---
title: Untitled Page
description: 
published: true
date: 2026-03-18T11:37:34.600Z
tags: 
editor: markdown
dateCreated: 2026-03-18T11:37:34.600Z
---

# Documentation stack
This stack is for your documentation of your homelab.
It's written in YAML and is used for docker compose.

```services:

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: wiki
      POSTGRES_PASSWORD: wikijsrocks
      POSTGRES_USER: wikijs
    logging:
      driver: none
    restart: unless-stopped
    volumes:
      - db-data:/var/lib/postgresql/data

  wiki:
    image: ghcr.io/requarks/wiki:2
    depends_on:
      - db
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: wikijs
      DB_PASS: wikijsrocks
      DB_NAME: wiki
    restart: unless-stopped
    ports:
      - "85:3000"

volumes:
  db-data: