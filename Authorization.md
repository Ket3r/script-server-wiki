Authorization in the Script server can be divided in the following sections, depending on your purposes:
* [User/group identifiers](#user-group-identifiers)
* [Groups](#groups)
  * [Ldap Groups](#ldap-groups)
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


To be continued...