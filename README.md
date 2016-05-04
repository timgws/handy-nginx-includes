# Tim's Quick and Easy nginx includes

**Please Note:** This has only been tested on RHEL6/RHEL7 with EPEL & provided nginx. YMMV.

**TL;DR:** Check out the [Website Config File](https://github.com/timgws/handy-nginx-includes/blob/master/vhost-template/website.conf)

# Quick Install Guide

```sh
# clone this reporitory to /etc/nginx/templates
git clone git@github.com:timgws/handy-nginx-includes.git /etc/nginx/templates
ln -s /etc/nginx/templates/site-config /etc/nginx/site-config
```

# Included Templates

* **[website.conf](https://github.com/timgws/handy-nginx-includes/blob/master/vhost-template/website.conf)**: A generic vhost domain. Has www and non-www support. Logs access to a seperate log file. Has easy to enable SSL support. Comment out the sections that you don't want or need.
* **[reverse-proxy.conf](https://github.com/timgws/handy-nginx-includes/blob/master/vhost-template/reverse-proxy.conf)**: A generic reverse proxy. Awesome for when you want to migrate servers. Has a block in there for serving files that exist in the root locally. Unfound files will be served by the reverse proxy.

# 'Modular Includes'

Inside the `site-includes` folder there is a bunch of files that have pre-rolled setting:

* `expires.conf`: set high expires values for css, javascript and common image formats
* `gzip.conf`: enable gzip compression for common formats
* `laravel.conf`: a simple laravel config file
* `log-me-not.conf`: don't log images in the access log
* `ssl.conf`: enable ssl, test with Qualys SSL Labs (https://www.ssllabs.com/ssltest/) which provides a comprehensive SSL testing suite. Config should give you a green A+.

# Using the SSL template

There is a template provided in `vhost-template/website.conf`. I recommend that this template is copied with the required vhost name into `/etc/nginx/conf.d`.

For example, when setting up `newdomain.com`, copy `vhost-template/website.conf` as `/etc/nginx/conf.d/newdomain.com.conf`.

Edit the newly created file and ensure that the settings are all correct

## quick note about dhparams

To avoid Logjam (https://weakdh.org/sysadmin.html) you want to ensure that before you use SSL for the first time on a server that you generate an unique `dhparam` file.

```sh
mkdir /etc/ssl/certs && cd /etc/ssl/certs
openssl dhparam -out dhparam.pem 4096
```

If you don't do this, the SSL templates will not work for you.

# Setting up SSL for a domain

## Creating the Certificate Signing Request (CSR)

Create an SSL certificate. Use the SSL template to ensure you can't skip required names (like the email address or hostname field).

```sh
cd /etc/nginx/ssl/
openssl req -config ../templates/openssl/ssl.conf -new -nodes -keyout domainname.com.key -out domainname.com.csr

# output the CSR and send to the certificate provider
cat domainname.com.csr

# or, on a mac, to automatically copy the contents into your clipboard
cat domainname.com.csr | pbcopy
```

## Order an SSL certificate

After ordering an SSL certificate with your favourite SSL provider (I normally order Geotrust $10 certificates from either enom or Namecheap), paste the above generated CSR when asked by your certificate wholesaler. Ensure that you can send an email to one of the listed email addresses.

## Save the certificate

Confirm your email address, then save the certificate once you recieve it.

```sh
# on a mac
pbpaste > /etc/nginx/ssl/domainname.com.crt

# on Linux
cat > /etc/nginx/ssl/domainname.com.crt

{paste certificate from email/web interface}
{CTRL+D}
```

