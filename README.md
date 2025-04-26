# SSH-Public-Keys

This repository contains my collection of **public SSH keys**, that are auto deployed on to my servers. 

## Structure

```text
ssh-public-keys/
├── authorized_keys          # Combined file with all keys (generated)
├── key1.pub         
├── key2.pub    
├── key3.pub    
```

## Regenerating `authorized_keys`

After adding a new `.pub` file:

```bash
cat *.pub > authorized_keys
```

## How to install SSH and crontab to auto install ssh keys (crontab)

### Install and config openssh-server
```bash
sudo apt update

sudo apt install -y openssh-server 

sudo systemctl enable ssh
sudo systemctl start ssh
```

#### One liner with sudo 
```bash
sudo apt update && sudo apt install -y openssh-server && sudo systemctl enable ssh && sudo systemctl start ssh
```
#### One liner without sudo
```bash
apt update && apt install -y openssh-server && systemctl enable ssh && systemctl start ssh
```

### Enabling PubkeyAuthentication
nano /etc/ssh/sshd_config
```bash
PubkeyAuthentication yes
```

Then restart SSH 
```bash
systemctl restart ssh
```

### Autodownload SSH key file every 5 minits
run this in the home dir of your user you want to login in as with SSH 
```bash
( crontab -l 2>/dev/null; \
  echo "*/5 * * * * mkdir -p $(pwd)/.ssh && curl -fsSL https://raw.githubusercontent.com/nikon-63/ssh-public-keys/main/authorized_keys > $(pwd)/.ssh/authorized_keys" \
) | crontab -
```