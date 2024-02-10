If you've never used ssh before, it will quickly become part of your common set up tasks. This walkthrough will discuss
how to perform basic set up for ssh which will include:
* Setting up your ssh keys
* Placing your ssh keys in the appropriate location(s)
* Using your ssh keys to connect

SSH stands for Secure SHell. SSH will allow us to connect to other devices easily and safely.

If you'd like to dig into more details about SSH, I highly recommend [Digital Ocean's SSH Documentation](https://www.digitalocean.com/community/tutorials/ssh-essentials-working-with-ssh-servers-clients-and-keys#ssh-overview). This guide will not attempt to explain things in detail; however, I will provide 
easy to follow, step by step set up to get you up and moving with SSH on your devices.


Pre-requisites:
* This guide will assume you have a Unix device (MacOS or Linux distrobution). I will not be providing details for Windows here.
* You will need 2 devices in order to connect to one another. I recommend a RaspberryPi or a small droplet on Digital Ocean.

Terminology:
* Server: in the context of this guide the "server" will be the second device you are attempting to connect to.
* "Your Computer": in the context of this guide "your computer" will be the device you will be typing most of the commands from. This is your terminal window.
* Private Key: a special generated key that is meant to be kept 'private', or secure. You should not share this with anyone.
* Public Key: a special generated key that is meant to be public. It allows you to share with others how they can securely connect with you.

# Initial Setup

You have two devices available: your server and your computer. First, we will start with your computer. If you've completed some form of ssh key generation before, you may skip this step; however, if you're still uncomfortable with the flow, I suggest at least reading through the steps and confirming your set up.

## Setup -- Step 1 -- Key Generation

Linux provides a simple tool for this: `ssh-keygen`. We will need to generate a key pair. The key pair will consist of 2 parts: a Public Key (`id_xxx.pub`) and a Private key (`id_xxx`). When generating this key pair, you will be asked a series of questions which will determine the security strength of these keys. For the purpose of today's guide we will focus on the RSA key.

Let's generate the key! You will type `ssh-keygen` and then be prompted with additional questions. The answers are up to you; however, for this guide I will assume you allowed the default answers. (Just keep hitting enter :) ) 

```shell
>ssh-keygen

Generating public/private rsa key pair.
Enter file in which to save the key (/Users/your_user_is_here/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa
Your public key has been saved in id_rsa.pub
The key fingerprint is:
SHA256:xX+MnR6s6+GbWEnG5X21FHMj8cmd32rpRswpY1GL63c your_username@your_computer 
The key's randomart image is:
+---[RSA 3072]----+
|             o.+.|
|         .    = O|
|          o  o.Bo|
|         . ooBoo=|
|        S   *=Oo=|
|           o===+.|
|           o=+=  |
|           +.*o E|
|          ..*+.. |
+----[SHA256]-----+
```

## Setup -- Step 2 -- Checking our work

Now that the key has been generated, let's investigate what happened.

The file will have been generated inside of your .ssh directory, so let's start by moving into that directory (`cd .ssh` in your terminal) and running an `ls`.

```shell
~/.ssh > ls

id_rsa        id_rsa.pub
```

If you see at least these two files, great! Now, just to be a bit familiar with them, let's open them up. Use the terminal editor of your choice; however, I'll be using vim. We'll start by investigating the private key. `vim id_rsa` should show something like this:

```shell
-----BEGIN OPENSSH PRIVATE KEY-----
Something like 36 lines of letters and numbers here
that ends with an =
-----END OPENSSH PRIVATE KEY-----
```

As we can see, the Key is clearly labeled as private. Keep this file safe! Do not share it with others! Sharing this file with others will allow people to impersonate you and thus give them access to the same things you have access to.

Now let's investigate the other key... `vim id_rsa.pub`

```shell

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAB... (and so on)Vsk= your_username@your_computer
```

Do these match up with what you are expecting? This file with the `.pub` extension is for sharing. So if you are ever in doubt, don't hesitate to open and investigate the file for yourself. NEVER share the private key, but DO share the `id_rsa.pub`. This will be important for things like git, server connections, and more.


## Setup -- Step 3 -- Adding the SSH key to your server

Now we will need your other device! There are certainly more advanced ways of doing the following; but this guide is intended to help you at a basic and accessible level. Your online searches may change what you would expect here, so YMMV (your mileage may vary). 

This guide will assume you have access to `ssh-copy-id`. You will need your sign in information for your other device.

It is important to note that some of you may be using an ip address while others will be using a hostname for the server. If the device is on the same network as you (say, in your house on the same wifi connection) you can use the host name. In writing this tutorial, I had a raspberrypi available so I was able to use `my_username@raspberrypi` to make the connection work.

```shell
ssh-copy-id your_username_on_your_server@your_server_here
```

Answer the prompts as necessary which will include your password for the server you are connecting into. If this was successful, you should now be able to simply use the ssh command:

```shell
ssh your_username@your_server

Linux raspberrypi 6.1.0-rpi8-rpi-2712 #1 SMP PREEMPT Debian 1:6.1.73-1+rpt1 (2024-01-25) aarch64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.

your_user@your_server:~ $
```
Now you are connected to your server remotely and free from passwords! If a password was requested, something isn't quite right and you should review the steps.

## Checking our work -- Step 4

To understand what's taken place, lets check inside the server to verify before we close up for the day.

As you are SSH'd into the server, let's investigate the `.ssh/` directory. so `cd .ssh` and then `ls` to give us some information.

Inside the `.ssh/` directory, we should see `authorized_keys`. Let's open that up with `vim authorized_keys`

This should look familiar, right? 

```shell

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAB... (and so on)Vsk= your_username@your_computer
`

If there are multiple ssh keys, you'll see them listed line by line, here. The more keys and computers you give access to the more `authorized_keys` will live within this file. If you are using solid host names for your computer and your server, it should be obvious which computers are authorized to which servers from this file.

## Wrapping up -- In Closing

This is consider one of the most basic skills to access remote devices and do work. As long as your devices are connected via networking somehow; you'll be able to connect to one another. Next time you decide to work from a coffee shop or check something out, try ssh-ing to your server from there and playing in the terminal.
