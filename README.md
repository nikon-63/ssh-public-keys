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

## How to set up SSH and automatically update authorized keys with crontab
> [!NOTE]
> `wget` and `crontab` are required

### Install and configure OpenSSH server

Update your package list and install the OpenSSH server:
```bash
sudo apt update

sudo apt install -y openssh-server 

sudo systemctl enable ssh
sudo systemctl start ssh
```

#### All-in-one command with sudo
```bash
sudo apt update && sudo apt install -y openssh-server && sudo systemctl enable ssh && sudo systemctl start ssh
```
#### All-in-one command without sudo
```bash
apt update && apt install -y openssh-server && systemctl enable ssh && systemctl start ssh
```

### Enable public key authentication

Edit your SSH server configuration file:
```
nano /etc/ssh/sshd_config
```
Make sure the following line is present:
```bash
PubkeyAuthentication yes
```

Restart the SSH service to apply changes:
```bash
systemctl restart ssh
```

### Automatically download the latest authorized_keys every 5 minutes

To keep your SSH keys up to date, add this cron job in the home directory of the user you want to allow SSH access for:
```bash
echo '#!/usr/bin/env bash' > regen_ssh_keys.sh && \
echo 'DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" >/dev/null 2>&1 && pwd)"' >> regen_ssh_keys.sh && \
echo 'mkdir -p "$DIR/.ssh"' >> regen_ssh_keys.sh && \
echo 'wget -qO "$DIR/.ssh/authorized_keys" https://raw.githubusercontent.com/nikon-63/ssh-public-keys/main/authorized_keys' >> regen_ssh_keys.sh && \
chmod +x regen_ssh_keys.sh && \
( crontab -l 2>/dev/null; echo "*/5 * * * * $(pwd)/regen_ssh_keys.sh" ) | crontab - && \
crontab -l
```
This will ensure your `.ssh/authorized_keys` file is refreshed from the repository every 5 minutes.
