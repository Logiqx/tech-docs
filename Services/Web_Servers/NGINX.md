# NGINX

## Background

Home Page - [Nginx](https://www.nginx.com/)

Nginx - [What is a Reverse Proxy Server?](https://www.nginx.com/resources/glossary/reverse-proxy-server/)



## Docker

Alpine - [Nginx](https://wiki.alpinelinux.org/wiki/Nginx) documentation

Image sizes - Nginx is 16MB on Alpine, compared to 124MB on Debian:

```
nginx                      alpine          32a037976344        2 days ago          16.1MB
nginx                      latest          73acd1f0cfad        12 months ago       109MB
```

[Tips for Deploying NGINX with Docker (Examples)](https://blog.docker.com/2015/04/tips-for-deploying-nginx-official-image-with-docker/) on Docker Blog



## PHP

I've made some notes in relation to [PHP](PHP.md).



## Multiple Domains

Here are a few articles about hosting multiple domains / websites using NGINX:

- [Installing Nginx on Ubuntu 18.04 with Multiple Domains](https://devanswers.co/installing-nginx-ubuntu-18-04-multiple-domains/) on DevAnswers.co

- [How to configure Multiple Domains with Nginx on Ubuntu](https://www.serverlab.ca/tutorials/linux/web-servers-linux/how-to-configure-multiple-domains-with-nginx-on-ubuntu/) on Serverlab

- [Host multiple websites on one VPS with Docker and Nginx](https://blog.ssdnodes.com/blog/host-multiple-websites-docker-nginx/) on Serverwise (SSD Nodes)



## HTTPS

The most difficult aspect of HTTPS is maintaining SSL certificates.

A nice article has been created which explains the use of [Let's Encrypt](https://letsencrypt.org/) and NGINX with Docker:

- [Nginx and Let’s Encrypt with Docker in Less Than 5 Minutes](https://medium.com/@pentacent/nginx-and-lets-encrypt-with-docker-in-less-than-5-minutes-b4b8a60d3a71)



