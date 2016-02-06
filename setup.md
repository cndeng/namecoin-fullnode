# Installing Namecoin #Fullnode

#### You will need:
1. 8Gb Micro SD Card (preferably Class 10 or above with wear protection)
2. Download Raspian Jessie https://www.raspberrypi.org/downloads/raspbian / or your favourite distro

#### Insert Micro SD Card & Open up Terminal/Putty on Windows

#### Follow the bitcoin fullnode [https://github.com/MrChrisJ/fullnode] setup instruction until the "Install the dependencies".  

## We need some extra dependancies for namecoin

#### Instal the dependencies
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
cd bitcoin/
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
testnet=0
dbcache=50
rpcuser=rpcuser
rpcpassword=changme_and_make_me_secure
```
Press ```cntr+X``` followed by ```Y``` then ```Enter``` to save changes and return back to the command line.

## Installing the other apps

```
sudo apt-get install iceweasel
```  
```
sudo apt-get install tor
```  
```
sudo apt-get install openvpn resolvconf
```  

#### To install IPFS go to [https://ipfs.io/docs/install/](https://ipfs.io/docs/install/)
Download the Linux ARM version [https://gobuilder.me/get/github.com/ipfs/go-ipfs/cmd/ipfs/ipfs_master_linux-arm.zip](https://gobuilder.me/get/github.com/ipfs/go-ipfs/cmd/ipfs/ipfs_master_linux-arm.zip)
#### Then in terminal...
```
cd ipfs
```  
```
sudo mv ipfs /usr/local/bin/ipfs
```

## And that's all there is to it. You now have your Namecoin #Fullnode. Enjoy your Freedom!
