This repository contains information on how to install and debug Scopy for CentOS7 x86_64

**Step 1: m2k drivers**

For the following, you will need the most recent update of the m2k usb driver
udev rules

Copy and paste the following file into /etc/udev/rules.d/53-adi-m2k-usb.rules (as root):

	$ sudo vim /etc/udev/rules.d/53-adi-m2k-usb.rules

Append the file:

	"# allow "plugdev" group read/write access to ADI M2K devices
	SUBSYSTEM=="usb", ATTRS{idVendor}=="0456", ATTRS{idProduct}=="b672", MODE="0664", GROUP="plugdev" 
	SUBSYSTEM=="usb", ATTRS{idVendor}=="0456", ATTRS{idProduct}=="b675", MODE="0664", GROUP="plugdev"
	# tell the ModemManager (part of the NetworkManager suite) that the device is not a modem, 
	# and don't send AT commands to it
	SUBSYSTEM=="usb", ATTRS{idVendor}=="0456", ATTRS{idProduct}=="b672", ENV{ID_MM_DEVICE_IGNORE}="1"

Remember to put that file into /etc/udev/rules.d/53-adi-m2k-usb.rules (need root permissions to edit)

**Step 2: Verify the ADALM2000 is recognized.**

Before doing the following, restart the system to refresh the udev rules!
Connect the ADALM2000 via USB to computer. Wait for about 30 seconds since it 
takes a while to be recognized. It should be automatically mounted. For me the
device mounts in /run/media/monsterma/M2K. 

	$ tree /run/media/monsterma/M2K # shows the directory tree of the ADALM2000

	├── config.txt # contains the ip address
	├── img # icons
	│   ├── ADI_Logo_AWP.png
	│   ├── download.png
	│   ├── ez.png
	│   ├── favicon.ico
	│   ├── fb.png
	│   ├── GNURadio_logo.jpg
	│   ├── gp.png
	│   ├── ig.png
	│   ├── li.png
	│   ├── mathworks_logo.png
	│   ├── PlutoSDR.png
	│   ├── prof_blue.png
	│   ├── ss.png
	│   ├── style.css
	│   ├── sw.png
	│   ├── tw.png
	│   ├── version.js
	│   ├── yk.png
	│   └── yt.png
	├── info.html
	├── LICENSE.html
	└── m2k-calib.ini # calibration information
1 directory, 23 files


**Step 3: Install Flatpak package manager (or update it)**

	 Run the following:
	 $ sudo yum install flatpak 
	 $ flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo # Scopy repo


**Step 4: Download and extract the .tar.gz source code for Scopy**
	 The source code is available here: 
	 My file is called: scopy-v1.2.0-Linux-flatpak.zip

	 $ tar -xvf scopy-v1.2.0-Linux-flatpak.zip # extracts the file scopy-v1.2.0-Linux-flatpak


**Step 5: Install Scopy with Flatpak:**

	 $ flatpak install scopy-v1.2.0-Linux-flatpak


**Step 6: Create proper script to run Scopy (do not run yet)**
	 
 Copy scopy.sh to the same directory as scopy-v1.2.0-Linux-flatpak (note: the version may be different)
 Do *not* run this yet:
	 $ ./scopy.sh # runs Scopy and unsets SESSION_MANAGER


**Step 7: Fire up Scopy**

 Plug in the ADALM2000 first! Wait for the ADALM2000 to appear mounted (~30 sec). Once it does:

	 $ ./scopy.sh

***------------------- TROUBLESHOOTING -------------------***

Using my scopy.sh script will put errors and warnings from Scopy in error-log.txt for reference. Warning are
not errors (usually). To read them, navigate to its directory and type:
	$ cat error-log.txt

**Error 1: Qt: Session management error: None of the authentication protocols specified are supported**

Make sure that you have unset the SESSION_MANAGER variable. To be absolutely certain, run:
	  $ export -p
if SESSION_MANAGER shows up, then
	  $ export -n SESSION_MANAGER

 If that still does not solve the problem (like for me) then you should beware of cached data. You will
 have to visit ~/.cache/ and look for scopy (mine was in the Applications folder)

	  $ cd ~/.cache
	  $ find . -name *[sS]copy* -print # if this command finds it, delete the folder it is in

For me it was in ~/.cache/Applications:
	  
	  /home/monsterma/.cache:
	  ├── Applications
	  │   ├── org.adi.Scopy

	  $ rm -rf ~/.cache/Applications # Make sure to get the directory right!!

**Error 2: Scopy opens, buttons work, device is recognized, but calibration fails:**

 For me this only happened when I had the ADALM2000 plugged in for too long and
 it heated up beyond its acceptable operating temperature. Unplug and let cool
 down. Otherwise, read StackOverflow for hints.

**Error 3: If there is an error about USB connectivity:**
	  Make sure that you have not opened another terminal that is idly running (or trying to run)
	  Scopy, or the USB I/O will fail and Scopy hangs.

  If that does not solve it:
	  $ sudo yum upgrade libusb

Error 4: Scopy will not recognize ADALM2000 though it is mounted
	  This happened for me too. Instead of bushwhacking, I took the easy way
	  out. I read the file config.txt on the ADALM2000 and use the IP address: 192.168.**.*
	  for the ipadr_host field. It should succeed and then I hit the connect button.


For reference, my installation folder looks like this:
/home/monsterma/Software/scopy/ # I installed the m2k drivers in another folder
 ├── error-log.txt
 ├── scopy.sh
 ├── scopy-v1.2.0-Linux.flatpak
 └── scopy-v1.2.0-Linux-flatpak.zip


