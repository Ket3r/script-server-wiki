Authorization in the Script server can be divided in the following sections, depending on your purposes:
* [User/group identifiers](#user-group-identifiers)
* [Groups](#groups)
  * [Ldap Groups](#--ldap-groups)
* [Server access](#server-access)
* [Scripts access](#scripts-access)
* [Admin users](#admin-users)  

Please refer to the corresponding section for the required information.  

In general there are no access restrictions in the Script server by default (except admin users). 

## User/group identifiers
When configuring any access from the section below, you should specify users and groups.  

User identifiers depend on your Authentication:
* LDAP: user name at login
* Google OAuth: email
* No Auth: token or IP ([[see details|Authentication#no-auth]])  

Group identifiers are group names, prefixed with @ sign, e.g. @devs.  

## Groups
Groups can be [[configured|Server-configuration#--groups]] via `groups` property. Each group has an unique name and a list of users (or other groups).  

Groups can be referenced by "@ + name" syntax (e.g. @devs). 

**@admin_users**  
There is an implicit group @admin_users, which is created based on `admin_users` configuration property.  

However if an explicit @admin_users group is defined, then `admin_users` property doesn't affect it.  

### Ldap Groups
If LDAP auth is enabled, then user groups can be fetched from it. These groups will combined with `groups`.  

`base_dn` configuration is required for groups lookup. In some cases `base_dn` can be deduced from `username_pattern`.  
Groups lookup is performed by _member_ and _memberUid_ group fields. Script server tries to get this user information from LDAP, based on `username_pattern`:
* _uid=$username,ou=users,dc=domain,dc=com_: searches exact dn
* _$username@<span></span>domain.com_: searches by userPrincipalName field
* _domain\\$username_: searches by sAMAccountName field  

If the user information is not found, then the full `username_pattern` will be treated as a user dn, and groups will be searched only by _member_ field.   

LDAP groups loading is performed only on the user login.   

## Server access
Global access of the users to the Script server can be configured via `allowed_users` [[property|Server-configuration#--allowed_users]].  
If a user is not in the `allowed_users` list, he won't be able to access anything at the script server (except login page).  

_Important note_: if a user is specified in `groups` or `admin_users`, he will be allowed automatically. So there is no need to add him to `allowed_users` explicitly.  

By default all the users are allowed.  
_Google OAuth_: it's mandatory to specify `allowed_users`, because otherwise any person with Google account would be able to access the Script server. If it's a desired effect, then any user (*) should be specified explicitly.  

Additionally, if auth is enabled, access to the server is possible only after performing authentication procedure.  

## Scripts access
Access to each script can be configured spearately via `allowed_users` [[config|Script-config#--allowed_users]].  
By default, users have access to any script.  

## Admin users
Script server has an admin page (/admin.html), who is accessible only by users in `admin_users` [[list|Server-configuration#--admin_users]].  

At the moment admin page provides only script logs. However in the future it would be possible to make configuration via it. So be careful, when setting `admin_users`.  

If auth is enabled, there are no default admin users.  
If auth is not enabled, by default admins are any connections from localhost.