# Shiny Platform  

This platform can be accessed by anyone with access in **Stiletto** and only requires setting up a VNC session.

## Requirements to be done only the first time:  
In Stiletto:   
   
1)) Set the [VNCserver](https://davetang.github.io/listerlab/vnc.html):  

- Set password for your VNCserver:  
```console
[eecheverria@PEB-SRVNIX-STILETTO ~]$ vncpasswd
```    

- Edit ~/.vnc/xstartup file:

```console
[eecheverria@PEB-SRVNIX-STILETTO ~]$ vim ~/.vnc/xstartup
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
[eecheverria@PEB-SRVNIX-STILETTO ~]$ chmod  u+x ~/.vnc/xstartup
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
kike@kike-X541UA:~$ ssh -N -Y -f -i your_private_key_file -L 59<Port>:127.0.0.1:59<Port> eecheverria@130.95.176.224
```   

- Open the VNCViewer and start the connection by typing *127.0.0.1:59#PORT#* and enter the password you used when you ran *vncpasswd*.    

Tip: In the VNCViewer session right-click>Properties>Options and set Picture quality to High.  
  
In the VNCViewer terminal:

```console
[eecheverria@PEB-SRVNIX-STILETTO ~]$ cd /scratchfs/<USER>  
[eecheverria@PEB-SRVNIX-STILETTO ~]$ /usr/bin/Rscript /dd_public/www/listerlab/kike/shinyApp_ListerLab/app.R
```  

## After closing the app

Remember to close IN stiletto the VNCserver port and to kill the .logs file 
```console
[eecheverria@PEB-SRVNIX-STILETTO ~]$ vncserver -kill :<PORT>  
[eecheverria@PEB-SRVNIX-STILETTO ~]$ rm ~/.vnc/PEB....log...
```  

**All sessions files (.xml) and track files must be stored with (at least) group access if you want people from the lab to be able to interact with (chmod 750)**

**If it's your first time launching IGV in the server change the default directory to avoid occupying too much space at ~/home and save it to any of your workding_data directories.** From the UI. Select "View > Preferences > Advanced and look to the bottom for the option to move the IGV directory.


## Platform structure
The data visualization platform is divided in three tabs. The first tab **Single Cell Experiment** is designed to store ouputs (bw, bed or bam files) of Single Cell Projects and to create IGV sessions on the "fly" but choosing which of these files should be included. The second tab **IGV sessions** has an IGV sessions database that helps sharing IGV sessions one prepare with all the people in the lab. The third tab **Sequencing information** has two databases of all the libraries (with the *RLs* and *PLs* numbers and the absolute path to their location, this was a suggestion from Jahnvi. 
 
### Single Cell experiments tab  
![Single Cell Experiment tab](https://user-images.githubusercontent.com/35218702/85836888-e5cdca00-b7c9-11ea-8e88-f47630ed32d5.png)  

In this tab there is a database with different single cell projects (I have created the first three of them so that everybody can interact with it). Once a project is selected, one needs to choose which files (bigwig, bam or bed) are wanted to visualize and to which reference genome to align (currently hg18, hg19, hg38,mm9,mm10 are included, but I can easily add more) and an IGV session will be generated for those files. 

It is also possible to create new projects and choose which files to upload. Finally one can delete a single cell project from the database if it was not created correctly or any other reason.  

In the bottom left there is a button that should be run from time to time to clean the temporary directory in which IGV creates the session. This sessions are *.xml* files so they are super small.

### IGV sessions tab  
![image](https://user-images.githubusercontent.com/35218702/85838289-ebc4aa80-b7cb-11ea-9503-73cbd321d980.png)

In this second tab it is hosted a database with IGV sessions that everybody can visualize. Besides, it is possible to add new sessions but just uploading the .xml file from IGV or to delete existing ones. Refer to [this guide](https://github.com/kikegoni/Computational-biology-tools/blob/master/VNCServer_setup.md) to open IGV from the VNC in stiletto.   

### Sequencing information tab  
![image](https://user-images.githubusercontent.com/35218702/85839279-4dd1df80-b7cd-11ea-9484-3bc6c06eb6f0.png)
 
 In this section there is a database of the libraries and the location where they can be found in the servers. There are two subtabs (one for the *RLs* numbers and one for the *PLs* numbers). In both of them there is a button that gives the option to update the databases (it will prompt a message saying when the last update was performed, since it takes ~3 minutes to run). 

### Reminds:  
- If two users are interacting with the platform at the same time some changes that one user makes are not immediately reflected in the app for the other user. So in that case it is just necessary to close de app and run the *Rscript* command again.  

- All sessions files (.xml) and track files must be stored with (at least) group access if you want people from the lab to be able to interact with (chmod 750)

- When openining an IGV session in the first two tabs just keep in mind that it takes ~20 seconds to launch IGV.

- The platform directory is hosted ***/dd_public/www/listerlab/kike/shinyApp_ListerLab/*** and is opened with all permissions in case someone wants to edit something, but you can always contact me with any question. 

