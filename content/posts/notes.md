+++
title = "notes"
tags = ["notes"]
date = "2019-05-07"
+++


# Mount remote directory

```
sshfs local@smr19:. ~/smr19
```

### Passwordless ssh:

ssh to tustin with k388h1, create passwordless login to smr19 without key-password


```
%From k388h1@tustin.iau.dk

ssh-keygen
%promt for overide and blank password
ssh-copy-id local@smr19
```
### Then from local pc create an sshfs with a tunnel to tustin.

```
sshfs local@smr19:. <localdir> -o ssh_command='ssh -t k388h1@tustin.iau.dtu.dk'
```


# Connect Via Access-point

Android access point with name "Maizerunner" and password "g***nDTU"

```
%check static ip on robot
sudo /etc/NetworkManager/system-connection/Maizerunner
```
When connected to same access point

```
sshfs local@192.168.43.19:. <local_dir>
```

and normal ssh

```
ssh local@192.168.43.19
```
