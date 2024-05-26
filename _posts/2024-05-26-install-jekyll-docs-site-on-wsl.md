---
layout: post
title:  "Install Jekyll on windows with WSL"
categories: wsl jekyll
tags: homelab wsl jekyll
---

This the installation process to install and maintain a similar doc/note.

First platform of choice for this one is Windows 11, I will be using WSL.

Prerequites :
- WSL up and running
- Ubuntu 22.04 base image
- Github account
- VScode (optionnal)

## Installing ubuntu 22.04 on WSL

```powershell
winget search ubuntu
```

If you don't have winget, you can use Microsoft Store it's working or you can download it [here](https://github.com/microsoft/winget-cli/releases)

You should have something similar to this :
```
Nom                ID                      Version         Correspondance Source
---------------------------------------------------------------------------------
Ubuntu             9PDXGNCFSCZV            Unknown                        msstore
Ubuntu 22.04.3 LTS 9PN20MSR04DW            Unknown                        msstore
Ubuntu 20.04.6 LTS 9MTTCL66CPXJ            Unknown                        msstore
Ubuntu 18.04.6 LTS 9PNKSF5ZN4SW            Unknown                        msstore
Ubuntu (Preview)   9P7BDVKVNXZ6            Unknown                        msstore
Ubuntu 24.04 LTS   9NZ3KLHXDJP5            Unknown                        msstore
Ubuntu 22.04 LTS   Canonical.Ubuntu.2204   2204.1.7.0      Tag: ubuntu    winget
Ubuntu 20.04 LTS   Canonical.Ubuntu.2004   2004.2021.825.0 Tag: ubuntu    winget
Ubuntu 18.04 LTS   Canonical.Ubuntu.1804   1804.2019.522.0 Tag: ubuntu    winget
Ubuntu 16.04 LTS   Canonical.Ubuntu.1604   1604.2019.523.0 Tag: ubuntu    winget
alarm-cron         bl00mber.alarm-cron     0.1.1           Tag: ubuntu    winget
YTDownloader       aandrew-me.ytDownloader 3.17.1          Tag: ubuntu    winget
```

Here I'm using Ubuntu 22.04.3 LTS

```powershell
winget install 9PN20MSR04DW
```

Run the wsl instance with the command :
1. Get the name of the instance with the command
```powershell
wsl -l
```
2. Run it
```powershell
wsl -d Ubuntu-22.04
```

Initialize it with user, password, ...

Update it with apt :
```bash
sudo apt update && sudo apt upgrade
```


### Optionnal creation of a « template » from ubuntu 22.04

Now we have a running WSL instance of ubuntu 22.04, I prefer to archive it and using it as a template.

Step :
1. Create a folder named WSL in Documents for example
2. Create a folder named base-image in WSL folder
3. Export it as a tar file : 
```powershell
wsl --export Ubuntu-22.04 D:\Users\<username>\Documents\WSL\base-image\base-image.tar
```
4. Import it with a new name :
```powershell
wsl --import Jekyll D:\Users\<username>\Documents\WSL\jekyll\ D:\Users\<username>\Documents\WSL\base-image\base-image.tar
```
5. Run it
```powershell
wsl -d Jekyll
```
If you're logged as root do this
```bash
nano /etc/wsl.conf
```
add this at this end of the file, replace username by yours
```text
[user]
default = username
```

### Installing rbenv

[Source](https://www.digitalocean.com/community/tutorials/how-to-install-ruby-on-rails-with-rbenv-on-ubuntu-22-04)

1. Install the dependencies :
```bash
sudo apt update
sudo apt install git curl libssl-dev libreadline-dev zlib1g-dev autoconf bison build-essential libyaml-dev libreadline-dev libncurses5-dev libffi-dev libgdbm-dev
```
2. Install rbenv
```bash
curl -fsSL https://github.com/rbenv/rbenv-installer/raw/HEAD/bin/rbenv-installer | bash
```
3. Add ~/.rbenv/bin to the path :
```bash
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
```
4. Then, add the command eval "$(rbenv init -)" to your ~/.bashrc file so rbenv loads automatically:
```bash
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
```
5. Apply the changes you made to your ~/.bashrc file to your current shell session:
```bash
source ~/.bashrc
```

### Install ruby

1. Run
```bash
rbenv install -l
```
2. Choose latest 3.2.x
```bash
rbenv install 3.2.x
```
3. Once it's done make it the default ruby version
```bash
rbenv global 3.2.x
```

### Installing nvm
Install NVM with command:
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

### Install Jekyll bundler
```bash
gem install jekyll bundler
```

## Create a SSH Key
On the Jekyll WSL instance :
1. Generate a new key with your github mail account
```bash
ssh-keygen -t ed25519 -C "youremail@example.com"
```
A prompt will ask if you want the default location press enter, after you have the choice to add passphrase, if don't want it just press *Enter* twice.
2. Get the public key
```bash
cat ~/.ssh/id_ed25519.pub
```

## Create a new repo with Chirpy Starter
**You must have a github account**

1.Create a new repo using [chirpy-starter](https://github.com/new?template_name=chirpy-starter&template_owner=cotes2020) template, name it *githubusername*.github.io and make it public.
2. On github go to Settings > Deploy Keys > Add deploy key :
- Choose a Title
- Paste de public key
- Check `Allow write access` 
- Click `Add key`
3. Clone the repository :
- Click `Clone`
- Click `SSH`
- Copy text to clipboard (`repo-text`)
4. On the Jekyll machine :
- ```bash
git clone repo-text
```
If asked add github to known hosts
```bash
cd repo-name
bundle
```

Setting up your user in local git repo
```
git config --global user.name "your-github-user"
git config --global user.email "your-github-email"
```

## Now you're ready

Read the chirpy [docs](https://chirpy.cotes.page/) you can now make some changes, write a posts, ...

Using for example vscode with the commande `code .` in the repo folder

If you want to see a live version of your site in local you can use the following command in the repo folder inside the repo folder
```bash
bundle exec jekyll s
```
The site should be accessible on `http://127.0.0.1:4000`

Once you're ready to publish, commit and push to github :
```bash
git add .
git commit -m "made some changes"
git push
```

> Every type you use `git push` the site is published on *username*.github.io by the github actions.
{: .prompt-warning }