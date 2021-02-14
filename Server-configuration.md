Script-server has default settings and works out of the box. For changing the settings, please edit _conf/conf.json_ file (create new if needed).  
You can set custom conf.json location by running Script server with -f parameter (e.g. `launcher.py -f /home/me/configs/script-server.json`)  
Please note, that configuration changes require a server restart.  

[Example](#example) can be found at the bottom of the page.  

Full list of configurable properties:
* [port](#port)
* [address](#address)
* [title](#title)
* [max_request_size](#max_request_size)
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
  * [group_support](#--group_support) 
  * [group_search](#--group_search) 
  * [auth_info_ttl](#--auth_info_ttl) 
  * [session_expire_minutes](#--session_expire_minutes) 
  * [state_dump_file](#--state_dump_file) 
  * [htpasswd_path](#--htpasswd_path)
* [access](#access)
  * [allowed users](#--allowed_users)
  * [admin_users](#--admin_users)
  * [full_history](#--full_history)
  * [groups](#--groups)
  * [trusted_ips](#--trusted_ips)
  * [user_header_name](#--user_header_name)
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
Custom web page title is shown to a user  

_Default_: Script Server  
_Type_: string  

### `max_request_size`
Allows specifying max accepted file size (_in megabytes_) for upload.  
Use this parameter if your users upload huge files, or, to the contrary, when you want to allow uploading only small files (not to occupy the server storage)  

_Default_: 100  
_Type_: number

## `ssl`
SSL configuration, use it for enabling communication over SSL  
This would require an additional private key and certificate configuration (see below)  

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
* gitlab
* htpasswd

For each provider description read corresponding sections on [[Authentication|Authentication]]

_Required_: yes  
_Type_: string  


### - `url`
(LDAP, Gitlab)  
URL of the LDAP/Gitlab server  

_Required_: yes (for LDAP), no (for Gitlab)
_Type_: string  
_Example_: `"url": "ldap://192.168.100.3",`
_Default_:  https://gitlab.com (for Gitlab) 


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
(Google OAuth, Gitlab)  
Client ID in OAuth provider

_Required_: yes  
_Type_: string  
_Example_: `"client_id": "12345678.apps.googleusercontent.com"`  

### - `secret`
(Google OAuth, Gitlab)  
The secret in OAuth provider
Environment variables can be used, prefixed by $$  

_Required_: yes  
_Type_: string  
_Example_: `"secret": "AbCdEfgHI"`  

### - `group_support`
(Gitlab)  
Enabled loading user groups from Gitlab. This requires Gitlab 'api' scope  

_Required_: no  
_Type_: boolean  
_Default_: true  

### - `group_search`
(Gitlab)  
Optional filter for user groups, if there are too many groups for users

_Required_: no  
_Type_: boolean  
_Example_: `"group_search": "developers/"`  

### - `auth_info_ttl`
(Google OAuth, Gitlab)  
Time (in milliseconds) to cache user and group information before requested it from Gitlab server again, default 0 - cache forever and never check Gitlab again  

_Required_: no  
_Type_: integer  
_Default_: 0  

### - `session_expire_minutes`
(Google OAuth, Gitlab)  
User session expiration time. If a user stays inactive for more than the expiration time, he is logged out and has to authenticate again. By default, there is only server-wide expiration of 30 days.  

_Required_: no  
_Type_: integer  
_Default_: 0 (no expiration)  


### - `state_dump_file`
(Google OAuth, Gitlab)  
File path where to persist user/group information, default - only memory used (restarting app will logout all users).  

_Required_: no  
_Type_: string  


### - `htpasswd_path`
(.htpasswd)  
File path with stored .htpasswd credentials

_Required_: yes (for .htpasswd auth)
_Type_: string  

## `access`
Access configuration section can be used to completely prohibit access or to limit it. 
Allows configuring access groups and trusted users. 
Users should be specified either by:
* their login name (if auth is enabled)
* or IP (if auth is disabled and `trusted_ips` is enabled)  

Any other names won't have an effect due to security reasons.  
For detailed description , please see [[Authorization|Authorization]]  

_Required_: no  
_Type_: json array  

### - `allowed_users`
List of allowed usernames, who can access the script-server. A single asterisk (*) stays for any user.
By default, any user is allowed  
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


### - `full_history`
List of users, who can see the execution history of other users  
This should be a list of non-admin users (because `admin_users` can see full history anyway)

_Default_: []  
_Required_: no  
_Type_: json array  

### - `groups`
List of groups with the information, which users belong to each group. Users should be specified by username.
There is an additional explicit group _admin_users_, which is created from the `admin_users` field. If this group is created explicitly, then `admin_users` property doesn't create its own group.  
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
This option allows specifying which IPs can be trusted. It has 2 effects:  
**On reverse proxies**  
All the requests, coming from reverse proxy has the same IP, so in audit information of the user, the IP of the proxy will be shown. To prevent it, you can put proxy's IP in the `trusted_ips` list, so the user IP will be resolved based on HTTP Headers from the proxy  
**On identification without `auth`**  
If `auth` is disabled, IP can be used to identify the users. But since IP is not very reliable, trusted IPs should be specified explicitly. If IP is not trusted, then each user would get a temporary identification token, which is harder to use in configuration (e.g. in `admin_users` or `groups`). More details can be found at [[Authorization|Authorization]]  

_Type_: json array  
_Default_: []  
_Required_: no  
_Example_: `"trusted_ips": ["192.168.0.15", "172.0.0.10"]`  

### - `user_header_name`
(works only when auth is disabled)  
Allows identifying users by an HTTP header. Can be used, when authentication is performed by a reverse proxy  
See [reverse-proxy-auth](https://github.com/bugy/script-server/wiki/Authentication#reverse-proxy-auth) for details

_Type_: string  
_Required_: no  
_Example_: `"user_header_name": "X-Forwarded-User"`  

## `alerts`
Configuration section for alert notifications in case of script execution failure.  
Notifications contain script name, username, exit code, and an attached log file.  

_Type_: json object  
_Required_: no  

### - `destinations`
List of destinations, where an alert should be sent to. Multiple destinations can be configured at once, even for the same `type`.  

_Type_: json array
_Default_: []

### --- `type`
Type of destination. Supported values:  
**email**  
requires `smtplib` module  
Alert is sent as an email. The execution log file will be attached as a file  
  
**http**  
requires `requests` module  
The alert message is sent to an HTTP server via POST request. The alert message will be sent in the request body, under _message_ name.  
The execution log file will be attached as a multipart file with "log" name.  

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
A boolean flag specifying if authentication is required to send an email.  

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
Specifies, whether the server requires TLS layer  

_Default_: false (true for gmail server)  
_Required_: no  
_Type_: boolean  

### --- `url`
(http)
A URL where the alert notification will be sent to.  

_Required_: yes  
_Type_: string  
_Example_: `"url": "localhost:5000/test_alerts"`  


## `logging`
Section for logging configuration  

_Type_: json dict  
_Required_: no  

### - `execution_file`
Filename pattern for script execution logs.  
Each execution is always written to a separate file, so if the filename is not unique, it's prefixed with a random value.  
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
```json
{
  "port": 8080,
  "address": "192.168.0.1",
  "title": "My Script Server",
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
  },
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
