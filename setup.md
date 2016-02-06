# Building a Namecoin #Fullnode

#### You will need:
1. 16Gb Micro SD Card or Larger (preferably Class 10 or above with wear protection)
2. Raspian Jessie https://www.raspberrypi.org/downloads/raspbian / or your favourite distro

If this is not your first time then:
```
sudo raspi-config
```
Select:  
1. Expand File System - click OK
2. Configure your internationalisation options
3. Ensure SSH is Enabled
4. Change hostname to eg "nmcpi" (without quotes)
5. Reboot either via the config 

If you want you can reboot manually like so:
```
sudo reboot
```
#### Installing Updates

```
sudo apt-get update
```  
```
sudo apt-get upgrade -y
```  
The -y flag tells the OS to answer "yes" to any prompts warning you of extra disk space required, we use this for convenience but it is not essential.  


#### Install the dependencies
```
sudo apt-get install build-essential autoconf libtool autotools-dev automake pkg-config libssl-dev libevent-dev bsdmainutils libboost-all-dev libminiupnpc-dev -y

```

#### Dependencies for Graphical User Interface (GUI) QT Wallet
```
sudo apt-get install libqt4-dev qt4-dev-tools libprotobuf-dev protobuf-compiler libqrencode-dev -y
```

#### Create a folder where we will install the files
```
mkdir ~/bin
```  
```
cd ~/bin
```

#### Install the Berkeley DB 4.8
```
wget http://download.oracle.com/berkeley-db/db-4.8.30.NC.tar.gz
```  
```
tar -xzvf db-4.8.30.NC.tar.gz
```  
```
cd db-4.8.30.NC/build_unix/
```  

#### Configuring the system and installing Berkeley DB  
#### the -j4 flag installs using all four cores on the Raspberry Pi  
```
../dist/configure --enable-cxx
```  
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

```
make -j2
``` 
```
sudo make install
```  

You can delete ~/bin folder after the compiling.  
Once Namecoin is installed you will need to create a .namecoin folder inside of your home directory. This is really on necessary if you want to use Namecoin Command Line Interface or run it headlessly. If you do simply enter:  
```
mkdir /home/pi/.namecoin/
```   
```
cd /home/pi/.namecoin
```  
```
nano namecoin.conf
```  
This will open a blank text editor. Enter the following text as a minimum:
```
listen=1
server=1
daemon=1
txindex=1
testnet=0
dbcache=50
rpcuser=rpcuser
rpcpassword=changme_and_make_me_secure
rpcallowip=127.0.0.1
```
Press ```cntr+X``` followed by ```Y``` then ```Enter``` to save changes and return back to the command line.

## Starting Namecoin

To start namecoin use either ```namecoind &``` or from within a terminal from the desktop for the GUI ```namecoin-qt &``` as appropriate.


## Installing the other apps

We are going to install some other apps that are useful. Firstly we need a Python update and then we can install Bitmessage, plus a fully featured browser Iceweasel and email client Icedove.

```
sudo apt-get install python-qt4
```
```
sudo apt-get install python-qt4
```
```
git clone https://github.com/Bitmessage/PyBitmessage ~/PyBitmessage
```
```
sudo apt-get install iceweasel
```  
```
sudo apt-get install icedove
```  

Iceweael and Icedove will have icons in the GUI, to launch Bitmessage (in a terminal from the desktop)
```  
python ~/PyBitmessage/src/bitmessagemain.py &
```  

## And that's all there is to it. 

You now have your Namecoin #Fullnode. 

## Enjoy your Freedom!
