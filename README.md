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
