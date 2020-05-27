# shinyApp_ListerLab

## Requirements to be done only the first time:  
In Stiletto:   
   
1)) Set the [VNCserver](https://davetang.github.io/listerlab/vnc.html):  

- Set password for your VNCserver:  
```console
[eecheverria@PEB-SRVNIX-STILETTO ~]$ vncpasswd
```    

- Edit ~/.vnc/xstartup file:

```console
[eecheverria@PEB-SRVNIX-STILETTO ~]$ vim ./vnc/xstartup
```  
Delete everything and write this:  
```console
#!/bin/sh
##unset SESSION_MANAGER
#unset DBUS_SESSION_BUS_ADDRESS

[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources

xsetroot -solid grey
vncconfig -iconic &
xterm -geometry 80x24+10+10 -ls -title "$VNCDESKTOP Desktop" &

#twm &
metacity &
#nautilus &
```    


- Give *execute* permission to *xstartup*:   

```console
[eecheverria@PEB-SRVNIX-STILETTO ~]$ chmod  u+x ./vnc/xstartup
```  

- Go to your local PC and download [VNC Viewer](https://www.realvnc.com/en/connect/download/viewer/)   


## Run app  
In stiletto:  

- Check which of the ports are not being used so that you can use them. The port is the number after the semicolon (:)

```console
[eecheverria@PEB-SRVNIX-STILETTO ~]$ ps -ef | grep vnc
hayden    5101     1  0 Nov18 ?        00:00:29 /usr/bin/Xvnc :1 -auth /home/hayden/.Xauthority -desktop PEB-SRVNIX-STILETTO:1 (hayden) -fp catalogue:/etc/X11/fontpath.d -geometry 1600x1050 -pn -rfbauth /home/hayden/.vnc/passwd -rfbport 5901 -rfbwait 30000
cpfluger  9288     1  0 Nov19 ?        00:00:00 /usr/bin/Xvnc :34 -auth /home/cpfluger/.Xauthority -desktop PEB-SRVNIX-STILETTO:34 (cpfluger) -fp catalogue:/etc/X11/fontpath.d -geometry 2560x1440 -pn -rfbauth /home/cpfluger/.vnc/passwd -rfbport 5934 -rfbwait 30000

```    

- Start the VNC server (I am using port 23):  

```console
[eecheverria@PEB-SRVNIX-STILETTO ~]$ vncserver -geometry 1150x820  :23
```    
In your local PC:  

- Open the ssh tunnel to the VNCserver:

```console
kike@kike-X541UA:~$ ssh -N -Y -f -L 59<Port>:127.0.0.1:59<Port> stiletto
```   

- Open the VNCViewer and start the connection by typing *127.0.0.1:59#PORT#* and enter the password you used when you ran *vncpasswd*.    

Tip: In the VNCViewer session right-click>Properties>Options and set Picture quality to High.  
  
In the VNCViewer terminal:

```console
[eecheverria@PEB-SRVNIX-STILETTO ~]$ cd /scratchfs/<USER>  
[eecheverria@PEB-SRVNIX-STILETTO ~]$ Rscript /dd_public/www/listerlab/kike/shinyApp_ListerLab/app.R
```  

## After closing the app

Remember to close IN stiletto the VNCserver port and to kill the .logs file 
```console
[eecheverria@PEB-SRVNIX-STILETTO ~]$ vncserver -kill :<PORT>  
[eecheverria@PEB-SRVNIX-STILETTO ~]$ rm ~/.vnc/PEB....log...
```  

**All sessions files (.xml) and track files must be stored in /dd_public with (at least) group access if you want people from the lab to be able to interact with (chmod 770)**

**If it's your first time launching IGV in the server change the default directory to avoid occupying too much space at ~/home and save it to any of your workding_data directories.** From the UI. Select "View > Preferences > Advanced and look to the bottom for the option to move the IGV directory.
