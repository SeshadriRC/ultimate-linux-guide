# User Management in Linux

## Introduction to User Management in Linux
Linux is a multi-user operating system, meaning multiple users can operate on a system simultaneously. Proper user management ensures security, controlled access, and system integrity. 

Key files involved in user management:
- `/etc/passwd` – Stores user account details.
- `/etc/shadow` – Stores encrypted user passwords.
- `/etc/group` – Stores group information.
- `/etc/gshadow` – Stores secure group details.

## Creating Users in Linux
To create a new user in Linux, use:

### `useradd` Command (For most Linux distributions)
```bash
useradd username
```
This creates a user without a home directory.

To create a user with a home directory:
```bash
useradd -m username
```

To specify a shell:
```bash
useradd -s /bin/bash username
```

### `adduser` Command (For Debian-based systems)
```bash
adduser username
```
This is an interactive command that asks for a password and additional details.

## Managing User Passwords
To set or change a user’s password:
```bash
passwd username
```

### Enforcing Password Policies
- **Password expiration**: Set password expiry days

```bash
chage -l <username>  # Check the password policy of user

root@ubuntu-dev:/home# chage -l sesha1
Last password change                                    : Feb 15, 2026
Password expires                                        : never
Password inactive                                       : never
Account expires                                         : never
Minimum number of days between password change          : 0
Maximum number of days between password change          : 99999
Number of days of warning before password expires       : 7

  
chage -M 90 <username> # Set password policy to user

root@ubuntu-dev:/home# chage -l sesha1
Last password change                                    : Feb 15, 2026
Password expires                                        : May 16, 2026
Password inactive                                       : never
Account expires                                         : never
Minimum number of days between password change          : 0
Maximum number of days between password change          : 90
Number of days of warning before password expires       : 7
  


```
- **Lock a user account**
```bash
  passwd -l username   # It locks the password of user,user cannot log in using password authentication anymore.

  root@ubuntu-dev:/home# passwd -l sesha1
  passwd: password changed.
```
  
- **Unlock a user account**
```bash
  passwd -u username

  root@ubuntu-dev:/home# passwd -u sesha1
  passwd: password changed.

```

## Modifying Users
Modify an existing user with `usermod`:
- Change the username:
```bash
  usermod -l new_username old_username  # However it will use the same home directory of old user
  
```
- Change the home directory:
```bash
usermod -d /new/home/directory -m username

root@ubuntu-dev:/home# usermod -d /home/sesha2 -m sesha2
root@ubuntu-dev:/home# su - sesha2
sesha2@ubuntu-dev:~$ pwd
/home/sesha2

```
- Change the default shell:
```bash
usermod -s /bin/zsh username # change the default sheel


root@ubuntu-dev:/home# cat /etc/passwd | grep sesha2
sesha2:x:1002:1002::/home/sesha2:/bin/bash

root@ubuntu-dev:/home# usermod -s /bin/zsh sesha2
usermod: Warning: missing or non-executable shell '/bin/zsh'

root@ubuntu-dev:/home# cat /etc/passwd | grep sesha2
sesha2:x:1002:1002::/home/sesha2:/bin/zsh

root@ubuntu-dev:/home# which zsh
root@ubuntu-dev:/home# which bash
/usr/bin/bash
root@ubuntu-dev:/home#

```

## Deleting Users
To remove a user but keep their home directory:
```bash
userdel username           # it won't delete the home directory
```

To remove a user and their home directory:
```bash
userdel -r username
```

## Working with Groups
### Creating Groups
```bash
groupadd groupname

root@ubuntu-dev:/home# groupadd sesha-group
root@ubuntu-dev:/home# cat /etc/group | grep sesha-group
sesha-group:x:1003:

```

### Adding Users to Groups
```bash
usermod -aG groupname username

root@ubuntu-dev:/home# usermod -aG sesha-group sesha2
root@ubuntu-dev:/home# cat /etc/group | grep sesha-group
sesha-group:x:1003:sesha2

root@ubuntu-dev:/home# id sesha2
uid=1002(sesha2) gid=1002(sesha1) groups=1002(sesha1),1003(sesha-group)


root@ubuntu-dev:/home# getent group sudo
sudo:x:27:ubuntu,sesha

```

### Viewing Group Memberships
```bash
groups username

root@ubuntu-dev:/home# groups sesha2
sesha2 : sesha1 sesha-group

```

### Changing Primary Group
```bash
usermod -g new_primary_group username

root@ubuntu-dev:/home# id sesha2
uid=1002(sesha2) gid=1002(sesha1) groups=1002(sesha1),1003(sesha-group)

root@ubuntu-dev:/home# usermod -g sesha2 sesha2
root@ubuntu-dev:/home# id sesha2
uid=1002(sesha2) gid=1004(sesha2) groups=1004(sesha2),1003(sesha-group)

```

### Remove user from a group

```bash
root@ubuntu-dev:/home# cat /etc/group | grep sudo
sudo:x:27:ubuntu,sesha


root@ubuntu-dev:/home# deluser sesha sudo
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
        LANGUAGE = (unset),
        LC_ALL = (unset),
        LANG = "en_US.UTF-8"
    are supported and installed on your system.
perl: warning: Falling back to the standard locale ("C").
info: Removing user `sesha' from group `sudo' ...
root@ubuntu-dev:/home# cat /etc/group | grep sudo
sudo:x:27:ubuntu

```

## Sudo Access and Privilege Escalation
### Adding a User to Sudo Group
On Debian-based systems:
```bash
# Before adding user to sudo group, we will getting below error
sesha@ubuntu-dev:~$ sudo su -
[sudo] password for sesha:
sesha is not in the sudoers file.

# Now add the user to sudo group
usermod -aG sudo username

# After adding we won't get error
root@ubuntu-dev:/home# cat /etc/group | grep sudo
sudo:x:27:ubuntu,sesha

sesha@ubuntu-dev:~$ sudo su -
[sudo] password for sesha:
```
On RHEL-based systems:
```bash
usermod -aG wheel username
```

### Granting Specific Commands with Sudo
Edit the sudoers file:
```bash
visudo
```
Then add:
```bash
username ALL=(ALL) NOPASSWD: /path/to/command   # Allow only specific command

sesha ALL=(ALL) NOPASSWD: ALL                   # Allow all commands, highly risky

sesha3 ALL=(root) NOPASSWD: /usr/sbin/service ssh stop  # Allow only to stop ssh service


sesha3@ubuntu-dev:~$ sudo service ssh start
[sudo] password for sesha3:
Sorry, user sesha3 is not allowed to execute '/usr/sbin/service ssh start' as root on ubuntu-dev.

```
