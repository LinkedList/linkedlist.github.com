---
layout: post
title: "Kubuntu and ssh-agent"
description: ""
category: 
tags: [kubuntu, kwallet, ssh-agent, github]
---
Almost no guide I found on the Internet about ssh-agent worked for me and writing a passphrase on every pull and push is tedious.
So here is my working solution for Kubuntu 14.04.
These are the steps needed for ssh-agent and ssh-add to automatically take your passphrase from Kwallet in Kubuntu.

1. First download ksshaskpass
        
        apt-get install ksshaskpass

2. Then modify your <code>.bashrc</code> file and append this to the end 

         echo "----Starting ssh-agent and ssh-add----"
         eval $(ssh-agent)
         export SSH_ASKPASS=/usr/bin/ksshaskpass
         ssh-add </dev/null
         echo "--------------------------------------"

3. Make sure KWallet is enabled, or it will ask for a passphrase everytime you open a new Konsole window.
4. Open a new Konsole window and you'll be asked for a passphrase one last time.
5. You can test that everything is working properly by going to a git repository and fetching

         git fetch
