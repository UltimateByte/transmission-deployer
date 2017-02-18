# transmission-deployer
Deploy and manage multiple transmission-daemons in Debian based distros.  
Tested on Debian 8.7, should also work with Ubuntu.  

The default directory structure is:  
````bash
/home/seedboxes/username/
/home/seedboxes/username/downloads
/home/seedboxes/username/downloads/.htacces
/home/seedboxes/username/incomplete
/etc/transmission-daemon-username
/var/lib/transmission-daemon-username
````
### Requirements
* Have a Debian based distro
* Have transmission-daemon installed 
* Have the default transmission-daemon unchanged and unused
 
### What it does
* Checks your current installation
* Creates a new user
* Asks for RPC info (port & password)
* Creates download and incomplete directories with right permissions
* Adds the user to debian-transmission group
* Duplicates original transmission-daemon to a new one corresponding to the corresponding transmission-daemon-user
* Edits all required files
* Allows the process to be started and started upon boot
* Creates a .htaccess file in downloads dir if you plan to use userdir apache2 mod
* Completes the install and gives you relevant connexion info
* You can display information about an already installed transmission instance

## Usage  
#### Requires elevated privileges

Download the script  
````bash
wget https://raw.githubusercontent.com/UltimateByte/transmission-deployer/master/transmission-deployer
````
Make it executable  
````bash
chmod +x transmsion-deployer
````
Edit home directory and default user shell if needed
````bash
nano transmsion-deployer
````
Run the script
````bash
./transmission-deployer [command] [username]
````
Available commands:  
* deploy [username]
* clean [username]
* details [username]
* details all
Example 
````bash
./transmission-deployer deploy downloader
````

### Wishlist/Devlist
* Be more versatile if user changed default transmission-daemon
* Add a check to know if default transmission-daemon is started or not (for now it will always close it before the process then restart it after the process)
* Check if ports are in use
* Add apaxy file browser for userdir
* Update transmission server
* Add quota management

# Sample output

## Deployment

````
root@webserver:~/deployscripts# ./transmission-deployer deploy downloader

Checking your configuration...

[ ok ] transmission-daemon installed
[ ok ] Directory /home found
[ ok ] File /usr/bin/transmission-daemon found
[ ok ] File /etc/init.d/transmission-daemon found
[ ok ] File /etc/default/transmission-daemon found
[ ok ] Directory /var/lib/transmission-daemon found
[ ok ] Directory /etc/transmission-daemon found
Continue? [Y/n]y
Let's go!


Transmission server deployment
########################################################
Welcome!

Please, take a moment to review your settings.
You're using this script as root. Be extra careful.

You chose to deploy transmission-daemon for downloader

Stopping transmission-daemon in order to continue the process
[ ok ] Stopping transmission-daemon (via systemctl): transmission-daemon.service.

Info! downloader doesn't exist
Home direcotry for this user set to dir: /home/seedboxes/downloader
Create new transmission server for user: downloader ?
Continue? [Y/n]y
Let's go!

Creating downloader....
Default shell /bin/bash for downloader
Adding user downloader to group debian-transmission...
Info! It fixes permission issues but allows this user to access other files from the group and reciprocally
[ ok ] User created
Please, choose a password for downloader:
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
Creating user's directories...
/home/seedboxes/downloader/downloads && /home/seedboxes/downloader/incomplete
[ ok ] Directories created!

Applying permissions...
[ ok ] Proper permissions applied!

Duplicating transmission-daemon to transmission-daemon-downloader...
[ ok ] transmission-daemon-downloader duplicated!

Info! Please, set the transmission RPC password
Warning! No confirmation, not hidden
RPC password:
lol
Please, set an RPC port (default 9091)
RPC port:
9098

Creating config file...
Symlinking config file
‘/var/lib/transmission-daemon-downloader/info/settings.json’ -> ‘/etc/transmission-daemon-downloader/settings.json’
Adjusting /etc/init.d/transmission-daemon-downloader to fit user...
[ ok ] New process now fits user!
Updating config location info
[ ok ] Config location info updated!

Allowing transmission-daemon-downloader to run on boot
[ ok ] update-rc.d applied!
Creating .htaccess
[ ok ] .htaccess created!

