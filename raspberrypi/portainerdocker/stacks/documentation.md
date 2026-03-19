---
title: Documentation
description: 
published: true
date: 2026-03-19T12:31:42.288Z
tags: 
editor: markdown
dateCreated: 2026-03-18T11:37:34.600Z
---

# Documentation stack
This stack is for your documentation of your homelab.
It uses WikiJS which is what this site is based on. It also uses PostGreSQL which is a database needed for WikiJS to function.
It's written in YAML and is used for docker compose.

```yaml
services:

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: dbhere #this
      POSTGRES_PASSWORD: passwordhere #this
      POSTGRES_USER: user #and this must match with the ones below
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
      DB_USER: userhere
      DB_PASS: passwordhere
      DB_NAME: dbhere
    restart: unless-stopped
    ports:
      - "85:3000"

volumes:
  db-data: