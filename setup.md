# Building a Namecoin #Fullnode

### You will need:
1. 16Gb Micro SD Card or Larger (preferably Class 10 or above with wear protection)
2. Raspian Jessie https://www.raspberrypi.org/downloads/raspbian / or your favourite distro

Write the Jessie .img file to the SD Card using your preferred method.

### Then boot for the first time and at the command line:
```
sudo raspi-config
```
Select:  
* [1] Expand File System - Click OK
* [2] Change the *pi* users password to something secure
* [3] Boot Options - Set to preferred boot mode: command line or desktop as you wish.
* [4] Internationalisation Options - Set locale, timezone and keyboard as appropriate.
* [5] Overclock - Set to Pi 2 1000Mhz
* [6] Advanced Options - Change hostname to something appropriate e.g. "nmcpi" (without quotes)
* [7] Advanced Options - Memory Split - We suggest *16* for headless/server or *128* for gui/desktop.
* [8] Advanced Options - Ensure SSH is enabled.

#### Reboot either via the config or manually like so:
```
sudo reboot
```
#### Installing Updates

Now the Raspeberry Pi is fully configured we need to ensure it is upto date, again in the command line:
```
sudo apt-get update
```  
```
sudo apt-get upgrade -y
```  
The -y flag tells the OS to answer "yes" to any prompts warning you of extra disk space required, we use this for convenience but it is not essential.  
```
sudo reboot
```

#### Install the dependencies for Namecoin
```
sudo apt-get install build-essential autoconf libtool autotools-dev automake pkg-config libssl-dev libevent-dev bsdmainutils libboost-all-dev libminiupnpc-dev -y

```

#### Install the dependencies for the Graphical User Interface (GUI) QT Wallet
```
sudo apt-get install libqt4-dev qt4-dev-tools libprotobuf-dev protobuf-compiler libqrencode-dev -y
```

#### Create a folder where we will work with installation the files
```
mkdir ~/bin
```  
```
cd ~/bin
```

#### Download, Unpack, Configure and Install the Berkeley DB 4.8
```
wget http://download.oracle.com/berkeley-db/db-4.8.30.NC.tar.gz
```  
```
tar -xzvf db-4.8.30.NC.tar.gz
```  
```
cd db-4.8.30.NC/build_unix/
```  
```
../dist/configure --enable-cxx
```  
##### the -j4 flag uses all four cores on the Raspberry Pi for maximum speed.  
```
make -j4
```  
```
sudo make install
```  

#### Installing Namecoin-core
```
cd ~/bin
```   
```
git clone https://github.com/namecoin/namecoin-core.git
```  
```
cd namecoin-core/
```  
```
./autogen
```  
```
./configure CPPFLAGS="-I/usr/local/BerkeleyDB.4.8/include -O2" LDFLAGS="-L/usr/local/BerkeleyDB.4.8/lib" --with-gui=qt4
```
##### For this make operation we only use 2 of the cores to prevent errors
```
make -j2
``` 
```
sudo make install
```  
Delete the ~/bin folder and all subfolders after the compiling and installing.  Assuming we are still in the ```namecoin-core``` folder
```
cd ..
cd ..
sudo rm -R ~/bin
```  
Now that Namecoin is installed create a .namecoin folder inside of your home directory so we can ensure that when run it is configured as we would like it to be, with a full index of transactions and name history:  
```
mkdir /home/pi/.namecoin/
```   
```
cd /home/pi/.namecoin
```  
Open a blank text editor.
```
nano namecoin.conf
```  
Enter the following text as a minimum:
```
listen=1
server=1
daemon=1
txindex=1
namehistory=1
testnet=0
dbcache=50
rpcuser=namecoinrpcuser
rpcpassword=changme_and_make_me_secure
rpcallowip=127.0.0.1
rpcport=8335
```
Press ```ctrl+X``` followed by ```Y``` then ```Enter``` to save changes and return back to the command line.

#### Starting Namecoin

To start namecoin use either ```namecoind &``` or from within a terminal from the desktop (for the GUI) ```namecoin-qt &``` as appropriate.

If you would like namecoind to start automatically at boot update your startup configuration as follows:
```
sudo nano /etc/rc.local
```
and then above the last line ```exit 0 ``` place the following line:
```
su pi -c 'namecoind &'
```

#### Reboot the system 
```
sudo reboot
```
##### Expect to wait a while for the namecoin blockchain to fully sync ...

### And that's all there is to it. 

You now have your Namecoin #Fullnode but keep reading as we have some additional tricks and tweaks below

To view the status of your node use the following command
```
namecoin-cli getblockchaininfo
```

### Installing Other Apps

We are going to install some other apps that are useful now that we have access to the Namecoin blockchain. Firstly we need a few Python related files and then we can install *NMControl* and *Bitmessage*

```
sudo apt-get install python-qt4
```
```
sudo apt-get install python-pip
```
```
sudo pip install bottle
```

#### NMControl gives you easy access to the .bit domain name system

```
git clone https://github.com/namecoin/nmcontrol/
```
Then we must make a configuration file as follows:

```
cd nmcontrol
```
```
cd conf
```
Open a blank text editor.
```
nano service-dns.conf
```
Use the following text as a template NOTE it must include the , at the end but you are free to modify as required:
```
; service-dns.conf example

[dns]
; Launch at startup
start=1

; Listen on ip
host=127.0.0.1

; Disable lookups for standard domains
disable_standard_lookups=0

; Listen on port
port=53

; Forward standard requests to your standard DNS
; There has to be a comma at the end!
; e.g. lokal router ip: resolver=192.168.0.1,
; e.g. Google DNS: resolver=8.8.8.8, 8.8.4.4,
resolver=192.168.0.1,
```

##### To start NMControl from within the /nmcontrol directory in the command line use: 

```
sudo python ./nmcontrol.py
```
alternatively start in debug mode:
```
sudo python nmcontrol.py --daemon=0 --debug=1 start
```

#### Bitmessage.  Bitmessage can be configured to use NMControl.
```
git clone https://github.com/Bitmessage/PyBitmessage ~/PyBitmessage
```
To launch Bitmessage, From a terminal on the desktop:
```  
python ~/PyBitmessage/src/bitmessagemain.py &
```  

#### Browser and Email client
```
sudo apt-get install iceweasel
```  
```
sudo apt-get install icedove
```  
Iceweael and Icedove have launch icons in the menu within GUI. 

### Additional Configuration Tweaks

Some additional tweaks we have found useful are

#### To prevent terminals from blanking the following can be done:
```  
sudo nano /etc/kbd/config
```  
Locate these options and change to these values:
```  
BLANK_TIME=0
POWERDOWN_TIME=0
```  
Press ```ctrl+X``` followed by ```Y``` then ```Enter``` to save changes and return back to the command line.

#### The size of the swap file can easily be modified by using:
```  
sudo nano /etc/dphys-swapfile
```  
and then editing the value e.g. 
```  
CONF_SWAPSIZE=1024
```  
Press ```ctrl+X``` followed by ```Y``` then ```Enter``` to save changes and return back to the command line. Then run the following commands to initialse the changes
```  
sudo dphys-swapfile setup
sudo /etc/init.d/dphys-swapfile stop
sudo /etc/init.d/dphys-swapfile start
```  

#### Firewall

It is also advisable to add a firewall to your configuration.  Namecoin uses port 8334 by default and we have assigned port 8335 for RPC and Bitmessage uses port 8444.  So we install the firewall and configure to deny incoming by default and to then allow SSH but limit to prevent attacks:
```  
sudo apt-get install ufw
sudo ufw default deny incoming
sudo ufw allow ssh/tcp
sudo ufw limit ssh/tcp
```  
Then open the additional Namecoin and BitMessage ports 
```  
sudo ufw allow 8334/tcp
sudo ufw allow 8335/tcp
sudo ufw allow 8444/tcp
```  
Then open the DNS and NMControl Ports
```  
sudo ufw allow 53/tcp
sudo ufw allow 9000/tcp
```  
Finally enable the firewall and reboot
```  
sudo ufw enable
sudo reboot
```  

## Enjoy your Freedom!
