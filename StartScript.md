# Start Script 

For OpenNebula VMs, temporary fix for SSH

```bash
#!/bin/bash

echo -e "fsktm\nfsktm\n\n\n\n\n\n\n" | adduser nrl-um 

usermod -aG sudo nrl-um

echo $(hostname -I | cut -d\  -f1) $(hostname) | sudo tee -a /etc/hosts

sed -i 's/RSAAuthentication yes/RSAAuthentication no/g' /etc/ssh/sshd_config

sed -i 's/PubkeyAuthentication yes/PubkeyAuthentication no/g' /etc/ssh/sshd_config

sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config

systemctl restart ssh
```

