# script-server
Script-server provides Web GUI for your scripts and remote execution facility. All you need to do, is create link/configuration to your scripts and start the server. Users will be able to access your server via web-browser and run your scripts. 
Everything will run on your machine, so users won't care about setting up the environment or working via ssh.
GUI is very straightforward any easy-to-use for anyone.

Example of the user interface:
![Example of user interface](screenshot.png?raw=true)

## Features
1. Sending parameters from a user to a script. Different parameter types are supported
2. Providing immediate output to the user and reading it's input (if script is interactive)
3. Possibility to enable LDAP authentication
4. Alerting in case of script execution failures (email or web hook)
5. HTTPS support
6. Transparent logging and auditing

## Setup and run
1. Clone/download the repository
2. Create configurations for your scripts in *conf/runners/* folder (see script config structure in the wiki)
3. Launch server using python3: python launcher.py

By default, server will run on 5000 port, over HTTP protocol

### Server config
You can configure ssl and port, using conf/conf.json file. This file should have correct json structure. All missing parameters will be replaced with defaults.
It is allowed not to create this file. In this case default values will be used.
See server config structure for details

#### SSL 
For working over HTTPS, server key and certificate should be provided and specified in server configuration.

## Requirements
### Server-side
Python 3.5+ with following modules:
* Tornado
* six

OS support:
- Linux (main). Tested and working on Debian 8
- Windows (additional). Light testing on Windows 7
- MacOS (additional). Not tested. Most probably some fixes are needed

### Client-side
Any more or less up to date browser with enabled JS

Internet connection is not needed. All the files are loaded from the server.

## Security
Completely no security! Use it only in local network for fully trusted users. 
### SSL
Server can work over SSL, for this server key and certificate should be provided.

## Logging
All web/operating logs are written to the _server-path_/logs/server.log
Additionally each script execution logs (output and error streams) are written to separate file in _server-path_/logs/processes. File name format is {script\_name}\_{client\_address}\_{date}\_{time}.log. 

## Testing/demo
Script-server has bundled configs/scripts for testing/demo purposes, which are located in testing folder. You can link/copy testing config files to server config files.