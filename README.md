# wsl-ubuntu-gui
Setup Notes for installing WSL2 assisted Ubuntu GUI in Windows 10

## Linux on Windows: WSL2 with Desktop Environment via RDP


WSL (Windows Subsystem for Linux) is very common these days especially with the news that Windows will ship a Linux kernel with WSL 2.0!

Installing a Linux distro as WSL is easy via the Microsoft App Store and there are plenty of tutorials out there for it.

Most of the resources cover the access via Shell, Terminal, or other console based tools to the WSL2.

Running GUI software is possible and there are resources describing how to achieve this via VcXsrv.

But what if you:

## Want to have/access a Desktop environment on WSL2?

You can use any Desktop Environment you want, I will be using [Xfce](https://www.xfce.org/) in this example because it is lightweight.

Here is the quick rundown of all commands and steps, explained in the sections below. 

Debian based distros (Debian, Ubuntu,...):

#### Updating the system and installing Xfce4
```
sudo apt update && sudo apt -y upgrade
# sudo apt -y install kali-desktop-xfce    # Kali Linux
sudo apt -y install xfce4
```
The first command updates the source list and the packages. 
The `sudo apt -y install kali-desktop-xfce` installs a Kali Linux specific version of Xfce4 and `sudo apt -y install xfce4` will install the Xfce4 package for Debian based distros.

#### Installing Xrdp
```
sudo apt-get install xrdp
```
[Xrdp](http://www.xrdp.org/) is an open source remote desktop solution and also very lightweight and easy to configure. This command will install the package and setup the default configuration with port 3389.

#### Configuring Xrdp
```
sudo cp /etc/xrdp/xrdp.ini /etc/xrdp/xrdp.ini.bak
sudo sed -i 's/3389/3390/g' /etc/xrdp/xrdp.ini
sudo sed -i 's/max_bpp=32/#max_bpp=32\nmax_bpp=128/g' /etc/xrdp/xrdp.ini
sudo sed -i 's/xserverbpp=24/#xserverbpp=24\nxserverbpp=128/g' /etc/xrdp/xrdp.ini
echo xfce4-session > ~/.xsession
```
Copy the config file as backup before the changes, change the port from 3389 to 3390 and for quality reasons increase the bpp from 24 to 128. You can play with those settings but since this is a local connection, the speed should not be worse with those settings.

#### Configure the window motif start-up
```
sudo nano /etc/xrdp/startwm.sh
```
then comment out the following lines:
```
## test -x /etc/X11/Xsession && exec /etc/X11/Xsession
## exec /bin/sh /etc/X11/Xsession
```
and add these lines
```
## xfce
startxfce4
```
#### And finally restarting the xrdp service to apply the changes
```
sudo /etc/init.d/xrdp start
```
#### Remote login
Now in Windows, use Remote Desktop Connection to  connect `localhost:3390` to your desktop.

[![Connect to WSL DE via Xrdp](rdp.png)](rdp.png)

[![Login to WSL DE](xorg.png)](xorg.png)

[![Desktop of WSL via xrdp](xfce4-desktop.jfif)](xfce4-desktop.jfif)

##### Why the port change from 3389 to 3390?

Two reasons: security and sometimes port 3389 is used by a process on WSL and you get the message

> ```
> Your computer could not connect to another console session on the remote computer because you already have a console session in progress.
> ```
