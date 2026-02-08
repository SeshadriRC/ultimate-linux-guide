
## apache2

```bash
apt install -y apache2

# change the port configuration
cat /etc/apache2/ports.conf

# need to update the virtualhost port, once we update above
cat /etc/apache2/sites-enabled/000-default.conf
```

## Openssh

```bash
# Install openssh
apt install -y openssh-server

# Configure PermitRootlogin to yes
vi /etc/ssh/sshd_config
```

## Vi

```bash
# used to install vi editor
apt install -y vim
```

## ss

```bash
# used to install ss utility
apt install iproute2 -y 
```
