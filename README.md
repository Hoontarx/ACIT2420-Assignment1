# ACIT2420-Assignment1
Assignment 1 instruction manual

# Creating a remote server using DigitalOcean

## Introduction

DigitalOcean is a cloud computing vendor and we will be using their services to create a remote server that will be running Arch Linux as its operating system. You'll learn how to generate SSH keys, upload a custom Arch Linux Image, use Cloud-init to automate the initial setup, create a Droplet on DigitalOcean, and connect to your server using the SSH keys you have generated. 

> [!note] Note
> A Droplet is the name DigitalOcean uses for virtual private servers. These virtual servers are servers running on a virtual machine.

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

>[!note] Note
>If you're using Windows, you might need to create a .ssh directory in your home directory first. To check to see if you already have one, you can use the following commands in your terminal.

```sh
cd ~ #This will take you to your home directory

ls #This will print out a list of directories
```

The .ssh directory can be found towards the top of the list as shown in the below screen shot:
![[./assets/Pasted image 20240927225448.png]]

If you're missing this directory then you can run the following command in your terminal:
```sh
mkdir .ssh
```

Now that we have the necessary directory we can create our SSH keys!

1. In the terminal, run the following command. 
Replace "your-user-name" with your Windows username and the email to your email:
```sh
ssh-keygen -t ed25519 -f C:\Users\your-user-name\.ssh\do-key -C "youremail@email.com"
```

>[!note] Note
>Passphrases are optional, in this tutorial we will not be entering one. If you do, make sure to keep it somewhere safe and make it something that you can remember

2. Hit **Enter** twice

 There should now be two plain text files located in your .ssh directory and the terminal output will look similar to this:
 ![[./assets/Pasted image 20240927232641.png]]

### Adding the SSH keys to your DigitalOcean Account

Now that the SSH key pairs have been created, it's time to add them to your DigitalOcean Account. Once added, they will be automatically copied to your Droplet once it's created.

Since the keys are plain text files, we can run a command in the terminal to copy them. This will copy it to your clipboard so we can paste them into DigitalOcean.

1. Run the following command in your terminal
```sh
Get-Content C:\Users\your-user-name\.ssh\do-key.pub | Set-Clipboard
```

2. In DigitalOcean, Click Settings on the left sidebar
![[./assets/Pasted image 20240928000336.png]]
3. Click Security
![[./assets/Pasted image 20240928000445.png]]
4. Click Add SSH Key

5. Paste the keys into the SSH Key Content box

6. Type a name for your Keys

7. Click Add SSH Key

The SSH keys are now added to your DigitalOcean account and are ready for use when we create the Droplet!
## Adding a Custom Arch Linux Image Using the Web Console

For the server we are creating on DigitalOcean, we will be using Linux as our operating system and Arch will be the distribution of Linux we are using in this tutorial. 

The Arch Linux Image can be found at the following link:
https://gitlab.archlinux.org/archlinux/arch-boxes/-/packages/1545

The image we want to download is the "cloudimg" one with ".qcow2" at the end as shown in the screen shot below:
![[./assets/Pasted image 20240927234215.png]]

We will be using ".qcow2" as it's known for its efficiency and flexibility (N. McNinch, personal communication, September 3, 2024).

Now that we have download the Arch Linux Image, we want to add it to DigitalOcean.

1. On the left sidebar under Manage, Click Backups & Snapshots
![[./assets/Pasted image 20240928000807.png]]

2. Click Custom Images
![[./assets/Pasted image 20240928000902.png]]

3. Click Upload Image
![[./assets/Pasted image 20240928001119.png]]

Upload the Arch Linux Image that was downloaded earlier

4. Select Arch Linux in the Distribution dropdown menu

5. Select San Francisco 3

6. Click Upload Image

The Upload will take a couple of minutes, once completed, it will be ready to use when the Droplet is created.
## Using a Cloud-Init Configuration File to Automate Initial Setup

Now that the SSH keys have been generated and the Arch Linux Image has been added to DigitalOcean, it's time to create a cloud-init config file. 

If a Droplet was created currently, there would be a lot of manual setup that needs to be done. This would include creating a user, installing packages, and much more. Cloud-init lets us skip all of the manual setup and tells DigitalOcean to automatically setup everything needed when creating the Droplet (N. McNinch, personal communication, September 17, 2024).

In your terminal run the following command:
```sh
notepad cloud-config.yml
```

This will open notepad, which is a text editor, and create a new file called cloud-config.yml

