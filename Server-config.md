You can edit conf/conf.json file, if you want to add some features to the server, of want to change default values. 

conf.json structure:
```javascript
{
  /**
    * Required: no
    * Description: custom listening address
    * Type: string
    * Default: 0.0.0.0 (any connection)
    */
  "address": "0.0.0.0",
  /**
    * Required: no
    * Description: custom port for running the web server
    * Type: number
    * Default: 5000 (5443 for ssl)
    */
  "port": 8080,
  /**
    * Required: no
    * Description: add ssl element to configure ssl, if needed
    * Type: object
    */
  "ssl": {
    /**
      * Required: yes
      * Description: the path to key file. Can be relative to script-server location
      * Type: string
      */
    "key_path": "testing/ssl/script-server.key",
    /**
      * Required: yes
      * Description: the path to cert file. Can be relative to script-server location
      * Type: string
      */
    "cert_path": "testing/ssl/script-server.crt"
  },
  /**
    * Required: no
    * Description: add user authentication to the server
    * Type: object
    */
  "auth": {
    /**
    * Required: yes
    * Description: type of authentication protocol. Supported types: ldap
    * ATTENTION: ldap auth requires python ldap3 module to be installed
    * Type: string
    */
    "type": "ldap",
    /**
    * Protocols: ldap
    * Required: yes
    * Description: url of the LDAP server
    * Type: string
    */
    "url": "ldap://127.0.0.1",
    /**
    * Protocols: ldap
    * Required: no
    * Default: $username
    * Description: pattern for username, which is sent to LDAP server. $username variable is filled with username from login screen
    * Type: string
    */
    "username_pattern": "uid=$username,ou=people,dc=test,dc=ldap",
    /**
    * Protocols: ldap
    * Required: no
    * Default: 3
    * Description: protocol version
    * Type: int
    */
    "version": 3
  },
  /**
    * Required: no
    * Description: alerts configuration, which is used when a script fails
    * Type: object
    */
  "alerts": {
    /**
    * Required: no
    * Description: destinations, to which an alert is sent
    * Type: array of objects
    */
    "destinations": [
      {
        /**
        * Required: yes
        * Description: type of destination. Supported types: 
        *    email: alert message is sent via email. Title and body are going to email title and body.
        *    http: alert message is sent to HTTP server via GET request. Title and body are concatenated together (separated by new line) and fill request body
        * 
        * ATTENTION: email requires smtplib python module to be installed
        * ATTENTION: http requires requests python module to be installed
        * Type: string
        */
        "type": "email",
        /**
        * Destinations: email
        * Required: yes
        * Description: sender of the letter
        * Type: string
        */
        "from": "buggygm@gmail.com",
        /**
        * Destinations: email
        * Required: yes
        * Description: semicolon-separated list of recipients
        * Type: string
        */
        "to": "buggygm@gmail.com",
        /**
        * Destinations: email
        * Required: yes
        * Description: which server should be used to send an email
        * Type: string
        */
        "server": "smtp.gmail.com",
        /**
        * Destinations: email
        * Required: no
        * Default: if auth is disabled, then empty, otherwise taken from "to" field
        * Description: username to authenticate in the server
        * Type: string
        */
        "login": "buggygm@gmail.com",
        /**
        * Destinations: email
        * Required: no
        * Default: empty
        * Description: password to authenticate in the server. Environment variables can be used, for this $$ prefix should be used
        * Type: string
        */
        "password": "$$EMAIL_PWD",
        /**
        * Destinations: email
        * Required: no
        * Default: true if login or password is specified, false otherwise.
        * Description: specifies, whether we should authenticate to the server before sending an email
        * Type: boolean
        */
        "auth_enabled": true,
        /**
        * Destinations: email
        * Required: no
        * Default: true if server is gmail, false otherwise
        * Description: specifies, whether server requires TLS layer enabled
        * Type: boolean
        */
        "tls": true,
        /**
        * Destinations: http
        * Required: yes
        * Description: url to which message will be sent   
        * Type: string
        */
        "url": "localhost:5000/test_alerts"
      }
    ]
  },
  /**
    * Required: no
    * Description: custom web page title
    * Type: string
    * Default: Script Server
    */
  "title": "My Script Server"
}
```
