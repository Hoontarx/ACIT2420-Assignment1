# ACIT2420-Assignment1
Assignment 1 instruction manual

# Creating a remote server using DigitalOcean

## Introduction

DigitalOcean is a cloud computing vendor and we will be using their services to create a remote server that will be running Arch Linux as its operating system. You'll learn how to generate SSH keys, upload a custom Arch Linux Image, use Cloud-init to automate the initial setup, create a Droplet on DigitalOcean, and connect to your server using the SSH keys you have generated. 

### The Goals of This Tutorial
1. Generating SSH keys to have secure access to your server

2. Uploading a custom Arch Linux image to DigitalOcean

3. Creating a cloud-init YAML file to automate setup tasks such as user creation and package installation

4. Creating an Arch Linux Droplet using DigitalOcean

5. Connecting to your Droplet using the SSH keys generated

## Creating SSH Keys on your Local Machine

SSH keys are what we will be using to access our Server on DigitalOcean from your local machine (your computer). These keys functionally resemble passwords, similar to an account you might set up with login credentials (“What Is an SSH Key? An Overview of SSH Keys”). 

SSH keys will be used over password authentication as SSH keys are more secure. This is because with SSH keys, only approved devices with the keys are able to log into the server. Also, password authentication is susceptible to brute force attacks by hackers. This is when hackers use a program that continuously tries different username and password combinations to try to access accounts. SSH keys prevent this issue from occurring since they keys need to be on your local machine (Horin, 2023).  

There will be a total of two SSH keys generated, an authentication key and an identity key. The authentication key is a public key and it acts like a lock that you would put on your locker. It tells the Server who has access to it and who doesn't have access. The identity key is a private key, this is like the password you would use to open your lock. The SSH client uses these private keys to authenticate the user (“What Is an SSH Key? An Overview of SSH Keys”).

Now lets get into generating our SSH keys!

### Creating a new SSH key pair
[!note] If you're using Windows, you might need to create a .ssh directory in your home directory first. To check to see if you already have one, you can use the following commands in your terminal.

```sh
cd ~ #This will take you to your home directory

ls #This will print out a list of directories
```

The .ssh directory can be found towards the top of the list as shown in the below screen shot:
![[Pasted image 20240927225448.png]]

If you're missing this directory then you can run the following command in your terminal:
```sh
mkdir .ssh
```



## Adding a Custom Arch Linux Image Using the Web Console

For the server we are creating on DigitalOcean, we will be using Linux as our operating system and Arch is the distribution of Linux we will be using in this tutorial. 

## Using a Cloud-Init Configuration File to Automate Initial Setup

## Creating a Droplet Running Arch Linux Using the DigitalOcean Web Console

## Connecting to your Server Using your SSH Keys

Now that an Arch Linux Droplet has been created using cloud-init to automate the setup of the user and have paired the SSH keys generated to DigitalOcean, it's time to connect to the Server using the terminal.


## References:

“What Is DigitalOcean?” _SearchCloudComputing_, 
	www.techtarget.com/searchcloudcomputing/definition/DigitalOcean.
	
“What Is an SSH Key? An Overview of SSH Keys.” _Www.ssh.com_, www.ssh.com/academy/ssh-keys.

Horin, Martin. “Why Use SSH-Key Authentication instead of Password Authentication?” _Www.sharetru.com_, 13 Apr. 2023, www.sharetru.com/blog/why-use-ssh-key-authentication-for-sftp-instead-of-password-authentication#:~:text=From%20a%20security%20standpoint%2C%20using.
