---
layout: _drafts
title: Multiple webapps on AWS Node.js
tags:
  - Node.js
  - Amazon Web Services (AWS)
categories:
  - Node.js
date: 2020-03-30 10:58:33
---


In this post, I will show you how to host multiple Node.js web applications on the same __AWS Ligthsail Node.JS__ instance.

Basically, we will be using the Apache web server to forward to the right application base on the requests' path.

## Installing our applications on the server

Under ```/home/bitnami``` folder, create an ```apps``` subfolder (just a convention).  This is where we will put your node.js apps.

Once your apps are copied there, you could start the with ```forever``` so it won't shut down when you log off and they will restart on a server reboot.

Example:

``` shell
forever start src/app1.js
```

Now your apps should be running on whatever port you have them configured under.


## Configuring Apache as a proxy

We need to tell Apache which app to use and what port they are running under.

In order to do so, we will edit the apps prefix file:

``` shell
nano /opt/bitnami/apache2/conf/bitnami/bitnami-apps-prefix.conf
```

And add the following lines (example)

``` shell
# Bitnami applications installed in a prefix URL

ProxyPass /app1 http://127.0.0.1:3000/app1
ProxyPassReverse /app1 http://127.0.0.1:3000/app1

ProxyPass /app2 http://127.0.0.1:5000
ProxyPassReverse /app1 http://127.0.0.1:5000

# ...
```

In this example, the configuration will prompt Apache to serve any requests to app1 straight to our app running on port 3000 while app2 requests will serve the app running under port 5000.

Please note that our app1 will receive a request with a root of app1 (http://127.0.0.1:3000/app1) while the app2 will not receive any prefix (http://127.0.0.1:5000).

Restart apache:

``` shell
sudo /opt/bitnami/ctlscript.sh restart apache
```

Now both apps should be available from the internet:

h&#8203;ttp://xx.xx.xx.xx/app1/test/test?name=George
h&#8203;ttp://xx.xx.xx.xx/app2/about

## Useful command

To stop a ```forever``` process by name (app1.js in our example):

``` shell
uid=$(forever list | grep app1.js | cut -c24-27) && forever stop $uid
```

