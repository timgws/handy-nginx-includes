# Quick and easy nginx includes

**Please Note:** This has only been tested on RHEL6/RHEL7 with EPEL & provided nginx. YMMV.

# Tim's Quick Install Guide

```sh
# clone this reporitory to /etc/nginx/templates
git clone git@github.com:timgws/handy-nginx-includes.git /etc/nginx/templates
ln -s /etc/nginx/templates/site-config /etc/nginx/site-config
```

# 'Modular Includes'

Inside the `site-includes` folder there is a bunch of files that have pre-rolled setting:

* `expires.conf`: set high expires values for css, javascript and common image formats
* `gzip.conf`: enable gzip compression for common formats
* `laravel.conf`: a simple laravel config file
* `log-me-not.conf`: don't log images in the access log
* `ssl.conf`: enable ssl

# Using the SSL template

There is a template provided in `vhost-template/website.conf`. I recommend that this template is copied with the required vhost name into `/etc/nginx/conf.d`.

For example, when setting up `newdomain.com`, copy `vhost-template/website.conf` as `/etc/nginx/conf.d/newdomain.com.conf`.

Edit the newly created file and ensure that the settings are all correct

# Setting up SSL for a domain
## Step One
Generate a dhparams file (avoid Logjam - https://weakdh.org/sysadmin.html)
```sh
mkdir /etc/ssl/certs && cd /etc/ssl/certs
openssl dhparam -out dhparam.pem 4096
```

## Step Two
Create an SSL certificate

```
cd /etc/nginx/ssl/
openssl req -config ../templates/openssl/ssl.conf -new -nodes -keyout domainname.com.key -out domainname.com.csr
```

Give the CSR to your given SSL provider (mine is Geotrust with Namecheap or ENOM). Once you have the certificate, save it.

```
cat > /etc/nginx/ssl/domainname.com.crt

{paste certificate from email/web interface}
{CTRL+D}
```

