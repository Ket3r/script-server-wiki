Authentication can be enabled in Script server via configuration (see [[auth|Server-configuration#auth]] configuration). At the moment the following auth providers are supported:
* [LDAP](#ldap)
* [Google OAuth](#google-oauth)  
* [Gitlab OAuth](#gitlab-oauth)  
* [.htpasswd file](#htpasswd-file)  
* [No Auth](#no-auth)
* [Reverse proxy auth](#reverse-proxy-auth)

If authentication is *not* enabled, please refer to [No Auth](#no-auth) section  

**Security note**: Script server does not and will not store any user credentials/tokens locally. However, some identification data is stored on users PC in encrypted form. Encryption key is available on server side only. 
User passwords or any securiry tokens are not stored anywhere.  

When authentication is enabled, users are required to login, before accessing the Script server.  

### Authentication purposes
Authentication is used in the Script server for the following purposes:
* auditing and logging user actions (executing scripts)
* controlling access to the Script server or/and to scripts
* allowing access to user's scripts executions
* allowing access to user's temp files  

---

## LDAP
_Requires: python ldap3 module_  

Script server can authenticate users against your LDAP server. Users would be prompted for username and login to check if the user have access to the LDAP server. Access is validated by a LDAP bind operation with simple authentication.  

After the authentication, user credentials are immediately discarded. User login will stay valid in Script server for 1 month (even if he looses access to the LDAP server).  

**username_pattern**  
Bind operation usually requires user's full distinguished name (dn). ActiveDirectory accepts some other username formats as well.  
However in most cases unique user names are too cumbersome for a user und simpler username is enough. In this case you can use a `username_pattern` with $username variable. Examples of most popular patterns:
* uid=$username,ou=users,dc=domain1,dc=domain2
* $username@<span></span>my-domain.com
* my-domain\$username  

User will have to specify only this simple $username on login, but the authentication will be performed with a full and unique name.  

**Groups**  
Script server can fetch user groups from LDAP. These groups can be later used by [[Authorization|Authorization]]. 
Groups loading is performed only during user login. After that they will stay cached and unchanged. So, if user needs to update his groups in Script server, he has to relogin.  

In order to load user groups `base_dn` property should be defined in the [[configuration|Server-configuration#--base_dn]]. Groups are always searched under the Base DN.  
`base_dn` can be automatically resolved for the following `username_pattern`: 
* uid=$username,ou=people,dc=domain1,dc=domain2,dc=domainN  
`base_dn`= "dc=domain1,dc=domain2,dc=domainN"
* username<span></span>@domain1.domain2.domainN  
`base_dn`= "dc=domain1,dc=domain2,dc=domainN"  


## Google OAuth
_Requires: connection to google.com and googleapis.com_  

Script server can authenticate users via [Google OAuth Service](https://developers.google.com/identity/protocols/OAuth2). `client_id` and `secret` properties should be acquired from Google and configured in the Script server.

### Instructions
- Go to [https://console.developers.google.com/apis/credentials](https://console.developers.google.com/apis/credentials). _(Select your existing project or you have to create new google project)_
- Click on **+ CREATE CREDENTIALS**, select **OAuth client ID**
- Select _Application type_ as **Web application**
- Add **Authorized redirect URIs**, URI should be like: **https://my-script-server.mydomain.com/login.html** or **https://mydomain.com/script-server/login.html**
- Save **Client ID** and **Client secret** into script-server's configuration file. Example:

```json
{
    "port": 5000,
    "address": "127.0.0.1",
    "title": "My Script Server",
    "auth": {
        "type": "google_oauth",
        "client_id": "COPY PASTE HERE",
        "secret": "COPY PASTE HERE"
    },
    "access": {
        "allowed_users": [ "myusername@gmail.com"],
        "admin_users": [ "myusername@gmail.com" ]
    }
}
```

Usernames of the users are their email addresses. These email adresses can and should be used when configuring user access rights.  
User emails are the only information which Script server uses from the Google OAuth Service.  

**allowed_users**  
Google OAuth doesn't perform any access control itself. So any user with google account would be able to access your instance of Script server. To prevent it, there is a [[configuration property|Server-configuration#--allowed_users]] `allowed_users`, which is required in case of Google OAuth. Simply put the emails of all allowed users there (or you can even put &ast; for any user).  

## Gitlab OAuth
Script server can authenticate users via Gitlab server, hosted or cloud. How to acquire client_id and secret you can find at https://docs.gitlab.com/ee/integration/oauth_provider.html. Script server requires `read_user` scope or `api` if you want group support.

Gitlab groups can be used in `allowed_users` and `admin_users`. Nested groups can be used using the full path (`@some/nested/group`)

Options:
* `type` (required) - should be `gitlab`
* `client_id` (required) - acquired gitlab client id
* `secret` (required) - acquired gitlab secret
* `url` - base url of Gitlab instance. Default - https://gitlab.com
* `auth_info_ttl` - time to live for info user cache. After expiry, script server will request GitLab for the updated information (e.g. read new groups). Default: 0 (never check GitLab again)
* `state_dump_file` - path to a dump file, where current authentication data is persisted. If not defined, then the data is not persisted between server restarts, thus all the users are logged out on a restart.
* `session_expire_minutes` - user inactivity time before logged out. Default: 0 (never expires)
* `group_support` - import user's Gitlab groups, require 'api' scope. Default: true
* `group_search` - search string used for filter groups, useful if users have too many groups because only 100 first returned. Default: do not filter

## .htpasswd file
If you would like to have a simple authentication, you can use a [.htpasswd file](https://httpd.apache.org/docs/2.4/programs/htpasswd.html)  
You need to configure a path to this file in Script server.  

### Requirements
Preferably, `htpasswd` utility should be installed on a machine. In this case Script server will delegate verification to this utility.  

Otherwise, as a fallback, Script server will verify user credentials itself. In this case only the encryption algorithms from this page are supported: https://httpd.apache.org/docs/2.4/misc/password_encryptions.html. Also, if bcrypt algorithm is used, it requires `bcrypt` python module

### Configuration
To enable htpasswd authentication, you need to set auth type and a path to the file, in the [server configuration](https://github.com/bugy/script-server/wiki/Server-configuration#auth)  
Example configuration:
```
"auth": {
    "type": "htpasswd",
    "htpasswd_path": "path/to/.htpasswd"
}
```

## No Auth
Script server needs to identify users even if authentication is not enabled. Identification is needed for the same [Authentication purposes](#authentication-purposes)  

There are 3 possible identificators in the Script server:  
* [random personal token](#personal-token) (default)  
* [IP](#ip) (can be configured)  
* [Reverse proxy auth](#reverse-proxy-auth) (can be configured)  

Access rights are configured based on these identificators.  

### personal token 
To my knowledge, there is no reliable way to identify anonymous users. Therefore on the initial connection, a user gets a unique personal token, which will stay valid for at least several days.  
The token is encrypted by the server and stored on the client-side.  

This token can be retrieved from the server logs or from an execution log file.  

This token can be used for access configuration. However, by its nature, this token is not the best option for configuration: it's hard to remember and find out and it's temporal. So if possible, it's always better to configure IP identification.  

### IP
Identifying users by IP has multiple problems:
* Users behind NAT/proxy share the same IP
* IP spoofing [(wikipedia)](https://en.wikipedia.org/wiki/IP_address_spoofing) 
* DHCP [(wikipedia)](https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol)  

But if you have trusted users and restricted network, you can add your users to `trusted_ips` list ([[see config|Server-configuration#--trusted_ips]]).  
After that, you would be able to configure users' access by their IP.  

**reverse proxy**  
If you are using a reverse proxy, real user IPs are not resolved by default. However, if you add the proxy's IP in the `trusted_ips` list, a real IP will be taken from X-Forwarded-For or X-Real-IP headers (proxy has to fill them).  

Please note, that this will cause all the users coming through a proxy to be trusted as well.  

## Reverse proxy auth
If you are using reverse proxy with authentication, you can pass authenticated user to Script server using HTTP header (for example X-Forwarded-User)  
And then you can enable this particular header in Script server for user identification. To do it, use [access.user_header_name](https://github.com/bugy/script-server/wiki/Server-configuration#--user_header_name) field  

Check [ticket #219](https://github.com/bugy/script-server/pull/219) for proxy configuration examples  