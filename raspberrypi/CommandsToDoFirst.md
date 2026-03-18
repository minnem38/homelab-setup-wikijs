---
title: Commands to do first
description: Commands you should do immediately after setting up your pi
published: true
date: 2026-03-18T20:26:47.284Z
tags: 
editor: markdown
dateCreated: 2026-03-18T10:52:46.106Z
---

# Commands you should do first
First things first, you need a root account, so type:
`sudo passwd root`
After making a password, you can continue.
`
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh`

Let's proceed to Portainer
Create the volume:
`docker volume create portainer_data`
And run the container:
`docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:lts` (yes that whole thing)

And check to see if it's running
`docker ps`
Now let's create some directories
`sudo mkdir /services`
`sudo mkdir /services/samba`
`sudo mkdir /services/samba/shares`
`sudo mkdir /services/samba/shares/RPiShare`

Now you can put in the stacks in Portainer!
