---
author: ["John Nguyen"]
title: "Self-Hosting n8n on a Raspberry Pi"
description: "A tutorial on hosting an n8n server locally on a Raspberry Pi."
date: "2025-10-20"
type: post
translationKey: pagefind
draft: false
tags: ["coolify", "self-hosting", "docker", "raspberry pi", "n8n"]
categories: ["tutorials"]
cover: '/images/n8npi.png'
---

![alt text](/images/n8npi.png)

# What is n8n
[n8n](https://n8n.io/) is a free and open workflow automation tool that lets you orchestrate workflows, connect APIs and apps, and build intelligent AI agents.

# What is Coolify

![Image Description](/images/CoolifyLogo.png)
[Coolify](https://coolify.io/) is a free self-hosted drop in replacement to Heroku, Netlify, and other cloud services. It provides a frontend and allows you to deploy apps and services. As stated from their website, some benefits include:

- No Hidden Costs
- Cost Efficient (only pay for server)
- Data Privacy (controlled by you)
- Easy and nice UI
- Open Source
- Free SSL Certs
- Any Server
- Any Language
- Any Use Case
- Automation and Integration

# Installing Coolify
1. SSH or login to your Raspberry Pi terminal. 
2. Run the installation script (verify the latest on the [Coolify Website](https://coolify.io/self-hosted/))

        curl -fsSL https://cdn.coollabs.io/coolify/install.sh | sudo bash

3. The installation will output a Coolify admin URL. Use the RaspberryPi hostname/IP to open it on your browser (e.g. raspberrypi:8000)
4. Create an account to view your Dashboard


# Self-Hosting n8n with Coolify
In my previous blog, I self-hosted Discord bots using Coolify running on my Raspberry Pi. Since Coolify can host anything that can be containerized, there's no reason it wouldn't be able to host n8n. In fact, Coolify already has a n8n resource option whenever you create a new project. Self-hosting n8n lets you skip the cost of paying for a cloud service to start working with n8n. 

![alt text](/images/n8nresource.png)

## Building a n8n service
1. Create a new project on Coolify
2. Add a resource
3. Search for n8n and use that as the template
4. Use docker-compose.
5. Create a service (you can call it n8n)
6. Under the service configuration, Edit the Compose File

### n8n Compose File
1. Paste the following docker-compose file and update the timezone

```bash
services:
  n8n:
    image: docker.n8n.io/n8nio/n8n
    environment:
      - "GENERIC_TIMEZONE='America/Chicago'"
      - "TZ='America/Chicago'"
      - 'DB_SQLITE_POOL_SIZE=${DB_SQLITE_POOL_SIZE:-3}'
      - 'N8N_RUNNERS_ENABLED=${N8N_RUNNERS_ENABLED:-true}'
      - 'N8N_BLOCK_ENV_ACCESS_IN_NODE=${N8N_BLOCK_ENV_ACCESS_IN_NODE:-true}'
      - 'N8N_GIT_NODE_DISABLE_BARE_REPOS=${N8N_GIT_NODE_DISABLE_BARE_REPOS:-true}'
      - 'N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS=${N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS:-true}'
      - 'N8N_PROXY_HOPS=${N8N_PROXY_HOPS:-1}'
      - N8N_SECURE_COOKIE=false
      - N8N_TELEMETRY_DISABLED=true
      - 'NODES_EXCLUDE="[]"'
    volumes:
      - 'n8n-data:/home/node/.n8n'
    ports:
      - '0.0.0.0:5678:5678'
    healthcheck:
      test:
        - CMD-SHELL
        - 'wget -qO- http://127.0.0.1:5678/'
      interval: 5s
      timeout: 20s
      retries: 10
volumes:
  n8n-data: null
```

2. Start the service on Coolify. Navigate to the URL Pi http://<<IP/hostname>>:5678 to open the n8n login. 
3. Create an account and activate the free license key

## Raspberry Pi Caveats
Hosting n8n on a Pi is slightly different than on a VPS. Since it's running locally, the n8n instance isn't accessible outside your network. You can get around this by using a VPN or creating a tunnel. I opted to use [Tailscale](https://tailscale.com/), a VPN service to connect my Pi and external devices. This allows me to use my Macbook away from home and still open n8n dashboard to edit my workflows. Additionally, Tailscale lets you connect to devices through SSH and already designates the hostname to the new internal IP on the VPN. Don't forget your Pi must be online 24/7 to keep the server online, of course. Since it is all running locally, you don't need to worry about the instance being exposed to the internet. However, this means any n8n generated public link forms may need to be modified to work.

### OAuth Caveat
When connecting n8n to other services like Google Cloud, you will need to create credentials that may use OAuth. By default, n8n will create a URL to launch the OAuth popup window to the ip that hosts it (which will be localhost on the Pi). If you are not using the Pi's browser, then you'll have to copy the generated link and change localhost to the Raspberry Pi IP address or hostname. With Tailscale active, using the hostname is all we'll need.

# Conclusion
With Tailscale or some network tunnel, you'll be able to access your n8n instance from anywhere as if it were hosted on a VPS. Now you'll be able to build automation workflows with n8n and easily update the service using Coolify!