## Upgrading script-server version on Linux
Here is a script that is supposed to upgrade painlessly your script-sever install.  
(tested on Oracle Linux 6 and 7)

### Usage
- Save the script in your system
- Configure hard-coded variables
- Execute it as root
```
$ sudo upgrade.sh 1.5.2
```

### Requirements:
- Correctly set up script-server as a service on your Linux server. See [Running as a Linux service](https://github.com/bugy/script-server/wiki/Running-as-a-linux-service)
- The service must be running when calling the script
- `unzip` and `wget` commands

### Warning: 
- This will move all your content in the installation folder to a backup directory named as the script-server install with datetime string like `%Y-%m-%dT%H-%M-%S` 
- Replace all files contained in the install directory 
- If you're using default `conf` directory, your configs files will be moved to the backup directory too.

### You must configure manually some variables
- `scriptServerService`: The name of the Script-Server service
- `scriptServerUser`: The name of the user who run the service
- `scriptServerInstall`: Folder of the source files.

##### `upgrade.sh`

```bash
#!/bin/bash
#
# Script-Server upgrade script
#
# Usage: upgrade.sh <Version>
# Usage: sudo upgrade.sh <Version>
# Usage: echo "myPAssw0rd" | sudo -S upgrade.sh <Version>
# Usage: echo "$SUDO_PASSWD" | sudo -S upgrade.sh <Version>
#
# Setting bash strict mode. See http://redsymbol.net/articles/unofficial-bash-strict-mode/
# Script will stop if error occurs
set -euo pipefail
# 
# scriptServerService: The name of the Script-Server service
scriptServerService="script-server"
# 
# scriptServerUser: The name of the user who run the service
scriptServerUser="script-server"
# 
# scriptServerInstall: Folder of the source files.
scriptServerInstall="/home/script-server/script-server"
#
#
datestr=`date +"%Y-%m-%dT%H-%M-%S"`
#
#
# Upgrade begin
cd /tmp/
echo "Backing up sources to ${scriptServerInstall}-${datestr}"
cp -r ${scriptServerInstall} ${scriptServerInstall}-${datestr}
echo "Cleaning temp files"
rm -rf script-server 2>/dev/null || true
rm -f script-server.zip 2>/dev/null || true
echo "Downloading new script-server sources"
wget https://github.com/bugy/script-server/releases/download/$1/script-server.zip
echo "Exracting..."
mkdir script-server
unzip ./script-server.zip -d ./script-server
echo "Service status"
service ${scriptServerService} status
echo "Stopping service"
service ${scriptServerService} stop
echo "Removing current sources."
rm -rf "${scriptServerInstall}/.*"
echo "Moving new source files"
cp -r ./script-server/* ${scriptServerInstall}
echo "Changing ownership of source files"
chown ${scriptServerUser}:${scriptServerUser} ${scriptServerInstall}
echo "Starting service"
service ${scriptServerService} start
echo "Service status"
service ${scriptServerService} status
```
