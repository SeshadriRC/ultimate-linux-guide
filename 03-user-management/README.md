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
  usermod -s /bin/zsh username
  ```

## Deleting Users
To remove a user but keep their home directory:
```bash
userdel username
```
To remove a user and their home directory:
```bash
userdel -r username
```

## Working with Groups
### Creating Groups
```bash
groupadd groupname
```

### Adding Users to Groups
```bash
usermod -aG groupname username
```

### Viewing Group Memberships
```bash
groups username
```

### Changing Primary Group
```bash
usermod -g new_primary_group username
```

## Sudo Access and Privilege Escalation
### Adding a User to Sudo Group
On Debian-based systems:
```bash
usermod -aG sudo username
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
username ALL=(ALL) NOPASSWD: /path/to/command
```