Paste the following into the yml file that was just created:
```yml
#cloud-config
users:
		  - name: user-name #change me
    primary_group: user-group # change me
    groups: wheel
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-ed25519 your-ssh-public-key # change this

packages:
  - ripgrep
  - rsync
  - neovim
  - fd
  - less
  - man-db
  - bash-completion
  - tmux

disable_root: true
```

the # cloud-config needs to be line 1 as this is the identifier cloud-init looks for when setting up our account manually. Sudo will allow cloud-init to run the commands as a super user temporarily to properly install all the packages we need. Lastly, `disable_root: true` will disable the root user. We disable the root user to minimize damages if an attack does occur (N. McNinch, personal communication, September 17, 2024).

There's 3 sections we will want to manually replace: "name", "primary_group", and "ssh-authorized-keys".

The "name" section will be the name of your user, similar to your windows user name. 
The "primary_group" should be the same as what was used for "name"
The "ssh-authorized-keys" will be the public key footprint that was created earlier.

Run the following command to copy the ssh key fingerprint to your clipboard:
```sh
ssh-keygen -lf C:\Users\your-user-name\.ssh\do-key | Set-Clipboard
```

You'll get something that looks like this:
```
256 SHA256:Our1gOR6uX2weVMpH7eeDZGkV6I5KqWl+AIBm7RPFWA hjb@telus.net (ED25519)
```

Paste it into the .yml file and delete the "256 SHA256:" from the beginning and "(ED25519)" from the end.

Now our .yml file is ready and DigitalOcean will apply the settings we have set when creating the Droplet.
## Creating a Droplet Running Arch Linux Using the DigitalOcean Web Console

We've generated SSH keys and added them to DigitalOcean, added an Arch Linux Image to DigitalOcean, and created a cloud-init config file. Now it's time to create a Droplet in DigitalOcean using everything we did in the previous steps. 

To create a Droplet on DigitalOcean:

1. Click Create

2. Click Droplets

3. Select San Francisco

4. Make sure Datacenter is San Francisco SFO3

5. Click Custom Image

![[./assets/Pasted image 20240928013717.png]]

6. Select Arch Linux

7. Select Premium AMD and $7/mo

![[Pasted image 20240928013827.png]]

8. Select SSH Key and the key you created

![[./assets/Pasted image 20240928014008.png]]

9. Click +Advanced Options

![[./assets/Pasted image 20240928014204.png]]

10. Select Add Initialization Scripts

11. Paste the contents of your .yml file into the textbox

12. Assign your Droplet a host name

> [!note] Note
> The host name can be anything you want, this is just a way for you to identify your Droplets

 13. Click Create Droplet

It will take a couple minutes for DigitalOcean to create the Droplet. Once it's completed it should look like this:
![[./assets/Pasted image 20240928014648.png]]

You've just created your first Droplet! All that's left is using your terminal to connect to the running Server using SSH.
## Connecting to your Server Using your SSH Keys

Now that an Arch Linux Droplet has been created using cloud-init to automate the setup of the user and have paired the SSH keys generated to DigitalOcean, it's time to connect to the Server using the terminal.

We will be using an IDE to create a config file that has no file extension in the .ssh folder. An IDE is just a text editor, I will be using VSCode in the example, you can use one of your choice.

1. Open your IDE

2. In the IDE open the .ssh folder

3. Click Create New File

4. Name it "config" and hit enter

Now a config file should be created in the .ssh folder

5. In the config file, paste the following

```
Host arch
  HostName 143.198.140.15
  User arch
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/do-key
  StrictHostKeyChecking no
  UserKnownHostsFile /dev/null
```

6. Change the IP Address beside "HostName" to the IP Address of your Droplet

7. Save the file and exit

8. In the terminal run the following
```sh
ssh arch
```


## References:

“What Is DigitalOcean?” _SearchCloudComputing_, 
	www.techtarget.com/searchcloudcomputing/definition/DigitalOcean.
	
“What Is an SSH Key? An Overview of SSH Keys.” _Www.ssh.com_, www.ssh.com/academy/ssh-keys.

Horin, Martin. “Why Use SSH-Key Authentication instead of Password Authentication?” _Www.sharetru.com_, 13 Apr. 2023, www.sharetru.com/blog/why-use-ssh-key-authentication-for-sftp-instead-of-password-authentication#:~:text=From%20a%20security%20standpoint%2C%20using.

“- the Standard for Customising Cloud Instances.” _Cloud-Init.io_, cloud-init.io/.

“AIX 7.2.” _Ibm.com_, 24 Mar. 2023, www.ibm.com/docs/de/aix/7.2?topic=rbac-disabling-root-user. Accessed 28 Sept. 2024.

