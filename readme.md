
# Readme

## Purpose of this repository

---

- To learn Samba Server on Linux.
- Protocol my steps and be able to reconstruct the results if necessary.
- To backup my current and the original config settings.
- Eventually: To be able to transfer data between my devices when i manage to get this service properly running.

## References

---

- More or less most here mentioned steps are copied / inspired from [this YouTube video](https://www.youtube.com/watch?v=4zIETevbstg).
- Also the [Samba.org](https://wiki.samba.org/index.php/Main_Page) is very helpful.
- Initial inspiration from [this YouTube video](https://www.youtube.com/watch?v=7Q0mnAT1MRg).


## Remaining Questions:

---

1. Do i need masks?
   1. What is the function of these masks?
   2. What is the current default?
   3. Differences between:
      1. 0664
      2. 0775
   4. Are these the reasons for the two problems I have?
1. How to scan documents with the iPad and save it directly on the server?
1. How to save pictures from iPad directly on the Server?
1. Security
   1. Where are the passwords exactly saved?
   2. When i update the Linux password for the user John, is it properly updated in Samba as well?
   3. Since it is "only one limited user for only one specific folder without a shell", is the potential damage low?


## Config Settings

---

- The actually settings are not saved as markdown. You can find the current settings in smb.conf.
- The original settings are saved in smb.original.conf


## Relevant Samba Terminal Commands

---

### 1. Basic

Install Samba.

```
sudo apt install samba
```

Check Samba status.

```
sudo systemctl status smbd
```

Start Samba.

```
sudo systemctl start smbd
```

Stop Samba.

```
sudo systemctl stop smbd
```

Restart Samba.

```
sudo systemctl restart smbd
```

### 2. Config

Config Path

```
cd /etc/samba/smb.conf
```

After editing the config file, check the syntax of the samba file.

```
testparm
```

### 3. Group, User, Permission

Create the folder, which Samba is now referring to.

```
sudo mkdir /srv/samba/helpdesk
```

Create the group who is responsible for the access. Groups allow to add more than one user to it. Here we call it "analyst".

```
sudo groupadd analyst
```

Create user woo will be member of the "analyst" group. The user does not need a home directory (-M) and nologin, so the shell is removed as well. Here i used the generic name "john".

```
sudo useradd -M -s /sbin/nologin john
```

Set the password for these account.

```
sudo passwd john
```

Add user to the group analyst.

```
sudo usermod -aG analyst john
```

Add the current Linux-user to the group analyst.

```
sudo usermod -aG analyst $USER
```

Set the group analyst as the owner of the folder /helpdesk.

```
sudo chgrp -R analyst /srv/samba/helpdesk
```

Add read and write permissions to the members of the group analyst for the folder /helpdesk.

```
sudo chmod -R g+rw /srv/samba/helpdesk
```

Restart Samba.

```
sudo systemctl restart smbd
```

Add users to the Samba database.

```
sudo smbpasswd -a john
```

If you want to synchronise the Linux password and the  Samba password, add these lines in smb.conf. (It seems like these code lines are already in place for the original config file.

```
unix password sync = yes
passwd program = /usr/bin/passwd %u
```


Enable the users in the Samba database.

```
sudo smbpasswd -e john
```


### 4. Additional Relevant Terminal Commands

Modify an existing Samba user's password.

```
sudo smbpasswd john
```

Delete Samba user.

```
sudo smbpasswd -x john
```

Delete UNIX user.

```
sudo userdel -r john
```

Change password in Linux.

```
sudo passwd john
```
