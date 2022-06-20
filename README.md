# WSL2-SSH-INSTRUCTIONS
Guide on how to install wsl2 and configure it for ssh and rsync.

## Requirements
- Have Windows 10 version 1903 or higher, with Build 18362 or higher, for x64 systems, and Version 2004 or higher, with Build 19041 or higher, for ARM64 systems
- Internet connection

## WSL2 installation guide
- Run cmd or PowerShell as adminitrator
- Before select the distribution which you prefer, to see a list of available Linux distributions write  `wsl --list --online`
- Install wsl `wsl --install` this will install Ubuntu by default if you want other distribution then write ` wsl --install -d <Distribution Name>`
- Reboot Windows
- Check if the wsl version is two `wsl -l -v`
- If after check the version is one write `wsl --set-version <distro name> 2` to upgrade to WSL2
- To run WSL2 just write `wsl`, once inside create a user for the linux system

## WSL2 network setup
Windows and WSL2 have differents ip but are in the same subnet, without the bellow configuration is not possible connect the WSL2 with external devices using network
that because WSL2 is connected virtually to the Windows host.
If you dont care that WSL2 will not connected to external devices, skip this section.
- Run cmd or PowerShell as adminitrator
- Run WSL2 `wsl`
- Write in WSL2 `ip addr | grep eth0` to know the WSL2 ip address, which we will need
- Once known the ip write `exit` to close WSL2 and go back to cmd or powershell
- Write the bellow command to use the net shell "netsh" and add a portproxy rule. 
```
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=2222 connectaddress=172.23.129.80 connectport=2222

   Explanation:
   - listenaddress: 0.0.0.0 to allow connection to any IP address or write ips to specify who can connect
   - listenport, connectport: this will be the port used to be listened or connected by external devices, make sure that the port is not used by other service
   - connectaddress: ip of your choice , which is an internal address to your machine. 
```
- Write the bellow command to open an incoming Firewall Port, use the same port as used before
```
netsh advfirewall firewall add rule name=”Open Port 2222 for WSL2” dir=in action=allow protocol=TCP localport=2222
```

## WSL2 SSH configuration
- Open the WSL2 machine created previusly
- Install OpenSSH server  `sudo apt install openssh-server` if the machine is running other distribution that is not Ubuntu, may not include apt as a package         manager then search other way to install OpenSSH server
- Edit /etc/ssh/sshd_config file `sudo nano /etc/ssh/sshd_config`
- Write, use the same port and ListenAddress as the one used in the netsh interface
```
Port 2222
#AddressFamily any
ListenAddress 0.0.0.0
#ListenAddress ::

# To enable PasswordAuthentication
PasswordAuthentication yes
```
- Start ssh service `sudo service ssh start` if say  a no hostkeys available error try `sudo ssh-keygen -A`
- Try connecting to a external  device using ssh '
- IMPORTANT BE CAREFUL ABOUT IF THE CONNECTION FAIL
   - Checks that both devices are listening to each other, use ping commando for this
   - Checks netshs interface and WSL2 machine have the same ip in both computers
   - Check ssh service is enable

