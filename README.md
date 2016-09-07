# transmission-deployer
Deploy multiple transmission-daemons in Debian based distros.  
Tested on Debian 8.5, should also work with Ubuntu.  

### Requirements
* Have a Debian based distro
* Have transmission-daemon installed 
* Have the default transmission-daemon unchanged and unused
 
### What it does
* Checks your current installation
* Creates a new user
* Creates download and incomplete directories with right permissiosn
* Adds the user to debian-transmission group
* Duplicates original transmission-daemon to a new one corresponding to the corresponding transmission-daemon-user
* Edits all required files
* Allows the process to be started and started upon boot
* Asks you for RPC info (port & password)
* Completes the install and gives you relevant connexion info

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
Available commands: deploy; clean  
Example 
````bash
./transmission-deployer deploy downloader
````

### Wishlist/Devlist
* Add quota management
* Be more versatile if user changed default transmission-daemon
* Add a check to know if default transmission-daemon is started or not (for now it will always close it before the process then restart it after the process)

# Sample output

## Deployment

````
root@terageek:~# ./transmission-deployer deploy downloader

Checking your configuration...

[ ok ] transmission-daemon installed
[ ok ] Folder /home found
[ ok ] File /usr/bin/transmission-daemon found
[ ok ] File /etc/init.d/transmission-daemon found
[ ok ] File /etc/default/transmission-daemon found
[ ok ] Folder /var/lib/transmission-daemon found
[ ok ] Folder /etc/transmission-daemon found

Transmission server deployment
########################################################
Welcome!

Please, take a moment to review your settings.
You're using this script as root. Be extra careful.

You choose to deploy transmission-daemon for downloader

Continue? [Y/n]y
Let's go!

Stopping transmission-daemon in order to continue the process
[ ok ] Stopping transmission-daemon (via systemctl): transmission-daemon.service.

Info! downloader doesn't exist
Home direcotry for this user set to dir: /home/downloader
Create new transmission server for user: downloader ?
Continue? [Y/n]y
Let's go!

Creating downloader
Default shell /bin/bash for downloader
Adding user downloader to group debian-transmission...
Info! It fixes permission issues but allows this user to access other files from the group and reciprocally
[ ok ] User created
Please, choose a password for downloader:
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
Creating user's directories...
/home/downloader/downloads && /home/downloader/incomplete
Applying permissions...

Duplicating transmission-daemon to transmission-daemon-downloader...
[ ok ] transmission-daemon-downloader duplicated

Info! Please, set the transmission RPC password
Warning! No confirmation, not hidden
RPC password:
ILoveUltimateByte
Please, set an RPC port (default 9091)
RPC port:
9092

Creating config file...
Symlinking config file
‘/var/lib/transmission-daemon-downloader/info/settings.json’ -> ‘/etc/transmission-daemon-downloader/settings.json’
Adjusting /etc/init.d/transmission-daemon-downloader to fit user...
[ ok ] New process now fits user!
Updating config location info
[ ok ] Config location info updated!

Allowing transmission-daemon-downloader to run on boot
insserv: warning: script is corrupt or invalid: /etc/init.d/../rc2.d/S15ssh_gen_host_keys
[ ok ] update-rc.d applied!
Starting back transmission-daemon
[ ok ] Starting transmission-daemon (via systemctl): transmission-daemon.service.

Transmission server Deployment
########################################################
Installation complete!
You can now try to run the daemon with:
/etc/init.d/transmission-daemon-downloader start

Info:
Username: downloader
RPC Port: 9092
RPC Password: ILoveUltimateByte
RPC URL: /transmission

Credits: UltimateByte


root@terageek:~# /etc/init.d/transmission-daemon-downloader start
[ ok ] Starting transmission-daemon-downloader (via systemctl): transmission-daemon-downloader.service.
````

## Cleaning

````
root@terageek:~# ./transmission-deployer clean downloader

Checking your configuration...

[ ok ] transmission-daemon installed
[ ok ] Folder /home found
[ ok ] File /usr/bin/transmission-daemon found
[ ok ] File /etc/init.d/transmission-daemon found
[ ok ] File /etc/default/transmission-daemon found
[ ok ] Folder /var/lib/transmission-daemon found
[ ok ] Folder /etc/transmission-daemon found

Transmission server deployment
########################################################
Welcome!

Please, take a moment to review your settings.
You're using this script as root. Be extra careful.

You choose to clean transmission-daemon for downloader

Continue? [Y/n]y
Let's go!

Stopping transmission-daemon in order to continue the process
[ ok ] Stopping transmission-daemon (via systemctl): transmission-daemon.service.
Stopping transmission-daemon-downloader in order to continue the process
[ ok ] Stopping transmission-daemon-downloader (via systemctl): transmission-daemon-downloader.service.

[WARNING] transmission-daemon-downloader is already installed!
Do you wish to autoclean the old transmission from downloader ?
Info! This will remove any torrent and transmission settings.
Continue? [y/n]y
Cleaning!

Removing transmission-daemon-downloader files
Removing transmission-daemon-downloader from rc.d
insserv: warning: script is corrupt or invalid: /etc/init.d/../rc2.d/S15ssh_gen_host_keys
[ ok ] Previous installation cleaned.

Info! downloader already exists
Do you wish to remove the user and its home directory ?
[WARNING] Any file in /home/downloader will be lost!
Continue? [y/n]y
Let's go!

Closing downloader processes
Info! Press ctrl + c to abort !
[ ok ] Closed processes
Removing user: downloader and its home: /home/downloader
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
