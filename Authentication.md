Authentication can be enabled in Script server via configuration (see [[auth|Server-configuration#auth]] configuration). At the moment the following auth providers are supported:
* [LDAP](#ldap)
* [Google OAuth](#google-oauth)  

If authentication is *not* enabled, please refer to [No Auth](#no-auth) section  

**Security note**: Script server does not and will not store any user credentials/tokens locally. However, some identification data is stored on users PC in encrypted form. Encryption key is available on server side only. 
User passwords or any securiry tokens are not stored anywhere.  

When authentication is enabled, users are required to login, before accessing the Script server.  

**Authentication purposes**
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

Usernames of the users are their email addresses. These email adresses can and should be used when configuring user access rights.  
User emails are the only information which Script server uses from the Google OAuth Service.  

**allowed_users**  
Google OAuth doesn't perform any access control itself. So any user with google account would be able to access your instance of Script server. To prevent it, there is a [[configuration property|Server-configuration#--allowed_users]] `allowed_users`, which is required in case of Google OAuth. Simply put the emails of all allowed users there (or you can even put &ast; for any user).  

## No Auth
Script server needs to identify users even if authentication is not enabled. Identification is needed for the same [Authentication purposes](#authentication-purposes)  

To be done...