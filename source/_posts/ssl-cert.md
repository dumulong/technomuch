---
title: Free SSL certificate
tags:
  - Amazon Web Services (AWS)
categories:
  - Web-related
date: 2020-04-12 14:01:10
---


SSL certificates can provide encryption over TCP between the server and the browser.  Until recently, I did not have much of a need for it since my work was done on the Intranet.  I did have many websites and tools that I was self-hosting on AWS, but I really never had to add any sort of protection.

I always knew that it would be a "must-have" if I was ever wanted to open my applications to other users or even put more personal content on the web.

Anyhow, now that I had some time to play around with it, I am shocked to see how easy it was to get started with SSL... and it was FREE!

To tell you the truth, I'm mostly writing this blog as a reminder on how to do it since it was all done in less than 30 minutes!

## Certificate Authority (CA)

Only a CA can issue a certificate that you will need to enable HTTPS.  For my websites, I went with [Let's Encrypt](https://letsencrypt.org/getting-started/).  We will not need to read more on this website as they have a client software called __Certbot__ who will do all the work for us!

If you go to the [Certbot website](https://certbot.eff.org/), you'll be able to enter the software (webserver) and the system (OS) that you are using.  In my case, I'm using __Apache__ on __Ubuntu 18.04__.  Once you select your software/system, you'll be provided instructions on how to do it.

I'll repeat some of the instructions here, just so I can remember the steps that I took, but I encourage you to go directly on their website.  They did an excellent job at detailing every step and it went without an itch.

I did have some issues afterward but it was related to the port 443 used by SSL.

I'll explain the steps that I took to solve this issue later on.

## Certbot ([visit website](https://certbot.eff.org/))

These are the steps that I took... for a detailed explanation, go to their website!

```
sudo apt-get update
sudo apt-get install software-properties-common
sudo add-apt-repository universe
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install certbot python-certbot-apache
sudo certbot --apache
```
Voila!  It should now be working for you... well.. almost.

## Opening the port 443 on AWS

I needed to open the port 443 in the AWS Lightsail console for my instance. To do so, click on the _vertical-three-dots_ icon of your instance and click __Manage__. From there, go to the __Networking__ tab.  We will change the firewall to make sure that we can communicate with port 443 of our instance.

* Click "+ Add another"
* Select HTTPS from the dropdown.
* Click Save

I also had the firewall enabled within my instance, so I had to open that post also:

To see if the firewall (utw) is active:
```
sudo ufw status
```

To open the https port:
```
sudo ufw allow https
```

## Limited time only!

Sadly, this certificate will expire every 3 months, but Certbot made it quite easy to renew.  Here's an example for renewing that you can run right now (it's a dry-run only, without effectively renewing the certificate).

```
sudo certbot renew --dry-run
```

If you entered your email correctly while setting it up, you should receive a notification when it's about time to renew.  You will then need to re-enter this command without the ```--dry-run``` flag.