Starting back transmission-daemon
[ ok ] Starting transmission-daemon (via systemctl): transmission-daemon.service.

Transmission server Deployment
########################################################
Installation complete!
You can now try to run the daemon with:
/etc/init.d/transmission-daemon-downloader start

Info:
Username: downloader
RPC Port: 9098
RPC Password: lol
RPC URL: /transmission

Credits: UltimateByte
root@webserver:~/deployscripts# ./transmission-deployer details downloader
#################################
######## Seedbox details ########
#################################
########
User: downloader
RPC Port: 9098
Disk Usage: 28K
Downloads count: 2
Incomplete count: 0
In Transmission: 0

root@webserver:~/deployscripts# ./transmission-deployer details all
#################################
######## Seedbox details ########
#################################
########
User: noobettz
RPC Port: 9097
Disk Usage: 28K
Downloads count: 1
Incomplete count: 0
In Transmission: 0


########
User: gibson45
RPC Port: 9095
Disk Usage: 197G
Downloads count: 31
Incomplete count: 0
In Transmission: 31


########
User: dreamgeek
RPC Port: 9093
Disk Usage: 19G
Downloads count: 11
Incomplete count: 0
In Transmission: 10


########
User: lutra
RPC Port: 9096
Disk Usage: 179G
Downloads count: 32
Incomplete count: 1
In Transmission: 25


########
User: downloader
RPC Port: 9098
Disk Usage: 28K
Downloads count: 2
Incomplete count: 0
In Transmission: 0


########
User: ultisb
RPC Port: 9094
Disk Usage: 328G
Downloads count: 77
Incomplete count: 0
In Transmission: 72


########
User: aatys
RPC Port: 9092
Disk Usage: 982G
Downloads count: 1
Incomplete count: 7
In Transmission: 6


root@webserver:~/deployscripts# ./transmission-deployer clean downloader

Checking your configuration...

[ ok ] transmission-daemon installed
[ ok ] Directory /home found
[ ok ] File /usr/bin/transmission-daemon found
[ ok ] File /etc/init.d/transmission-daemon found
[ ok ] File /etc/default/transmission-daemon found
[ ok ] Directory /var/lib/transmission-daemon found
[ ok ] Directory /etc/transmission-daemon found
Continue? [Y/n]y
Let's go!


Transmission server deployment
########################################################
Welcome!

Please, take a moment to review your settings.
You're using this script as root. Be extra careful.

You chose to clean transmission-daemon for downloader

Stopping transmission-daemon in order to continue the process
[ ok ] Stopping transmission-daemon (via systemctl): transmission-daemon.service.
Stopping transmission-daemon-downloader in order to continue the process
[ ok ] Stopping transmission-daemon-downloader (via systemctl): transmission-daemon-downloader.service.

[WARNING] transmission-daemon-downloader is already installed!
Do you wish to autoclean the old transmission from downloader ?
Info! This will remove any torrent and transmission settings.
Continue? [y/n]y
Cleaning!

Removing user downloader from group debian-transmission...
Removing user `downloader' from group `debian-transmission' ...
Done.
[ ok ] User is not a part of debian-transmission anymore!

Removing transmission-daemon-downloader files...
[ ok ] files removed!
Removing transmission-daemon-downloader from rc.d...
[ ok ] Process cleared from rc.d!

[ ok ] Previous installation cleaned!

Info! downloader already exists
Do you wish to remove the user and its home directory ?
[WARNING] Any file in /home/seedboxes/downloader will be lost!
Continue? [y/n]y
Let's go!

Closing downloader processes
Info! Press ctrl + c to abort !
[ ok ] Closed processes

Removing user: downloader and its home: /home/seedboxes/downloader
Info! Press ctrl + c to abort !
5
4
3
2
1
removing downloader
userdel: downloader mail spool (/var/mail/downloader) not found
[ ok ] Removed previous downloader and its home directory
Starting back transmission-daemon
[ ok ] Starting transmission-daemon (via systemctl): transmission-daemon.service.

Transmission server cleaning
########################################################
Cleaning complete!

Credits: UltimateByte
````
