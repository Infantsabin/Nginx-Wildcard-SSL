<div align="center" id="top">
  <img src="https://www.nginx.com/wp-content/uploads/2018/08/NGINX-logo-rgb-large.png" alt="Nginx Wildcard SSL" />

  &#xa0;

  <!-- <a href="https://yashodadevelopment.netlify.app">Demo</a> -->
</div>

<h1 align="center">Nginx Wildcard SSL</h1>

<p align="center">
  <img alt="Github top language" src="https://img.shields.io/github/languages/top/infantsabin/nginx-wildcard?color=56BEB8">

  <img alt="Github language count" src="https://img.shields.io/github/languages/count/infantsabin/nginx-wildcard?color=56BEB8">

  <img alt="Repository size" src="https://img.shields.io/github/repo-size/infantsabin/nginx-wildcard?color=56BEB8">

  <img alt="License" src="https://img.shields.io/github/license/infantsabin/nginx-wildcard?color=56BEB8">

  <!-- <img alt="Github issues" src="https://img.shields.io/github/issues/{{YOUR_GITHUB_USERNAME}}/nginx-wildcard?color=56BEB8" /> -->

  <!-- <img alt="Github forks" src="https://img.shields.io/github/forks/{{YOUR_GITHUB_USERNAME}}/nginx-wildcard?color=56BEB8" /> -->

  <!-- <img alt="Github stars" src="https://img.shields.io/github/stars/{{YOUR_GITHUB_USERNAME}}/nginx-wildcard?color=56BEB8" /> -->
</p>

<!-- Status -->

<!-- <h4 align="center">
	🚧  Nginx Wildcart SSL 🚀 Under construction...  🚧
</h4>

<hr> -->

<p align="center">
  <a href="#dart-about">About</a> &#xa0; | &#xa0;
  <a href="#sparkles-pre-requirements">Pre-requirements</a> &#xa0; | &#xa0;
  <a href="#sparkles-starting">Starting</a> &#xa0; | &#xa0;
  <a href="#sparkles-set-up-nginx">Set Up NGINX</a> &#xa0; | &#xa0;
  <a href="#sparkles-set-up-certbot-(optional)">Set Up Certbot (Optional)</a> &#xa0; | &#xa0;
  <a href="#sparkles-config-certbot-dns-token">Config Certbot DNS token</a> &#xa0; | &#xa0;
  <a href="#sparkles-certificate-generation">Certificate Generation</a> &#xa0; | &#xa0;
  <a href="#sparkles-sample-nginx-configuration">Sample Nginx configuration</a> &#xa0; | &#xa0;
  <a href="#sparkles-error">Error</a> &#xa0; | &#xa0;
  <a href="https://github.com/infantsabin" target="_blank">Author</a>
</p>

<br>

## :dart: About ##

It's all about to setup free wildcard SSL in your hosting server. It has auto-renewal feature as well. Here, I tell you the steps to setup this in easy way!

## :sparkles: Pre-requirements ##
Before starting :checkered_flag: \
:heavy_check_mark: Ubuntu/Linux Hosting Server;\
:heavy_check_mark: Nginx;\
:heavy_check_mark: Python;

## :sparkles: Starting ##

Download the Let’s Encrypt Client

```bash
# Update your Packages
$ apt-get update

# Install certbot
$ sudo apt-get install certbot

# Install certbot nginx
$ apt-get install python3-certbot-nginx
```

## :sparkles: Set Up NGINX ##

certbot can automatically configure NGINX for SSL/TLS. It looks for and modifies the server block in your NGINX configuration that contains a server_name directive with the domain name you’re requesting a certificate for.
Assuming you’re starting with a fresh NGINX install, use a text editor to create a file in the /etc/nginx/sites-enabled directory named domain.in.conf
Specify your domain name (and variants, if any) with the server_name directive:

```bash
# create new file and add this at /etc/nginx/sites-enabled

server {
    listen 80 default_server;
    listen [::]:80 default_server;
    root /var/www/html;
    server_name mverve.in www.mverve.in;
}
```

Save the file, then run this command to verify the syntax of your configuration and restart NGINX:

```bash
# check nginx config
$ nginx -t

# reload nginx
$ nginx -s reload
```

## :sparkles: Set Up Certbot (Optional) ##

You have to install python3-pip in your system. If you use lower than 16.04 and below use python-pip	 instead of python3-pip

```bash
# Install Pip
$ apt-get install python3-pip

```

Use this below command to display your certbot installed pip dependencies

```bash
$ pip list | grep certbot
# After you put this command, you should able to see these below dependencies
certbot                0.40.0
certbot-nginx          0.40.0
certbot-dns-digitalocean (1.11.0)
```

## :sparkles: Config Certbot DNS token ##

Create a file in root directory by given name,

```bash
# Create a txt file
$ nano /root/0IMPORTANT_digital_ocean_creds.txt

```

There inside the file you have to paste your digital ocean dns token like this, and save.


```bash
# add your token inside
dns_digitalocean_token = 7f7c54***************************
```

## :sparkles: Certificate Generation ##

Follow these commands to generate your WILD CARD certificates.

This is for your main domain.

```bash
# Get wildcard cert for main domain

$ certbot certonly --server https://acme-v02.api.letsencrypt.org/directory --dns-digitalocean --dns-digitalocean-credentials /root/0IMPORTANT_digital_ocean_creds.txt --dns-digitalocean-propagation-seconds 60 -d \domain.in

```

This is for you subdomains

```bash
# Get wildcard cert for sub domains

$ certbot certonly --server https://acme-v02.api.letsencrypt.org/directory --dns-digitalocean --dns-digitalocean-credentials /root/0IMPORTANT_digital_ocean_creds.txt --dns-digitalocean-propagation-seconds 60 -d \*.domain.in

```

After this generation you would get certs and keys in /etc/letsencrypt/live/  in this directory.

Based on your usage use these certificates in your nginx configuration.

## :sparkles: Sample Nginx configuration ##

In your,  /etc/nginx/sites-enabled/domain.in  follow this snippet

```bash
# sample nginx setup file

server {
  listen 80;
  listen [::]:80;
  server_name domain.in www.domain.in;
  return 301 https://$host$request_uri;
}
server {
  listen 443 ssl;
  server_name domain.in www.domain.in;
  ssl_certificate /etc/letsencrypt/live/domain.in-0001/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/domain.in-0001/privkey.pem;
  include /etc/letsencrypt/options-ssl-nginx.conf;
  #ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
  root /var/www/html;
  index index.html;
  location / {
    try_files $uri $uri/ =404;
  }
}

```

## :sparkles: Error ##

If you are facing this issue while generating your certificates.

An unexpected error occurred:
pkg_resources.VersionConflict: (acme 1.1.0 (/usr/lib/python3/dist-packages), Requirement.parse('acme>=1.8.0'))

```bash
# Update your pip acme
$ pip3 install --upgrade acme

```

Made with :heart: by <a href="https://github.com/infantsabin" target="_blank">Infant Sabin</a>

&#xa0;

<a href="#top">Back to top</a>
