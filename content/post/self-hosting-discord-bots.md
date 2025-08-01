---
author : ['John Nguyen']
title: "Self Hosting Discord Bots and Other Apps with Coolify"
description: "How to self host your apps using Coolify on VPS, Raspberry Pi and more."
date: 2025-01-21
type: post
draft: false
tags: ["coolify", "hosting", "docker", "git", "raspberry pi"]
categories: ["self-hosting", "automation", "deployment"]
cover: '/images/CoolifyLogo.png'
---

![Image Description](/images/CoolifyLogo.png)

## Why Self-Host?

A lot of people tend to stray away from self-hosting and look for convenient deployment solutions with automated processes, Git integration, a simple user interface, etc. However, they can sometimes get hit with a surprise serverless bill from their cloud provider running over 4 digits. Self-Hosting allows you to have full control over your apps and services. It teaches you how to run things privately, setting up things like nginx, systemd services, networking and more. It is also cost efficient since you only have to pay for the server cost. While, I enjoy not worrying about huge bills from cloud providers, I still miss having the convenience these hosting platforms provided. With some investigation, I came across Coolify.


## Coolify Benefits

As stated on their website, there's plenty of benefits such as:

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

Check [Coolify.io](https://coolify.io/) for the latest information.

## Installing on a Raspberry Pi

One of the most money-saving things I've invested in was a Raspberry Pi. It's a tiny computer that can be used for numerous small projects. Here I've decided to treat it as an Ubuntu Server. The only cost I will have to worry about is my electricity bill for keeping it plugged in. Coolify fortunately supports installing on the Raspberry Pi with slightly different steps. In the future, I may consider moving to a VPS so that I can reuse my Raspberry Pi for more hardware intensive projects but for now this saves me money. With Coolify backup configurations, transferring in the future should be no issue. I SSHed into my Pi, ran through the single install script, created an account, and accessed the web portal through my Pi's IP address and port on my main Windows machine. 

![Image Description](/images/coolifydash.png)

## Deploying Discord Bots

 For my use case, I wanted to keep my Discord bots running 24/7. I formerly had a script that ran as a service with systemd. This script would perform a git pull on the local repository and then build a docker image and run the container. This was straightforward but it still involved a lot of messing around with systemd service files and creating startup scripts. In the past, I've used Heroku until they removed their Free-Tier. With Coolify, I would get the benefits of Heroku without worrying about any additional pay wall. Coolify's Dashboard is clean and simple. Since it already had Git and Docker integration, I was able to create new project on Coolify, use my Git repo as a resource, set my environment variables, build my Docker image, and then immediately have it running! If I made a new change to the repo, Coolify would automatically build and re-deploy my app. Additionally, I could see the logs from the app, deployment dates, and access the terminal to the app if needed. Deployment is quick, automated, and easy to manage! With this setup, I have two Discord bots running. No additional networking was required since the bots didn't need to forward any ports or map to any domain. 
 
![Image Description](/images/crewaideploy.png)


## Other Use Cases

The key point of Coolify is that it can deploy ANY service that can be containerized. I was looking into other things that I can self-host locally. Coolify has a list of one click services that can be deployed already: [Coolify Services](https://coolify.io/docs/services/). In the future, once my current web-hosting plans expire, I plan to migrate my websites to Coolify. Additionally, I am investigating useful services to deploy for myself such as finance managers, home server UIs, Cloud storages, password managers, and more. 

## Conclusion

Coolify brought the best of both worlds in self-hosting and convenience from cloud providers. I can continue to manage my own data privacy in a convenient way without worrying about over paying. I am looking forward to migrating more apps to Coolify in the future and I hope Coolify gets continuous updates. Overall, I am very satisfied with the solution that Coolify offers. 
