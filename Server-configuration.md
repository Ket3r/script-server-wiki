Script-server has default settings and works out of the box. For changing the settings, please edit _conf/conf.json_ file (create new if needed).  
Please note, that configuration changes require server restart.  

[Example](#example) can be found at the bottom of the page.  

Full list of configurable properties:
* [port](#port)
* [address](#address)
* [title](#title)
* [ssl](#ssl)
  * [key_path](#--key_path)
  * [cert_path](#--cert_path)
* [auth](#auth)
  * [type](#--type)
  * [url](#--url) 
  * [username_pattern](#--username_pattern) 
  * [version](#--version) 
  * [base_dn](#--base_dn) 
  * [client_id](#--client_id) 
  * [secret](#--secret) 
* [access](#access)
  * [allowed users](#--allowed-users)
  * [admin_users](#--admin_users)
  * [groups](#--groups)
  * [trusted_ips](#--trusted_ips)
* [alerts](#alerts)
  * [destinations](#--destinations)
    * [type](#----type)
    * [from](#----from)
    * [to](#----to)
    * [server](#----server)
    * [auth_enabled](#----auth_enabled)
    * [login](#----login)
    * [password](#----password)
    * [tls](#----tls)
    * [url](#----url)
* [logging](#logging)
  * [execution_file](#--execution_file)
  * [execution_date_format](#--execution_date_format)

---

### `port`
Listening port of the server.  

_Default_: 5000 (5443 for ssl)  
_Type_: number  


### `address`
Listening address of the server. Can be used to allow connections only from a specific network  

_Default_: 0.0.0.0 (any connection)  
_Type_: string (ip)  


### `title`
Custom web page title shown to a user  

_Default_: Script Server  
_Type_: string  


## `ssl`
SSL configuration, use it for enabling communication over SSL  
This would require additional private key and certificate configuration (see below)  

_Default_: ssl disabled  
_Type_: json object  

### - `key_path`
The path to a private key file. Can be relative to script-server location or an absolute path  

_Required_: yes  
_Type_: string  

### - `cert_path`
The path to a public certificate file. Can be relative to script-server location or an absolute path  

_Required_: yes  
_Type_: string  


## `auth`
Enables user authentication. This configuration depends on the auth provider, specified by `type` property
Auth providers require different settings  

For detailed information on how authentication works and affects the 
script server, please read [[Authentication|Authentication]] and 
[[Authorization|Authorization]]  

_Type_: json object

### - `type`
Type of authentication provider. Supported types: 
* ldap (requires python `ldap3` module)  
* google_oauth  

For each provider description read corresponding sections on [[Authentication|Authentication]]

_Required_: yes  
_Type_: string  


### - `url`
(LDAP)  
URL of the LDAP server  

_Required_: yes  
_Type_: string  
_Example_: `"url": "ldap://192.168.100.3",`  


### - `username_pattern`
(LDAP)  
Pattern for username, which is sent to the LDAP server, e.g. 
`uid=$username,ou=people,dc=test,dc=ldap"`. _$username_ is filled with 
the username, specified by user on login screen  
This pattern can be useful, when user _unique_ name in LDAP is more 
verbose than username.  

_Required_: no  
_Default_: "$username"  
_Type_: string  
_Example1_: `"username_pattern": "uid=$username,ou=people,dc=buggy,dc=net"`  
_Example2_: `"username_pattern": "$username@buggy.net"`  
_Example3_: `"username_pattern": "buggy\\$username"`  

### - `version`
(LDAP)  
LDAP protocol version. Supported versions:
* 2
* 3  

_Default_: 3  
_Required_: no  
_Type_: number  


### - `base_dn`
(LDAP)  
Base DN for resolving user attributes and groups. Required for getting user groups from LDAP.  
`base_dn` should have format: "dc=name1,dc=name2,...dc=nameN"  

`base_dn` can be resolved automatically from the following `username_pattern`s:
* uid=$username,ou=people,dc=domain1,dc=domain2,dc=domainN
* username@domain1.domain2.domainN  

_Required_: no  
_Default_: resolved from `username_pattern`  
_Type_: string  
_Example_: `"base_dn": "dc=buggy,dc=net"`  


### - `client_id`
(Google OAuth)  
Client ID, provided by Google  

_Required_: yes  
_Type_: string  
_Example_: `"client_id": "12345678.apps.googleusercontent.com"`  

### - `secret`
(Google OAuth)  
Secret, provided by Google  

_Required_: yes  
_Type_: string  
_Example_: `"secret": "AbCdEfgHI"`  


## `access`
Access configuration section, can be used to completely prohibit an access or to limit it. 
Allows to configure access groups and trusted users. 
Users should be specified either by:
* their login name (if auth is enabled)
* or ip (if auth is disabled and `trusted_ips` is enabled)  

Any other names won't have affect due to security reasons.  
For detailed description , please see [[Authorization|Authorization]]  

_Required_: no  
_Type_: json array  

### - `allowed_users`
List of allowed usernames, who can access the script-server. Single asterisk (*) stays for any user.
By default any user is allowed  
Important note: users from `groups` and `admin_users` are always allowed. Thus there is no need to specify them explicitly.

_Default_: &ast;  
_Required_: no (yes for _google_auth_)  
_Type_: json array  (or &ast; string)  
_Example1_: `"allowed_users": ["bob", "john", "me@domain.com"]`  
_Example2_: `"allowed_users": "*"]`  

### - `admin_users`
List of users, who can access admin page (admin.html)  

_Default_ (without auth): ["127.0.0.1", "::1"]  
_Default_ (with auth): []  
_Required_: no
_Type_: json array  

### - `groups`
List of groups with the information, which users belong to each group. Users should be specified by username.
There is an additional explicit group _admin_users_, which is created from `admin_users` field. If this group is created explicitly, then `admin_users` property doesn't create an own group.  
Groups can be references with @groupname syntax. Groups can even reference other groups  

_Type_:  json dict  
_Default_: {}  
_Required_: no  
_Example1_: 
```
{ "groups": {
	"dev": ["127.0.0.1"],
	"support": ["192.168.2.15", "192.168.2.15"]
}}  
```
_Example2_: 
```
{ "groups": {
	"dev": ["buggygm_gmail.com"],
	"all": ["@dev", "@admin_users"]
}}
```  

### - `trusted_ips`
This option allows to specify which IPs can be trusted. It has 2 effects:  
**On reverse proxies**  
All the requests, coming from reverse proxy has the same IP, so in audit information of the user, the IP of the proxy will be shown. To prevent it, you can put proxy's IP in the `trusted_ips` list, so the user IP will be resolved based on HTTP Headers from the proxy  
**On identification without `auth`**  
If `auth` is disabled, IP can used to identify the users. But since IP is not very reliable, trusted IPs should be specified explicitly. If IP is not trusted, then each user would get a temporary identification token, which is harder to user in configuration (e.g. in `admin_users` or `groups`). More details can be found at [[Authorization|Authorization]]  

_Type_: json array  
_Default_: []  
_Required_: no  
_Example_: `"trusted_ips": ["192.168.0.15", "172.0.0.10"]`  

## `alerts`
Configuration section for alert notifications in case of script execution failure.  
Notifications contain script name, username, exit code and an attached log file.  

_Type_: json object  
_Required_: no  

### - `destinations`
List of destinations, where an alert should be send to. Multiple destinations can be configured at once, even for the same `type`.  

_Type_: json array
_Default_: []

### --- `type`
Type of destination. Supported values:
*email*  
(requires smtplib module)  
Alert is send as an email. Execution log file will be attached as a file  
*http*  
(requires requests module)  
Alert message is sent to an HTTP server via POST request. The alert message will be send in the request body, under _message_ name.  
Execution log file will be attached as a multipart file with "log" name.  

_Type_: string  
_Required_: yes  

### --- `from`
(email)  
Sender email address.  

_Required_: yes  
_Type_: string  

### --- `to`
(email)  
Semicolon-separated list of recipients' email addresses  

_Required_: yes  
_Type_: string  

### --- `server`
(email)  
Server hostname/IP, which is used to send an email  

_Required_: yes  
_Type_: string  

### --- `auth_enabled`
(email)  
Boolean flag specifying if authentication is required to send an email.  

_Default_: false (true, if login or password are set)  
_Required_: no  
_Type_: boolean  

### --- `login`
(email)  
Username for authentication in the `server`  

_Default_: `from` field  
_Required_: no  
_Type_: string  

###  --- `password`
(email)
Password for authentication in the `server`  
Environment variables can be used, prefixed by $$  

_Required_: no  
_Default_: -  
_Type_: string  
_Example1_: `"password": "$$EMAIL_PWD"`  

### --- `tls`
(email)
Specifies, whether server requires TLS layer  

_Default_: false (true for gmail server)  
_Required_: no  
_Type_: boolean  

### --- `url`
(http)
A URL where alert notification will be sent to.  

_Required_: yes  
_Type_: string  
_Example_: `"url": "localhost:5000/test_alerts"`  


## `logging`
Section for logging configuration  

_Type_: json dict  
_Required_: no  

### - `execution_file`
Filename pattern for script execution logs.  
Each execution is always written to a separate file, so if the filename is not uniquie, it's prefixed with random value.  
The following variables can be used (via $varname or ${varname} syntax):
* ID - execution identificator
* USERNAME - authentication name of the user
* IP - IP of the user
* HOSTNAME - hostname of the user
* DATE - current date and time (in `execution_date_format` format)
* AUDIT_NAME - audit name of the user
* SCRIPT - script name  
.log suffix is not required and can added automatically  

_Default_: "${SCRIPT}_${AUDIT_NAME}_${DATE}.log"  
_Required_: no  
_Type_: string  
_Example1_: `"execution_file": "$ID.log"`  
_Example2_: `"execution_file": "${IP}-${SCRIPT}"`  

### - `execution_date_format`
Date format to be used for `execution_file`.  
Should be a python format: https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior  

_Default_: "%y%m%d_%H%M%S"
_Required_: no
_Type_: string
_Example_: `"execution_date_format": "%y-%m-%d_%H-%M-%S"`

---

## Example
```javascript
{
  "port": 8080,
  "address": "192.168.0.1",
  "title": "My Script Server"
  "ssl": {
    "key_path": "testing/ssl/script-server.key",
    "cert_path": "testing/ssl/script-server.crt"
  },
  "auth": {
    "type": "ldap",
    "url": "ldap://127.0.0.1",
    "username_pattern": "uid=$username,ou=people,dc=test,dc=ldap",
    "version": 3,
    "base_dn": "dc=test,dc=ldap",
    "client_id": "12345678.apps.googleusercontent.com",
    "secret": "AbCdEfgHI"
  },
  "access": {
	"allowed_users": [ "myname", "my_friend", "jack", "joe", "jihn" ],
	"admin_users": [ "buggy@gmail.com", "myname" ],
	"groups": {
		"dev": ["jack", "joe", "jihn"],
		"support": ["my_friend"],
		"all": ["@admin_users", "@dev", "@support", "alice"]
	},
	"trusted_ips": [ "192.168.0.1", "192.168.0.2" ]
  }
  "admin_users": ["buggy@gmail.com", "somebody@gmail.com"],
  "alerts": {
    "destinations": [
      {
        "type": "email",
        "from": "buggygm@gmail.com",
        "to": "buggygm@gmail.com",
        "server": "smtp.gmail.com",
        "auth_enabled": true,
        "login": "buggygm@gmail.com",
        "password": "$$EMAIL_PWD",
        "tls": true,
        "url": "localhost:5000/test_alerts"
      }
    ]
  },
  "logging": {
    "execution_file": "$DATE-$ID.log",
    "execution_date_format": "%y-%m-%d_%H-%M"
  }
}
```