---
layout: posts
title: AWS Virtual Private Server (VPS)
date: 2020-03-30 07:37:04
tags: [Amazon Web Services (AWS)]
categories: [Amazon Web Services (AWS)]
---

In this post, I will show you how to create, configure and secure a Virtual Private Server (VPS) on Amazon Web Services (AWS) Lightsail.

## Creating a VSP instance

First up, go to the [Amazon website for Lightsail](https://lightsail.aws.amazon.com) and sign up for an account if you don't already have one.

On your Home page, click on "create instance".  For this post, I've selected __OS only__ and __Ubuntu 18__.

Once you've created a VSP instance, click on the 3 dots icon and select __Manage__.  In the __Networking__ tab, click on "create static IP". This way you'll be able to stop/start the instance without losing the IP that was assigned to it.

## Downloading the SSH key

The next step is to download our SSH key so we can use SSH to remote in our server.  So, go to your __account__'s page (toolbar on top) and, on the __SSH Keys__ tab, download the key.

Put the key in you home directory under the .ssh subdirectory (by convention)

Example:

``` batch
c:/users/denis/.ssh/myKey.pem
```

To connect with an ssh client (I use GIT bash), enter the following line:

``` batch
ssh [user]@[IP address] -p [port] -i [full path to ssh key]
```

Example:

``` batch
ssh ubuntu@3.89.214.99 -p 22 -i c:/users/denis/.ssh/myKey.pem
```

## Getting the server up-to-date

Prepare the list of updates from the package lists:

``` shell
sudo apt-get update
```

Then actually preform the update:

``` shell
sudo apt-get upgrade
```

NOTE: For the sshd config change question, I normally select __install the package maintainer's version__

Package no longer required?

``` shell
sudo apt-get autoremove
```

## Install finger (optional)

``` shell
sudo apt-get install finger
```

## Adding a new user

In this example, we will create a user named __technomuch__

``` shell
sudo adduser technomuch
```

Set the new password and general information for the new user.

Now, let's add sudo capabilities to our new user by copying the default file

``` shell
sudo cp /etc/sudoers.d/90-cloud-init-users /etc/sudoers.d/technomuch
```

After that,  edit and change "ubuntu" to "technomuch" (I use the nano text editor)

``` shell
sudo nano /etc/sudoers.d/technomuch
```

## Create an SSH key to use for our new user

On the client side (local), create a new SSH key from ssh-keygen.

NOTE: on Windows, I used the GIT Bash to create the SSH key pair.

``` batch
ssh-keygen
```

Then enter the name of a name for the key that you will create.  After that, you'll be asked to set a passphrase to protect the key.

Two files will be created: a file without an extension and a ".pub" file.  The ".pub" file is the public key that we will eventually copy to our sever and the other is our private key that you should never share with anyone.

## SSH set up

We will now create the SSH authorized keys file, set its permissions and finally, change its ownership.

``` shell
sudo mkdir /home/technomuch/.ssh
sudo touch /home/technomuch/.ssh/authorized_keys
sudo chmod 700 /home/technomuch/.ssh
sudo chmod 644 /home/technomuch/.ssh/authorized_keys
sudo chown technomuch:technomuch /home/technomuch/.ssh
sudo chown technomuch:technomuch /home/technomuch/.ssh/authorized_keys
```

Now that we have the file, you'll need to __copy/paste the content of the xxxx.pub__ file that we've just created using the ssh-keygen software.

``` shell
sudo nano /home/technomuch/.ssh/authorized_keys
```

_Use the console to reboot_


## PS: Public key file format

The values in the __xxx.pub__ file should be on one line and look like this:

``` shell
ssh-rsa AAAAB3<...very long  string...>Tx5I55KMQ== rsa-key-20200820
```

But, if you have generated the key using another tool like __PuTTYgen__, you may get a key looking like this:

``` shell
---- BEGIN SSH2 PUBLIC KEY ----
Comment: "rsa-key-20200820"
AAAAB3NzaC1yc2EAAAABJQAAAQEAki9hkBcpDBoS+7B/GdaLMP+Clu4ywfZgZi80
... more lines ...
+Qy3XKjwPD9AtNOD+vIayR5/T4OSF1ooEzcMarcS8xu3gTEoykH55f8IFZU0TyHU
EEQsiSsbNeV7uW44YAUmX+AWM+IODGF2YirISHGe8Tx5I55KMQ==
---- END SSH2 PUBLIC KEY ----
```

If so you can always reformat it like this ```ssh-rsa <SSH Public key> <comment>``` all in one line.

Or, better yet, reopen the private key with __PuTTYgen__ by clicking "Load" and selecting the file.  You should then see the public key in the text field titled "Public key for pasting into OpenSSH authorized_keys file" (quite descriptive...).


## Alternative

You could have created the authorized_keys file and the .ssh folder with the technomuch's account directly.

In order to do so, you would have needed to enable password authentication while you are doing the work.

To enable password authentication, you would have needed to edit the file:

``` shell
sudo nano /etc/ssh/sshd_config
```

And change the line "PasswordAuthentication no" to "PasswordAuthentication yes"

Just don't forget to turn it back to _no_ when you're done.

## Login using SSH key

Now, you should be able to use the private key to login:

``` batch
ssh technomuch@3.89.214.99 -p 22 -i c:/users/denis/.ssh/generatedKey
```

## Change the SSH port from 22 to 2200

<span style="color:red">__ATTENTION__</span>: _After this step, you'll no longer be able to use the online SSH option offered by amazon.  Trying to login via the "Connect using SSH" button will just hang as it will still try to use the port 22 for SSH which our sever no longer uses._

Make sure you update the LightSail firewall by creating a custom rule for TCP port 2200 (Networking tab of the instance)

While you are at it, add the custom UDP 123 (NTP)

We will now configure SSH to use the port 2200 by editing the SSH config file:

``` shell
sudo nano /etc/ssh/sshd_config
```

And, uncomment __Port 22__ and change to __Port 2200__

_Use the console to reboot_

Now, you can ssh in on port 2200

``` batch
ssh technomuch@3.89.214.99 -p 2200 -i c:/users/denis/.ssh/generatedKey
```


## Make sure that secure the server by enabling a firewall

Deny all incoming traffic, then open some ports

``` shell
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow ntp
sudo ufw allow 2200/tcp
sudo ufw allow www
sudo ufw enable
```

Firewall status (already installed by default but inactive...)

``` shell
sudo ufw status
```

## Summary

So, in retrospect, we've created a server instance, and updated any packages to the latest version.

we've created a new user and gave it sudo capabilities.

We've created a new SSH key and set our new user with the public key.

After changing the SSH port from 22 to 2200, we've enable a firewall.

That's it, you should now be running a secure AWS VPS!  Congratulation!

